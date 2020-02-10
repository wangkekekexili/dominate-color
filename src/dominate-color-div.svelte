<script>
  import { onMount } from "svelte";
  import { pasteToClipboard } from "./paste-to-clipboard.svelte";

  export let backgroundColor;

  let div;
  let span;

  onMount(function() {
    div.style.backgroundColor = backgroundColor.toCSS();
    span.innerText = backgroundColor.toHex();
  });

  function pasteHexToClipboard() {
    pasteToClipboard(backgroundColor.toHex());
  }
</script>

<style>
  div {
    height: 25px;
    width: 240px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  span {
    font-family: monospace;
  }
</style>

<div
  bind:this={div}
  on:dblclick|preventDefault={pasteHexToClipboard}
  on:selectstart|preventDefault>
  <span bind:this={span} />
</div>
