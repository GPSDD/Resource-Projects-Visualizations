<template>
  <div v-if="loading" class="c-spinner">
  <div class="c-overlay" v-if="overlay"></div>
    <div class="spinner" v-if="loading">
      <div class="spinner-container">
        <div class="spinner-content">          
          <icon-component name="icon-loader" class="-large"></icon-component>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import IconComponent from '~/components/Icon.vue';

export default{
  name: 'spinner-component',
  props: {
    loading: {
      type: Boolean,
      default: false,
    },
    overlay: {
      type: Boolean,
      default: false,
    },
  },
  components: {
    IconComponent,
  },
};
</script>
<style lang="scss">
.c-spinner {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 60%;

  &.-absolute {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;

    .spinner {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      z-index: 4;
    }
  }

  &.-relative {
    > .spinner {
        margin: 0 auto;
    }
  }
}

.spinner {
  height: 70px;
  width: 70px;
  // background: rgba($grayed-black, 0.7);
  border-radius: 5px;
  display: flex;
  align-items: center;
  justify-content: center;

  .spinner-container {
    height: 50px;
    width: 50px;

    .spinner-content {
      height: 100%;
      width: 100%;
      display: flex;
      justify-content: center;
      align-items: center;

      animation-name: spin;
      animation-duration: 3000ms;
      animation-iteration-count: infinite;
      animation-timing-function: linear;

      svg {
        stroke: black;
      }

      @keyframes spin {
        from {
          transform: rotate(0deg);
        }
        to {
          transform: rotate(360deg);
        }
      }
    }
  }
}

.c-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 3;
  background-color: rgba(0, 0, 0, .4);
}

</style>
