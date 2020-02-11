<script>
  import { pasteToClipboard } from "./paste-to-clipboard.svelte";
  import { RGB } from "./color.svelte";

  export let backgroundColor = new RGB(0, 0, 0);
  let fontColor = "#222";
  $: {
    debugger;
    if (backgroundColor.luminance > 75) {
      fontColor = "#222";
    } else {
      fontColor = "#ccc";
    }
  }

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
    mix-blend-mode: normal;
  }
</style>

<div
  style="background-color: {backgroundColor.toCSS()}"
  on:dblclick|preventDefault={pasteHexToClipboard}
  on:selectstart|preventDefault>
  <span style="color:{fontColor}">{backgroundColor.toHex()}</span>
</div>
