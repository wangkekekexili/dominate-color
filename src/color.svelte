<script context="module">
  export function RGB(r, g, b) {
    this.r = r;
    this.g = g;
    this.b = b;
  }
  RGB.prototype.toCSS = function() {
    return `rgb(${this.r},${this.g},${this.b})`;
  };
  RGB.prototype.toHex = function() {
    let rStr = this.r.toString(16);
    if (rStr.length === 1) {
      rStr = "0" + rStr;
    }
    let gStr = this.g.toString(16);
    if (gStr.length === 1) {
      gStr = "0" + gStr;
    }
    let bStr = this.b.toString(16);
    if (bStr.length === 1) {
      bStr = "0" + gStr;
    }
    return `#${rStr}${gStr}${bStr}`;
  };
  RGB.prototype.equal = function(other) {
    return this.r === other.r && this.g === other.g && this.b === other.b;
  };
  RGB.prototype.distance = function(other) {
    return Math.sqrt(
      (this.r - other.r) * (this.r - other.r) +
        (this.g - other.g) * (this.g - other.g) +
        (this.b - other.b) * (this.b - other.b)
    );
  };

  export function canvasImageDataToRGBArray(data) {
    const rgbs = [];
    for (let i = 0; i < data.length; i += 4) {
      rgbs.push(new RGB(data[i], data[i + 1], data[i + 2]));
    }
    return rgbs;
  }

  export function getAverageColor(colors) {
    let totalR = 0;
    let totalG = 0;
    let totalB = 0;
    colors.forEach(color => {
      totalR += color.r;
      totalG += color.g;
      totalB += color.b;
    });
    return new RGB(
      Math.floor(totalR / colors.length),
      Math.floor(totalG / colors.length),
      Math.floor(totalB / colors.length)
    );
  }

  export function getDominateColors(colors) {
    // const bestOption = { totalStandardVariation: Infinity };
    return kmeans(colors);
  }

  function kmeans(colors) {
    const numColors = colors.length;
    let firstGroup = {
      avg: colors[Math.floor(Math.random() * numColors)],
      data: []
    };
    let secondGroup = {
      avg: colors[Math.floor(Math.random() * numColors)],
      data: []
    };
    let thirdGroup = {
      avg: colors[Math.floor(Math.random() * numColors)],
      data: []
    };

    while (true) {
      colors.forEach(color => {
        let dist1 = firstGroup.avg.distance(color);
        let dist2 = secondGroup.avg.distance(color);
        let dist3 = thirdGroup.avg.distance(color);
        if (dist1 <= dist2 && dist1 <= dist3) {
          firstGroup.data.push(color);
        }
        if (dist2 <= dist1 && dist2 <= dist3) {
          secondGroup.data.push(color);
        }
        if (dist3 <= dist1 && dist3 <= dist2) {
          thirdGroup.data.push(color);
        }
      });

      const firstGroupNext = getAverageColor(firstGroup.data);
      const secondGroupNext = getAverageColor(secondGroup.data);
      const thirdGroupNext = getAverageColor(thirdGroup.data);
      console.log(
        firstGroupNext.toHex(),
        secondGroupNext.toHex(),
        thirdGroupNext.toHex()
      );
      if (
        firstGroup.avg.equal(firstGroupNext) &&
        secondGroup.avg.equal(secondGroupNext) &&
        thirdGroup.avg.equal(thirdGroupNext)
      ) {
        return [firstGroupNext, secondGroupNext, thirdGroupNext];
      } else {
        firstGroup = {
          avg: firstGroupNext,
          data: []
        };
        secondGroup = {
          avg: secondGroupNext,
          data: []
        };
        thirdGroup = {
          avg: thirdGroupNext,
          data: []
        };
      }
    }
  }
</script>
