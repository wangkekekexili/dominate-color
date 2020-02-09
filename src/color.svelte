<script context="module">
  export function canvasImageDataToRGBArray(data) {
    const rgbs = [];
    for (let i = 0; i < data.length; i += 4) {
      rgbs.push({
        r: data[i],
        g: data[i + 1],
        b: data[i + 2]
      });
    }
    return rgbs;
  }

  export function getAverageColor(colors) {
    let totalR = 0;
    let totalG = 0;
    let totalB = 0;
    colors.forEach(color => {
      totalR += color.r * color.r;
      totalG += color.g * color.g;
      totalB += color.b * color.b;
    });
    return {
      r: Math.floor(Math.sqrt(totalR / colors.length)),
      g: Math.floor(Math.sqrt(totalG / colors.length)),
      b: Math.floor(Math.sqrt(totalB / colors.length))
    };
  }
</script>
