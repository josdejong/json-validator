<script lang="ts">
  import { type Content, JSONEditor, toTextContent } from 'svelte-jsoneditor'
  import { faArrowUpRightFromSquare, faCheck, faCopy } from '@fortawesome/free-solid-svg-icons'
  import { Icon } from 'svelte-awesome'

  export let content: Content = { text: '' }

  let copied = false
  let isValid = false // TODO: initialize correctly

  const copiedVisibleDuration = 700 // ms

  function handleCopy() {
    navigator.clipboard.writeText(toTextContent(content).text).catch((err) => console.error(err))

    copied = true
    setTimeout(() => (copied = false), copiedVisibleDuration)
  }

  function handleOpenExternal() {
    // TODO: throw a warning when text is too large to be copied via url (2000 chars?)
    window.open('https://jsoneditoronline.org/#left=json.' + encodeURI(toTextContent(content).text))
  }

  function createHandleRenderMenu(copied) {
    return function handleRenderMenu(items) {
      const formatButton = items.find((item) => item.title?.startsWith('Format'))

      if (!formatButton) {
        console.error('format button not found, cannot create custom menu')
        return items
      }

      return [
        {
          onClick: handleCopy,
          icon: faCopy,
          text: copied ? 'Copied!' : 'Copy',
          title: 'Copy the JSON data to clipboard',
          className: 'jse-beautify menu-button-copy'
        },
        {
          space: true
        },
        {
          onClick: handleOpenExternal,
          icon: faArrowUpRightFromSquare,
          text: 'Open in full editor',
          title:
            'Open the data in the full JSON Editor where you can sort, transform, query, compare and more',
          className: 'jse-beautify'
        }
      ]
    }
  }

  function handleChange(updatedContent, previousContent, { contentErrors }) {
    isValid = !contentErrors
  }
</script>

<div class="json-validator">
  <div class="json-validator-editor">
    <JSONEditor
      mode="text"
      statusBar={false}
      {content}
      onRenderMenu={createHandleRenderMenu(copied)}
      onChange={handleChange}
    />
  </div>
  {#if isValid}
    <div class="json-validator-status-valid">
      <Icon data={faCheck} /> JSON document is valid
    </div>
  {/if}
</div>

<style lang="scss">
  .json-validator {
    width: 100%;
    max-width: 800px;
    height: 300px;
    display: flex;
    flex-direction: column;

    .json-validator-editor {
      flex: 1;
      min-width: 0;
      min-height: 0;
    }
  }

  .json-validator-status-valid {
    font-family: var(--jse-font-family);
    font-size: var(--jse-font-size);
    background: var(--jse-message-success-background);
    color: var(--jse-message-success-color);
    padding: var(--jse-padding);
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
