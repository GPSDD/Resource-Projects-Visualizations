<template>
  <section class="container">
    <visualization></visualization>
    <svg aria-hidden="true" style="position: absolute; width: 0; height: 0; overflow: hidden;" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
      <defs>
            <symbol id="icon-loader" viewBox="0 0 32 32">
      <title>loader</title>
      <path stroke-width="2.6667" stroke-miterlimit="4" stroke-linecap="round" stroke-linejoin="round" d="M16 2.667v5.333"></path>
      <path stroke-width="2.6667" stroke-miterlimit="4" stroke-linecap="round" stroke-linejoin="round" d="M16 24v5.333"></path>
      <path stroke-width="2.6667" stroke-miterlimit="4" stroke-linecap="round" stroke-linejoin="round" d="M6.573 6.573l3.773 3.773"></path>
      <path stroke-width="2.6667" stroke-miterlimit="4" stroke-linecap="round" stroke-linejoin="round" d="M21.653 21.653l3.773 3.773"></path>
      <path stroke-width="2.6667" stroke-miterlimit="4" stroke-linecap="round" stroke-linejoin="round" d="M2.667 16h5.333"></path>
      <path stroke-width="2.6667" stroke-miterlimit="4" stroke-linecap="round" stroke-linejoin="round" d="M24 16h5.333"></path>
      <path stroke-width="2.6667" stroke-miterlimit="4" stroke-linecap="round" stroke-linejoin="round" d="M6.573 25.427l3.773-3.773"></path>
      <path stroke-width="2.6667" stroke-miterlimit="4" stroke-linecap="round" stroke-linejoin="round" d="M21.653 10.347l3.773-3.773"></path>
      <animateTransform attributeType="xml"
                        attributeName="transform"
                        type="rotate"
                        from="0 25 25"
                        to="360 25 25"
                        dur="0.6s"
                        repeatCount="indefinite"/>
    </symbol>
      </defs>
    </svg>
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
