<template>
  <section class="container">
    <visualization></visualization>
  </section>
</template>

<script>
import Visualization from '~/components/Visualization.vue'

export default {
  components: {
    Visualization
  }
}
</script>

<style>
.container {
  min-height: 100vh;
  display: flex;
  text-align: center;
}


</style>
