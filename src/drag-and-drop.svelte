<script>
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher();

  let div;

  function ondragenter() {
    div.style.borderColor = "#333";
  }

  function ondrop(event) {
    div.style.borderColor = "#aaa";
    const image = event.dataTransfer.files[0];
    dispatch("imageDropped", { image });
  }
</script>

<style>
  div {
    border: 1px dashed #aaa;
    border-radius: 10px;
    text-align: center;
    color: #555;
    height: 75px;
    width: 60%;
    margin-left: auto;
    margin-right: auto;
    margin-top: 32px;
    margin-bottom: 16px;
  }
</style>

<div
  bind:this={div}
  on:dragenter|preventDefault={ondragenter}
  on:dragleave|preventDefault
  on:dragover|preventDefault
  on:drop|preventDefault={ondrop}>
  <p>Drop your image here.</p>
</div>
