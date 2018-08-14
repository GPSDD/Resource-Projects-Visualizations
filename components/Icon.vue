<template>
  <div class="c-icon">
    <svg>
      <use :xlink:href="selector"></use>
    </svg>
  </div>
</template>
<script>
export default{
  name: 'icon-component',
  props: {
    name: {
      type: String,
      required: true
    }
  },
  computed: {
    selector() {
      return `#${this.name}`;
    }
  },
  data() {
    return {};
  },
};
</script>
<style lang="scss">
// Import global variables

.c-icon {
  flex-shrink: 0;

  > svg {
    fill: black;//$icon-color;
  }

  &.-round {
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 50%;
  }

  &.-mini {
    height: 6px;
    width: 6px;
    > svg {
      height: 6px;
      width: 6px;
    }
  }

  &.-xsmall {
    height: 12px;
    width: 12px;
    > svg {
      height: 12px;
      width: 12px;
    }
  }

  &.-small {
    width: 18px;
    height: 18px;
    > svg {
      height: 18px;
      width: 18px;
    }
  }

  &.-medium {
    height: 35px;
    width: 35px;
    > svg {
      height: 35px;
      width: 35px;
    }
  }

  &.-large {
    height: 60px;
    width: 60px;
    > svg {
      height: 60px;
      width: 60px;
    }
  }

  &.-base > svg {
    fill: black;
    stroke: black;
  }

}

</style>
