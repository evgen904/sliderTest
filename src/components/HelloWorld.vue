<template>
  <div class="hello">
    <h1>Slider</h1>
    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
    <br><br><br><br><br><br>


    <div class="slider-new-wrap">
      <div
        ref="sliderNew"
        class="slider-new"
        @touchstart="touchStart"
        @touchmove="touchMove"
        @touchend="touchEnd"
      >
        <div v-for="(slide, i) in countSlider" :key="i">
          <div class="bg" :class="`bg-${i}`"></div>
        </div>
      </div>
      <div class="slider-count">{{ activeSlide }} / {{ countSlider }}</div>
    </div>





    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
  </div>
</template>

<script>
import { Swiper, SwiperSlide } from 'vue-awesome-swiper'
import 'swiper/swiper-bundle.css'

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      text: '',
      activeSlide: 1,
      countSlider: 4,
      sliderWidth: 0
    }
  },
  mounted() {
     this.sliderWidth = this.$refs.sliderNew.scrollWidth / this.countSlider
    document.querySelector('.slider-new').addEventListener('scroll', () => {
      console.log(this.$refs.sliderNew.scrollLeft)
      if(this.$refs.sliderNew.scrollLeft > 0) {
        this.$refs.sliderNew.scrollLeft = (0 + this.sliderWidth) * this.activeSlide;
      }
    })
  },
  components: {
    Swiper,
    SwiperSlide
  },
  methods: {
    touchStart() {
      //console.log('touchStart')
    },
    touchMove() {
      //console.log('touchMove')
    },
    touchEnd(event) {
      //console.log('touchEnd')
      this.activeSlide = Math.round(this.$refs.sliderNew.scrollLeft / this.sliderWidth) + 1



      let posTouchX = event.changedTouches[0].clientX - this.$refs.sliderNew.getBoundingClientRect().left
      let procX = Math.round(posTouchX / this.sliderWidth * 100)


      if (procX > 50) {
        //console.log('right')
      } else {
        //console.log('left')
      }

      // this.$refs.sliderNew.scrollLeft = this.sliderWidth * (this.activeSlide-1)
      // console.log(this.$refs.sliderNew.scrollLeft)



      // this.$refs.sliderNew.scrollBy({
      //   top: 0,
      //   left: 280,
      //   behavior: 'smooth'
      // })









    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.hello {
  h1 {
    color: #cc0000;
  }
}



.slider-new-wrap {
  position: relative;
  width: 280px;
  margin: 0 auto;

  .slider-count {
    position: absolute;
    bottom: 0;
    left: 0;
    background: #fff;
    color: #000000;
    font-size: 14px;
    padding: 12px;
    z-index: 100;
  }

}

.slider-new {
  display: flex;
  flex-wrap: nowrap;
  width: 100%;

  //scroll-snap-type: x mandatory;
  overflow-x: scroll;
  scroll-behavior: smooth;
  -ms-overflow-style: none;
  scrollbar-width: none;

  &::-webkit-scrollbar {
    width: 0px;
    background: transparent;
    display: none;
  }

  > div {
    min-width: 100%;
    height: 100%;
    flex-grow: 1;
    scroll-snap-align: start;
    img {
      object-fit: cover;
      width: 100%;
      height: 100%;
      pointer-events: unset;
    }
  }
}



  .bg {
    height: 200px;
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
