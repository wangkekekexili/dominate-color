<script>
  import { onMount } from "svelte";
  import {
    RGB,
    canvasImageDataToRGBArray,
    getAverageColor
  } from "./color.svelte";
  import DominatColorDiv from "./dominate-color-div.svelte";

  let input;
  let c;

  onMount(function() {
    input.addEventListener("change", function() {
      const image = input.files[0];
      const imageURL = URL.createObjectURL(image);

      const i = new Image();
      i.addEventListener("load", function() {
        c.width = i.width;
        c.height = i.height;
        const ctx = c.getContext("2d");
        ctx.drawImage(i, 0, 0);
        const averageColor = getAverageColor(
          canvasImageDataToRGBArray(
            ctx.getImageData(0, 0, i.width, i.height).data
          )
        );
        const body = document.querySelector("body");
        body.style.backgroundColor = `rgba(${averageColor.r},${averageColor.g},${averageColor.b},1)`;
      });
      i.src = imageURL;
    });
  });
</script>

<input bind:this={input} type="file" accept="image/*" />
<!-- <div style="height: 10px;"> -->
<!-- <canvas bind:this={c} /> -->
<!-- </div> -->
<DominatColorDiv backgroundColor={new RGB(0, 255, 100)} />
