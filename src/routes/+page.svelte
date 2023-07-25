<script lang="ts">
  import { onMount } from 'svelte'
  import { Editor } from '$lib/index.js'

  const useOnRenderMenu = false
  const showIsValidMessage = true

  let editorRef: HTMLDivElement

  const content = {
    text: `{
  name: "Joe!!!"
  age: 42
  scores: [31.4,29.9,35.7]
  winner: false
}`
  }

  onMount(() => {
    const testEditor = new Editor({
      target: editorRef,
      props: {
        content,
        showIsValidMessage,
        onRenderMenu: useOnRenderMenu
          ? (items) => items.slice(0, 1) // remove the "Open in full editor" button just to see that onRenderMenu works
          : undefined
      }
    })
  })
</script>

<h1>JSON Validator</h1>

<div bind:this={editorRef} class="json-validator" />

<style lang="scss">
  .json-validator {
    width: 1000px;
    max-width: 100%;
    height: 400px;
    display: flex;
  }
</style>
