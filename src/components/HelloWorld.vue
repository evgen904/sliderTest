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
        @touchstart="touchStart($event)"
        @touchmove="touchMove($event)"
        @touchend="touchEnd($event)"
      >
        <div class="bg bg-0">
          {{ text }}
        </div>
      </div>
    </div>





    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
    <br><br><br><br><br><br>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      text: 'slider',
      touch: false,
      action: false,
      startX: 0,
      startY: 0,
      diffX: 0,
      diffY: 0,
      sortTimer: null,
      sort: false,
      swipe: false,
      scroll: false
    }
  },
  methods: {
    getCoord(e, c) {
      return /touch/.test(e.type) ? (e.originalEvent || e).changedTouches[0]['page' + c] : e['page' + c];
    },
    testTouch(e) {
      if (e.type == 'touchstart') {
        this.touch = true;
      } else if (this.touch) {
        this.touch = false;
        return false;
      }
      return true;
    },
    touchStart(ev) {
      if (this.testTouch(ev) && !this.action) {
        this.action = true;

        this.startX = this.getCoord(ev, 'X');
        this.startY = this.getCoord(ev, 'Y');
        this.diffX = 0;
        this.diffY = 0;
      }
    },
    touchMove(ev) {
      if (this.action) {
        let endX = this.getCoord(ev, 'X');
        let endY = this.getCoord(ev, 'Y');
        this.diffX = endX - this.startX;
        this.diffY = endY - this.startY;

        if (!this.sort && !this.swipe && !this.scroll) {
          if (Math.abs(this.diffY) > 10) { // It's a scroll
            this.scroll = true;
          } else if (Math.abs(this.diffX) > 7) { // It's a swipe
            this.swipe = true;
          }
        }

        if (this.swipe) {
          document.querySelector('body').classList.add('scroll-disabled')
          this.text = Math.random()
        }
      }
    },
    touchEnd() {
      if (this.action) {
        this.action = false;

        if (this.swipe) {
          console.log('end')
        }

        this.swipe = false;
        this.sort = false;
        this.scroll = false;

        document.querySelector('body').classList.remove('scroll-disabled')
      }
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
