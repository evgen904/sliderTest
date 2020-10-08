<template>
  <div class="home" style="width: 334px; height: 250px;">
    <HelloWorld :media="['1 slide', '2 slide', '3 slide', '4 slide', '5 slide', '6 slide']"/>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'Home',
  components: {
    HelloWorld
  }
}
</script>
