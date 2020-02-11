<script>
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher();

  let div;
  let p;

  let dragStates = [];

  function ondragenter(event) {
    dragStates.push(event.target);
    div.style.borderColor = "#333";
    if (event.dataTransfer.items.length > 1) {
      p.innerText = "Only one image is allowed.";
    }
  }

  function ondragleave(event) {
    dragStates.pop();
    if (dragStates.length === 0) {
      div.style.borderColor = "#aaa";
    }
  }

  function ondrop(event) {
    dragStates = [];
    div.style.borderColor = "#aaa";
    if (event.dataTransfer.files.length > 1) {
      p.innerText = "Only one image is allowed.";
      return;
    }
    const file = event.dataTransfer.files[0];
    if (file.type.startsWith("image/")) {
      dispatch("imageDropped", { image: file });
      p.innerText = "Drop your image here.";
    } else {
      p.innerText = "Only an image is allowed.";
    }
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

  p {
    line-height: 1.5;
  }
</style>

<div
  bind:this={div}
  on:dragenter|preventDefault={ondragenter}
  on:dragleave|preventDefault={ondragleave}
  on:dragover|preventDefault
  on:drop|preventDefault={ondrop}>
  <p bind:this={p}>
    Drop your image here.
    <br />
    It may take up to 10 seconds.
  </p>
</div>
