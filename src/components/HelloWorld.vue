<template>
  <div
    class="sc-sliderlight"
    :class="{ 'mobile': isMobile, 'shadow': isShadow }"
    @touchstart="swipeStart"
    @touchmove="swipeAction"
    @touchend="swipeEnd"
    @mousedown="swipeStart"
  >
    <div
      ref="slider"
      class="sc-sliderlight__track track"
    >
      <div v-for="(slide, index) in mediaView" class="track__item" :key="index">
        <img class="track__img" :src="slide" loading="lazy" alt="" @load="onLoad" @error="onError" />
        <div class="loading"></div>
      </div>
    </div>

    <div class="sc-sliderlight__arrows arrows" v-if="arrows">
      <button
        type="button"
        class="arrows__prev"
        :disabled="slideIndex === 0"
        @click.stop.prevent="prevSlide()"
      ></button>
      <button
        type="button"
        class="arrows__next"
        :disabled="slideIndex === media.length-1"
        @click.stop.prevent="nextSlide()"
      ></button>
    </div>

    <div class="sc-sliderlight__counter counter" v-if="isCounter">
      <div class="counter__text">
        <span>{{ slideIndex + 1 }}/{{ media.length }}</span>
      </div>
    </div>

    <div class="sc-sliderlight__dots dots" v-if="dots">
      <div
        class="dots__wrap"
        :class="{ compact: media.length < 5 }"
        :style="{ transform: transformDots }"
      >
        <div
          class="dot"
          :class="{current: slideIndex == i }"
          v-for="(dot, i) in media.length"
          :key="i"
        >
          <div class="dot__circle" :style="{ transform: `scale(${sizeDots[i]})` }"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// логика работы слайдера
// https://habr.com/ru/post/501258/

