<script lang="ts">
  import type {
    Content,
    MenuItem,
    MenuButton,
    OnChangeStatus,
    OnRenderMenu,
    RenderMenuContext
  } from 'svelte-jsoneditor'
  import { JSONEditor, Mode, toTextContent } from 'svelte-jsoneditor'
  import { faArrowUpRightFromSquare, faCheck, faCopy } from '@fortawesome/free-solid-svg-icons'
  import { Icon } from 'svelte-awesome'

  export let content: Content = { text: '' }
  export let onRenderMenu: OnRenderMenu | undefined = undefined
  export let showIsValidMessage = true

  let copied = false
  let isValid = false // TODO: initialize correctly

  const copiedVisibleDuration = 700 // ms

  export function get() {
    return content
  }

  function handleCopy() {
    navigator.clipboard.writeText(toTextContent(content).text).catch((err) => console.error(err))

    copied = true
    setTimeout(() => (copied = false), copiedVisibleDuration)
  }

  function handleOpenExternal() {
    // TODO: throw a warning when text is too large to be copied via url (2000 chars?)
    window.open('https://jsoneditoronline.org/#left=json.' + encodeURI(toTextContent(content).text))
  }

  function createHandleRenderMenu(copied: boolean, onRenderMenu: OnRenderMenu | undefined) {
    return function handleRenderMenu(items: MenuItem[], context: RenderMenuContext) {
      const formatButton = items.find((item) => (item as MenuButton).title?.startsWith('Format'))

      if (!formatButton) {
        console.error('format button not found, cannot create custom menu')
        return items
      }

      const customItems: MenuItem[] = [
        {
          type: 'button',
          onClick: handleCopy,
          icon: faCopy,
          text: copied ? 'Copied!' : 'Copy',
          title: 'Copy the JSON data to clipboard',
          className: 'jse-beautify menu-button-copy'
        },
        {
          type: 'space'
        },
        {
          type: 'button',
          onClick: handleOpenExternal,
          icon: faArrowUpRightFromSquare,
          text: 'Edit in JSON Editor Online',
          title:
            'Open the document in JSON Editor Online where you can sort, transform, query, compare and more',
          className: 'jse-beautify'
        }
      ]

      return onRenderMenu ? onRenderMenu(customItems, context) || customItems : customItems
    }
  }

  function handleChange(
    updatedContent: Content,
    previousContent: Content,
    { contentErrors }: OnChangeStatus
  ) {
    isValid = !contentErrors
  }
</script>

<div class="json-validator">
  <div class="json-validator-editor">
    <JSONEditor
      mode={Mode.text}
      statusBar={false}
      {content}
      onRenderMenu={createHandleRenderMenu(copied, onRenderMenu)}
      onChange={handleChange}
    />
  </div>
  {#if isValid && showIsValidMessage}
    <div class="json-validator-status-valid">
      <Icon data={faCheck} /> JSON document is valid
    </div>
  {/if}
</div>

<style lang="scss">
  .json-validator {
    flex: 1;
    display: flex;
    flex-direction: column;

    .json-validator-editor {
      flex: 1;
      min-width: 0;
      min-height: 0;
    }

    .json-validator-status-valid {
      font-family: var(--jse-font-family);
      font-size: var(--jse-font-size);
      background: var(--jse-message-success-background);
      color: var(--jse-message-success-color);
      padding: var(--jse-padding);
    }
  }

  :global(.jse-menu) {
    :global(.jse-button) {
      width: unset !important;
      height: unset !important;
      font-family: sans-serif !important;
      font-size: 12pt !important;
      padding: 10px !important;

      :global(.fa-icon) {
        vertical-align: -0.125em;
      }
    }
  }
</style>
