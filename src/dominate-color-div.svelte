<script>
  import { onMount } from "svelte";

  export let backgroundColor;

  let div;
  let span;

  onMount(function() {
    div.style.backgroundColor = backgroundColor.toCSS();
    span.innerText = backgroundColor.toHex();
  });

  function pasteHexToClipboard() {
    const tempTextInput = document.createElement("input");
    tempTextInput.type = "text";
    document.body.appendChild(tempTextInput);
    tempTextInput.value = backgroundColor.toHex();
    tempTextInput.select();
    document.execCommand("copy");
    document.body.removeChild(tempTextInput);
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
