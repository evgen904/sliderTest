<template>
  <div class="slider">
    <div ref="nav" class="slider-nav" :style="{ width: width }">
      <div v-if="slideWidth" ref="content" class="nav-content">
        <div
          v-for="(item, index) in media"
          :key="index"
          ref="slide"
          class="preview"
          :class="{ 'is-current': current == index }"
          :style="{ width: slideWidth + 'px' }"
          @click="clickSlide(index)"
        >
          <img
            class="nav-img"
            :src="item.image_url.replace('source', 'small')"
            :alt="item.comment || ''"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SliderLightDots',
  props: {
    media: {
      type: Array,
      default: []
    },
    width: {
      type: String,
    },
  },
  mounted() {
    this.slideWidth = this.$refs.nav.offsetWidth / 4
  },
  data() {
    return {
      current: 0,
      slideWidth: null,
    }
  },
  methods: {
    clickSlide(i) {
      this.current = i
      let translate;
      translate = -this.slideWidth * i;
      this.$refs.content.style = `transform: translate(${translate}px, 0);`;
    },
  }
}
</script>

<style lang="scss" scoped>
.slider {
  overflow: hidden;
  &-nav {
    margin-top: 3px;
    height: 90px;
    position: relative;
    width: calc(100% + 2px);
    margin-left: -1px;
    margin-right: -1px;
    .nav-img {
      object-fit: cover;
      width: 100%;
      height: 90px;
    }
    .nav-content {
      position: absolute;
      display: flex;
      height: 100%;
      transition: 700ms;
      .preview {
        box-sizing: border-box;
        cursor: pointer;
        padding-left: 1px;
        padding-right: 1px;
        .nav-img {
          outline: 2px solid transparent;
          outline-offset: -2px;
          transition: 500ms;
        }
        &.is-current {
          .nav-img {
            outline: 2px solid red;
          }
        }
        &.video {
          position: relative;
          &:after {
            content: '';
            display: block;
            cursor: pointer;
            position: absolute;
            top: 50%;
            left: 50%;
            background-image: url('https://cdnjs.cloudflare.com/ajax/libs/fotorama/4.5.2/fotorama.png');
            background-repeat: no-repeat;
            background-position: -4px -72px;
            z-index: 1;
            width: 32px;
            height: 32px;
            margin: -16px 0 0 -16px;
            background-position: -64px -32px;
          }
        }
      }
    }
  }
}
</style>