import noPhoto from '../assets/no_photo-fill.svg';

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
      transition: true,
      allowSwipe: true,
      isSwipe: false,
      isScroll: false,
      sizeDots: [],
      mediaView: [],
      countLoadMedia: 3,
      slideIndex: 0,
      slideWidth: 0,
      nextTrf: 0,
      prevTrf: 0,
      posInit: 0,
      posX1: 0,
      posX2: 0,
      posY1: 0,
      posY2: 0,
      posFinal: 0,
    }
  },
  mounted() {
    for(let i = 0; i < this.countLoadMedia; i++) {
      if(this.media[i])
        this.mediaView.push(this.media[i])
    }
    this.$refs.slider.style.transform = 'translate3d(0px, 0px, 0px)';
    this.slideWidth = this.$refs.slider.clientWidth;
    this.sizeDots = this.scales(Object.keys(this.media), this.slideIndex);
  },
  watch: {
    slideIndex(val) {
      this.sizeDots = this.scales(Object.keys(this.media), val);

      this.$emit("currentSlide", val);
      if (val == this.media.length - 1) {
        this.$emit("lastSlide", val);
      }
    }
  },
  computed: {
    lastTrf() {
      return this.media.length * this.slideWidth
    },
    posThreshold() {
      return this.slideWidth * 0.1
    },
    transformDots() {
      let translate;
      if (this.media.length <= 5) {
        translate = 0;
      } else {
        if (this.slideIndex <= 2) {
          translate = this.slideIndex ? `0px` : 0;
        } else if (this.slideIndex >= this.media.length - 3) {
          translate = this.slideIndex
            ? `${-1 * (this.media.length - 5)}0px`
            : 0;
        } else if (this.slideIndex > 2) {
          translate = this.slideIndex
            ? `${-1 * (this.slideIndex - 2)}0px`
            : 0;
        }
      }
      return `translate(${translate}, 0)`;
    },
  },
  methods: {
    onError(e) {
      e.target.classList.add('no-photo')
      e.target.src = noPhoto
    },
    onLoad(e) {
      const parent = e.currentTarget.parentElement;
      const loading = parent.querySelector('.loading')
      parent.removeChild(loading)
    },
    scales(indexArray, currentIndex) {
      const scaleArray = indexArray.map(v =>
        Math.abs(v - currentIndex) > 5
          ? 0
          : 0.4 + (Math.abs(Math.abs(v - currentIndex) - 5) / 5) * 0.6
      );
      return scaleArray;
    },
    pushImage() {
      const indexLoadImage = this.mediaView.length;

      if(!this.mediaView[indexLoadImage] && this.media.length !== this.mediaView.length) {
        this.mediaView.push(this.media[indexLoadImage])
      }
    },
    prevSlide() {
      if (this.slideIndex > 0) {
        this.pushImage()
        this.slideIndex--
        this.slide()
      }
    },
    nextSlide() {
      if (this.slideIndex < this.media.length - 1) {
        this.pushImage()
        this.slideIndex++
        this.slide()
      }
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

      document.addEventListener('mousemove', this.swipeAction);
      document.addEventListener('mouseup', this.swipeEnd);

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
        this.pushImage()
        event.preventDefault()
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

      document.removeEventListener('mousemove', this.swipeAction);
      document.removeEventListener('mouseup', this.swipeEnd);
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

<style scoped lang="scss">
.sc-sliderlight {
  position: relative;
  width: 100%;
  height: 100%;
  background: #fff;
  user-select: none;
  touch-action: pan-y;
  overflow: hidden;
  &.shadow {
    &:before {
      content: "";
      display: block;

      pointer-events: none;
      position: absolute;
      left: 0;
      right: 0;
      top: 0;
      bottom: 0;

      opacity: 0.35;
      z-index: 1;

      background: linear-gradient(
        0deg,
        rgba(0, 0, 0, 0.0001) 75%,
        #000000 100%
      );
    }
  }
  &:hover {
    .sc-sliderlight__arrows {
      opacity: 1;
    }
    .arrows {
      &__prev,
      &__next {
        background: rgba(255, 255, 255, 0.8);
        &:hover:not(.deactive) {
          background: rgba(255, 255, 255, 1);
        }
      }
    }
  }

  &__track {
    display: flex;
    flex-wrap: nowrap;
    height: 100%;
    //transition: transform 0.5s;

    .track {
      &__item {
        min-width: 100%;
        height: 100%;
        flex-grow: 1;
      }
      &__img {
        object-fit: cover;
        width: 100%;
        height: 100%;
        pointer-events: none;
      }
    }
  }

  &__dots {
    position: absolute;
    bottom: 10px;
    left: 50%;
    z-index: 2;
    transform: translate(-50%, 0);

    &.dots {
      width: 50px;
      overflow: hidden;

      .dots {
        &__wrap {
          display: flex;
          // justify-content: space-between;
          align-items: center;
          width: 100%;
          transition: transform 0.3s;

          &.compact {
            justify-content: center;
          }
        }
      }

      .dot {
        width: 10px;
        min-width: 10px;
        height: 10px;
        display: flex;
        align-items: center;
        justify-content: center;

        &__circle {
          width: 6px;
          height: 6px;
          background: #fff;
          opacity: 0.7;
          border-radius: 50%;
          // transition: width 0.3s, height 0.3s, opacity 0.3s;
          transition: transform 0.3s, opacity 0.3s;
        }

        &.active {
          .dot__circle {
            width: 6px;
            height: 6px;
          }
        }
        &.current {
          .dot__circle {
            opacity: 0.9;
          }
        }
      }
    }
  }

  &__counter {
    position: absolute;
    bottom: 10px;
    left: 50%;
    z-index: 2;
    transform: translate(-50%, 0);

    display: flex;
    justify-content: center;
    align-items: center;

    border-radius: 20px;
    background: rgba(0, 0, 0, 0.5);
    min-width: 40px;
    height: 20px;
    padding: 2px 10px;

    .counter {
      &__text {
        font-size: 13px;
        line-height: 17px;
        color: #fff;
      }
    }
  }

  &__arrows {
    opacity: 0;
    transition: opacity 0.3s;
    .arrows {
      &__prev {
        left: 10px;
        &:before {
          transform: rotate(180deg);
        }
      }
      &__next {
        right: 10px;
      }
      &__prev,
      &__next {
        outline-style: none;
        border: none;
        cursor: pointer;

        position: absolute;
        top: 50%;
        transform: translate(0, -50%);

        display: flex;
        justify-content: center;
        align-items: center;

        width: 40px;
        height: 40px;
        border-radius: 50%;
        background: rgba(255, 255, 255, 0.7);
        transition: background 0.3s, opacity 0.3s;

        &:before {
          content: "";
          display: block;
          background: url("../assets/arrow.svg") center center no-repeat;
          background-size: contain;
          width: 8px;
          height: 10px;
        }

        &:disabled {
          opacity: 0.5;
        }
      }
    }
  }
}
</style>
