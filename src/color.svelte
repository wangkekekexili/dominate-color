<script context="module">
  export function RGB(r, g, b) {
    this.r = r;
    this.g = g;
    this.b = b;
    Object.defineProperty(this, "luminance", {
      // https://www.w3.org/TR/AERT/#color-contrast
      get() {
        return 0.299 * this.r + 0.587 * this.g + 0.114 * this.b;
      }
    });
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
      bStr = "0" + bStr;
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

  function getStandardDeviation(colors) {
    const numColors = colors.length;

    let totalR = 0;
    let totalG = 0;
    let totalB = 0;
    colors.forEach(color => {
      totalR += color.r;
      totalG += color.g;
      totalB += color.b;
    });
    let avgR = totalR / numColors;
    let avgG = totalG / numColors;
    let avgB = totalB / numColors;

    let tempStdR = 0;
    let tempStdG = 0;
    let tempStdB = 0;
    colors.forEach(color => {
      tempStdR += (color.r - avgR) * (color.r - avgR);
      tempStdG += (color.g - avgG) * (color.g - avgG);
      tempStdB += (color.b - avgB) * (color.b - avgB);
    });
    let varR = tempStdR / numColors;
    let varG = tempStdG / numColors;
    let varB = tempStdB / numColors;

    return (1 / 3) * (varR + varG + varB);
  }

  export function getDominateColors(colors) {
    const bestOption = { totalStd: Infinity, groups: [] };
    for (let i = 0; i != 5; i++) {
      const groups = kmeans(colors);
      const currentTotalStd = groups[0].std + groups[1].std + groups[2].std;
      if (currentTotalStd < bestOption.totalStd) {
        bestOption.groups = [groups[0].avg, groups[1].avg, groups[2].avg];
        bestOption.totalStd = currentTotalStd;
      }
    }
    return bestOption.groups;
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
        if (dist1 < dist2 && dist1 < dist3) {
          firstGroup.data.push(color);
          return;
        }
        if (dist2 < dist1 && dist2 < dist3) {
          secondGroup.data.push(color);
          return;
        }
        if (dist3 < dist1 && dist3 < dist2) {
          thirdGroup.data.push(color);
          return;
        }
        if (dist1 === dist2 && dist1 === dist3) {
          const r = Math.floor(Math.random() * 3);
          if (r === 0) {
            firstGroup.data.push(color);
          } else if (r === 1) {
            secondGroup.data.push(color);
          } else {
            thirdGroup.data.push(color);
          }
        } else if (dist1 === dist2) {
          const r = Math.floor(Math.random() * 2);
          if (r === 0) {
            firstGroup.data.push(color);
          } else {
            secondGroup.data.push(color);
          }
        } else if (dist1 === dist3) {
          const r = Math.floor(Math.random() * 2);
          if (r === 0) {
            firstGroup.data.push(color);
          } else {
            thirdGroup.data.push(color);
          }
        } else {
          // dist2 === dist3
          const r = Math.floor(Math.random() * 2);
          if (r === 0) {
            secondGroup.data.push(color);
          } else {
            thirdGroup.data.push(color);
          }
        }
      });

      const firstGroupNext = getAverageColor(firstGroup.data);
      const secondGroupNext = getAverageColor(secondGroup.data);
      const thirdGroupNext = getAverageColor(thirdGroup.data);
      if (
        firstGroup.avg.equal(firstGroupNext) &&
        secondGroup.avg.equal(secondGroupNext) &&
        thirdGroup.avg.equal(thirdGroupNext)
      ) {
        firstGroup.std = getStandardDeviation(firstGroup.data);
        secondGroup.std = getStandardDeviation(secondGroup.data);
        thirdGroup.std = getStandardDeviation(thirdGroup.data);
        return [firstGroup, secondGroup, thirdGroup];
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
