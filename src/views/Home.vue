<template>
  <div class="home" style="width: 240px; height: 250px;">
    <!--<SliderLight :dots="true" :arrows="true" :media="media"/>-->
    <br>
    <div class="wrap-slider">
      <SliderNav :media="media2" />
    </div>


    <br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>
  </div>
</template>

<script>
// @ is an alias to /src
import SliderLight from '@/components/SliderLight.vue'
import SliderNav from '@/components/SliderNav.vue'

export default {
  name: 'Home',
  components: {
    SliderLight,
    SliderNav
  },
  data() {
    return {
      media: [
        "https://zastavok.net/main/priroda/159707928877.jpg",
        "https://zastavok.net/main/priroda/159710022741.jpg",
        "https://zastavok.net/main/priroda/159707616594.jpg",
        "https://zastavok.net/main/priroda/159707730813.jpg",
        "https://zastavok.net/main/priroda/159707623495.jpg",
        "https://zastavok.net/main/priroda/159708096261.jpg"
      ],
      media2: [
        {
          image_url: "https://dovga.com/images/photo/316-1280x1280.jpg",
          comment: "https://dovga.com/images/photo/316-1280x1280.jpg"
        },
        {
          image_url: "https://goodies.pcastuces.com/vignettes/9872_l.jpg",
          comment: "https://goodies.pcastuces.com/vignettes/9872_l.jpg"
        },
        {
          image_url: "https://goodies.pcastuces.com/vignettes/9872_l.jpg",
          comment: "https://goodies.pcastuces.com/vignettes/9872_l.jpg"
        },
        {
          image_url: "https://goodies.pcastuces.com/vignettes/9872_l.jpg",
          comment: "https://goodies.pcastuces.com/vignettes/9872_l.jpg"
        },
        {
          image_url: "https://goodies.pcastuces.com/vignettes/9872_l.jpg",
          comment: "https://goodies.pcastuces.com/vignettes/9872_l.jpg"
        },
        {
          image_url: "https://goodies.pcastuces.com/vignettes/9872_l.jpg",
          comment: "https://goodies.pcastuces.com/vignettes/9872_l.jpg"
        },
        {
          image_url: "https://goodies.pcastuces.com/vignettes/9872_l.jpg",
          comment: "https://goodies.pcastuces.com/vignettes/9872_l.jpg"
        }
      ]
    }
  }
}
</script>

<style lang="scss" scoped>
</style>
