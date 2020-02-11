<script>
  import {
    RGB,
    canvasImageDataToRGBArray,
    getDominateColors
  } from "./color.svelte";
  import DominatColorDiv from "./dominate-color-div.svelte";
  import DragAndDrop from "./drag-and-drop.svelte";

  let displayImage;
  let dominateColorsDiv;

  let first;
  let second;
  let third;

  function onImageDropped(event) {
    displayImage.parentNode.style.display = "block";

    const image = event.detail.image;
    const imageURL = URL.createObjectURL(image);
    displayImage.src = imageURL;

    const tempImg = new Image();
    tempImg.addEventListener("load", function() {
      const c = document.createElement("canvas");

      let scale = 1;
      if (tempImg.width > 512) {
        scale = 512 / tempImg.width;
      }
      c.width = Math.floor(tempImg.width * scale);
      c.height = Math.floor(tempImg.height * scale);
      const ctx = c.getContext("2d");
      ctx.scale(scale, scale);
      ctx.drawImage(tempImg, 0, 0);
      [first, second, third] = getDominateColors(
        canvasImageDataToRGBArray(
          ctx.getImageData(0, 0, c.width, c.height).data
        )
      ).sort((a, b) => {
        if (a.r !== b.r) {
          return a.r - b.r;
        }
        if (a.g !== b.g) {
          return a.g - b.g;
        }
        return a.b - b.b;
      });
      dominateColorsDiv.style.display = "block";
    });
    tempImg.src = imageURL;
  }
</script>

<style>
  .display-image {
    width: 60%;
    margin-left: auto;
    margin-right: auto;
    margin-top: 16px;
    margin-bottom: 16px;
    display: none;
  }

  .display-image img {
    width: 100%;
  }

  .dominate-colors {
    width: 60%;
    margin-left: auto;
    margin-right: auto;
    display: none;
  }

  .dominate-colors div {
    display: flex;
    justify-content: center;
    margin-top: 8px;
  }
</style>

<svelte:body
  on:dragenter|preventDefault
  on:dragleave|preventDefault
  on:dragover|preventDefault
  on:drop|preventDefault />

<DragAndDrop on:imageDropped={onImageDropped} />
<div class="display-image">
  <img bind:this={displayImage} />
</div>
<div class="dominate-colors" bind:this={dominateColorsDiv}>
  <div class="dominate-colors-first">
    <DominatColorDiv backgroundColor={first} />
  </div>
  <div class="dominate-colors-second">
    <DominatColorDiv backgroundColor={second} />
  </div>
  <div class="dominate-colors-third">
    <DominatColorDiv backgroundColor={third} />
  </div>
</div>
