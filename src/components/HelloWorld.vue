<template>
  <div class="hello">
    <h1>Slider</h1>
    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
    <br><br><br><br><br><br>


    <div
      class="slider"
      @touchstart="swipeStart"
      @touchmove="swipeAction"
      @touchend="swipeEnd"
    >
      <div class="slider-list">
        <div
          ref="slider"
          class="slider-track"
        >
          <div v-for="(item, index) in media" class="slide bg" :class="`bg-${index}`" :key="index">
            {{ item }}
          </div>
        </div>
      </div>
      <div class="slider-arrows">
        <button type="button" class="prev" :disabled="slideIndex === 0" @click="prevSlide">&larr;</button>
        <button type="button" class="next" :disabled="slideIndex === media.length-1" @click="nextSlide">&rarr;</button>
      </div>
    </div>

    <div>{{ text }}</div>




    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
  </div>
</template>

<script>
export default {
  name: 'SliderLight',
  props: {
    media: {
      type: Array,
      default: []
    },
    isCounter: {
      type: Boolean,
      default: false
    },
    isMobile: {
      type: Boolean,
      default: false
    },
    isShadow: {
      type: Boolean,
      default: true
    },
    dots: {
      type: Boolean,
      default: false
    },
    arrows: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      text: '123',

      transition: true,
      slideIndex: 0,
      slideWidth: 0,
      allowSwipe: true,
      nextTrf: 0,
      prevTrf: 0,
      posInit: 0,
      posX1: 0,
      posX2: 0,
      posY1: 0,
      posY2: 0,
      posFinal: 0,
      isSwipe: false,
      isScroll: false,
    }
  },
  mounted() {
    this.$refs.slider.style.transform = 'translate3d(0px, 0px, 0px)';
    this.slideWidth = this.$refs.slider.clientWidth;
  },
  computed: {
    lastTrf() {
      return this.media.length * this.slideWidth
    },
    posThreshold() {
      return this.slideWidth * 0.1
    }
  },
  methods: {
    prevSlide() {
      this.slideIndex--
      this.slide()
    },
    nextSlide() {
      this.slideIndex++
      this.slide()
    },
    getEvent() {
      return (event.type.search('touch') !== -1) ? event.touches[0] : event;
    },
    slide() {
      if (this.transition) {
        this.$refs.slider.style.transition = 'transform .5s';
      }
      this.$refs.slider.style.transform = `translate3d(-${this.slideIndex * this.slideWidth}px, 0px, 0px)`;
    },
    swipeStart() {
      let evt = this.getEvent();

      if (this.allowSwipe) {
        this.transition = true;

        this.nextTrf = (this.slideIndex + 1) * -this.slideWidth;
        this.prevTrf = (this.slideIndex - 1) * -this.slideWidth;

        this.posInit = this.posX1 = evt.clientX;
        this.posY1 = evt.clientY;

        this.$refs.slider.style.transition = '';
      }
    },
    swipeAction() {
      let evt = this.getEvent(),
        style = this.$refs.slider.style.transform,
        transform = +style.match(/([-0-9.]+(?=px))/)[0];

      this.posX2 = this.posX1 - evt.clientX;
      this.posX1 = evt.clientX;

      this.posY2 = this.posY1 - evt.clientY;
      this.posY1 = evt.clientY;

      if (!this.isSwipe && !this.isScroll) {
        let posY = Math.abs(this.posY2);
        if (posY > 7 || this.posX2 === 0) {
          this.isScroll = true;
          this.allowSwipe = false;
        } else if (posY < 7) {
          this.isSwipe = true;
        }
      }

      if (this.isSwipe) {
        event.preventDefault()
        this.text = Math.random()
        if (this.slideIndex === 0) {
          if (this.posInit < this.posX1) {
            this.setTransform(transform, 0);
            return;
          } else {
            this.allowSwipe = true;
          }
        }

        // запрет ухода вправо на последнем слайде
        if (this.slideIndex === this.media.length-1) {
          if (this.posInit > this.posX1) {
            this.setTransform(transform, this.lastTrf);
            return;
          } else {
            this.allowSwipe = true;
          }
        }

        if (this.posInit > this.posX1 && transform < this.nextTrf || this.posInit < this.posX1 && transform > this.prevTrf) {
          this.reachEdge();
          return;
        }

        this.$refs.slider.style.transform = `translate3d(${transform - this.posX2}px, 0px, 0px)`;
      }
    },
    swipeEnd() {
      this.posFinal = this.posInit - this.posX1;

      this.isScroll = false;
      this.isSwipe = false;

      if (this.allowSwipe) {
        if (Math.abs(this.posFinal) > this.posThreshold) {
          if (this.posInit < this.posX1) {
            this.slideIndex--;
          } else if (this.posInit > this.posX1) {
            this.slideIndex++;
          }
        }

        if (this.posInit !== this.posX1) {
          //this.allowSwipe = false;
          this.slide();
        } else {
          this.allowSwipe = true;
        }

      } else {
        this.allowSwipe = true;
      }
    },
    setTransform(transform, comapreTransform) {
      if (transform >= comapreTransform) {
        if (transform > comapreTransform) {
          this.$refs.slider.style.transform = `translate3d(${comapreTransform}px, 0px, 0px)`;
        }
      }
      this.allowSwipe = false;
    },
    reachEdge() {
      this.transition = false;
      this.swipeEnd();
      this.allowSwipe = true;
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.slider {
  position: relative;
  width: 100%;
  margin: 50px auto 0;
  user-select: none;
  touch-action: pan-y;
  .slider-list {
    width: 100%;
    overflow: hidden;
    .slider-track {
      display: flex;
      .slide {
        min-width: 100%;
        height: 200px;
        flex-grow: 1;
        flex-shrink: 0;
        display: flex;
        align-items: center;
        justify-content: center;
        img {
          poiner-events: none;
        }
      }
    }
  }
}



.bg {
  height: 200px !important;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
  font-weight: bold;
  &.bg-0 {
    background: #f7d3d3;
  }
  &.bg-1 {
    background: #d3f7e3;
  }
  &.bg-2 {
    background: #eed3f7;
  }
  &.bg-3 {
    background: #d3ebf7;
  }
  &.bg-4 {
    background: #f3d3f7;
  }
  &.bg-5 {
    background: #d3def7;
  }
}
</style>
