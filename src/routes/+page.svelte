<script lang="ts">
  import { onMount } from 'svelte'
  import { Editor } from '$lib/index.js'

  let editorRef

  const content = {
    text: `{
  name: "Joe!!!"
  age: 42
  scores: [31.4,29.9,35.7]
  winner: false
}`
  }

  onMount(() => {
    const testEditor = new Editor({ target: editorRef, props: { content } })
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
