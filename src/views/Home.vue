<template>
  <div class="home" style="width: 334px; height: 250px;">

    <br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>


    <HelloWorld :dots="true" :arrows="true" :media="media"/>



    <br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'Home',
  components: {
    HelloWorld
  },
  data() {
    return {
      media: [
        "https://zastavok.net/main/priroda/159707616594.jpg",
        "https://zastavok.net/main/priroda/159707730813.jpg",
        "https://zastavok.net/main/priroda/159710022741.jpg",
        "https://zastavok.net/main/priroda/159707928877.jpg",
        "https://zastavok.net/main/priroda/159707623495.jpg",
        "https://zastavok.net/main/priroda/159708096261.jpg"
      ]
    }
  }
}
</script>
