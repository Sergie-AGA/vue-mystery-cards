<template>
  <section class="overlay-animation" @click="dismiss">
    <div class="overlay-animation__image-container">
      <div
        class="overlay-animation__image"
        :style="`background-image: url(${data.url})`"
      >
        <div class="overlay-animation__image-overlay"></div>
      </div>
    </div>
    <p class="overlay-animation__title">
      <span
        :class="`title-part title-part--${index}`"
        v-for="(part, index) in title"
        :key="index"
        >{{ part }}</span
      >
    </p>
    <div class="overlay-animation__white-overlay"></div>
  </section>
</template>

<script>
export default {
  name: "OverlayAnimation",
  props: {
    data: {
      type: Object,
      required: true,
    },
  },
  data: () => ({
    title: [],
  }),
  created() {
    let str = this.data.name;
    let parts = [];

    // Check if there are at least 3 spaces in the string
    let numSpaces = (str.match(/ /g) || []).length;
    if (numSpaces >= 3) {
      // Use spaces to split the string into 4 parts
      let spaceIndices = [];
      let currentSpaceIndex = str.indexOf(" ");
      while (currentSpaceIndex !== -1 && spaceIndices.length < 3) {
        spaceIndices.push(currentSpaceIndex);
        currentSpaceIndex = str.indexOf(" ", currentSpaceIndex + 1);
      }

      let partStart = 0;
      for (let i = 0; i < 3; i++) {
        let partEnd = spaceIndices[i];
        parts.push(str.substring(partStart, partEnd + 1)); // Include the space in the part
        partStart = partEnd + 1;
      }
      parts.push(str.substring(partStart)); // Add last part without space at the end
    } else {
      // Split the string into 4 equal parts
      let length = str.length;
      let partLength = Math.ceil(length / 4);

      for (let i = 0; i < 4; i++) {
        let partStart = i * partLength;
        let partEnd = partStart + partLength;
        if (partEnd > length) {
          partEnd = length;
        }
        parts.push(str.substring(partStart, partEnd));
      }
    }

    this.title = parts;
  },

  methods: {
    dismiss() {
      this.$emit("dismiss");
    },
  },
};
</script>

<style scoped lang="scss">
.overlay-animation {
  width: 100vw;
  height: 100vh;
  position: fixed;
  top: 0;
  left: 0;
  animation: section-animation 39s linear forwards;
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center;
  &__image-container {
    top: -30vh;
    left: 50%;
    transform: translate(-50%, -60%);
    position: absolute;
    height: 70%;
    width: 70%;
    border-radius: 4px;
    overflow: hidden;
    animation: container-animation 39s linear forwards;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  &__image {
    height: 100%;
    width: 43%;
    background-size: contain;
    background-repeat: no-repeat;
    background-position: center;
    /* opacity: 0; */
    position: relative;
  }
  &__image-overlay {
    position: absolute;
    top: 0;
    left: 50%;
    height: 100%;
    width: 100%;
    background-image: url("../assets/2754.png");
    background-color: goldenrod;
    background-size: contain;
    background-repeat: no-repeat;
    background-position: center;
    z-index: 9;
    transform: translateX(-50%);
    animation: overlay-animation 12.5s linear 12s forwards;
  }
  &__white-overlay {
    position: absolute;
    height: 100vh;
    width: 100vw;
    top: 0;
    left: 0;
    background: rgba($color: white, $alpha: 0);
    animation: white-overlay 39s linear forwards;
  }
  &__title {
    position: absolute;
    bottom: 10%;
    left: 50%;
    transform: translateX(-50%);
    background: rgba($color: black, $alpha: 0.7);
    padding: 10px 20px;
    border-radius: 4px;
    color: white;
    text-transform: uppercase;
    font-size: 30px;
    opacity: 0;
    animation: fade-in 2s linear 30s forwards;
  }
}

@keyframes container-animation {
  0% {
    top: -40vh;
  }
  14% {
    top: -50%;
    transform: translate(-50%, -60%) rotateY(0);
  }
  28% {
    top: 50%;
    transform: translate(-50%, -60%) rotateY(630deg);
  }
  31% {
    transform: translate(-50%, -60%) rotateY(720deg);
  }

  65% {
    transform: translate(-50%, -60%) rotateY(10800deg);
  }

  100% {
    top: 50%;
    transform: translate(-50%, -60%) rotateY(10800deg);
  }
}

@keyframes section-animation {
  0% {
    pointer-events: none;

    background-image: none;
  }
  67% {
    background-image: none;
    background-color: transparent;
  }
  68% {
    background-color: white;
    background-image: url("../assets/confetti.gif");
  }
  87% {
    background-image: url("../assets/confetti.gif");
  }
  88% {
    background-image: url("../assets/goldenlight.gif");
  }
  100% {
    background-color: white;
    background-image: url("../assets/goldenlight.gif");
    pointer-events: all;
  }
}

@keyframes white-overlay {
  0% {
    background: rgba($color: white, $alpha: 0);
  }

  31% {
    background: rgba($color: white, $alpha: 0);
  }
  63% {
    background: rgba($color: white, $alpha: 1);
  }
  67% {
    background: rgba($color: white, $alpha: 1);
  }
  78% {
    background: rgba($color: white, $alpha: 0);
  }
  100% {
    background: rgba($color: white, $alpha: 0);
  }
}

@keyframes overlay-animation {
  0% {
    background-color: goldenrod;
  } /* Gold */
  8.33% {
    background-color: #2ecc40;
  } /* Green */
  16.67% {
    background-color: #0074d9;
  } /* Blue */
  25% {
    background-color: #ffdc00;
  } /* Yellow */
  33.33% {
    background-color: #ff851b;
  } /* Orange */
  41.67% {
    background-color: #b10dc9;
  } /* Purple */
  50% {
    background-color: #f012be;
  } /* Magenta */
  58.33% {
    background-color: #7fdbff;
  } /* Cyan */
  66.67% {
    background-color: #01ff70;
  } /* Lime */
  75% {
    background-color: #85144b;
  } /* Maroon */
  83.33% {
    background-color: #3d9970;
  } /* Olive */
  91.67% {
    background-color: #ff4136;
  } /* Red */
  99% {
    background-color: goldenrod;
    background-image: url("../assets/2754.png");
  }
  100% {
    background-image: none;
    background-color: transparent;
  } /* Gold */
}

@keyframes fade-in {
  0% {
    opacity: 0;
  }

  100% {
    opacity: 1;
  }
}

.title-part {
  opacity: 0;
  &--0 {
    animation: fade-in 1s linear 30s forwards;
  }
  &--1 {
    animation: fade-in 1s linear 31s forwards;
  }
  &--2 {
    animation: fade-in 1s linear 31.7s forwards;
  }
  &--3 {
    animation: fade-in 1s linear 32.7s forwards;
  }
}
</style>
