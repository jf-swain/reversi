<template>
  <div
    class="element"
    :class="{'black': status.status === 1, 'white': status.status === 2}"
    @click="clickSquare"
    ></div>
</template>

<script>
export default {
  name: 'Square',
  props: {
    position: {
      type: Array,
    },
    status: {
      type: Object,
      default() {
        return {
          status: 0,
          position: [],
        };
      },
    },
  },
  methods: {
    clickSquare() {
      this.$emit('clickSquare', this.status);
    },
  },
};
</script>

<style scoped lang="scss">
.element {
  $width: 100%/8;

  background: #2aa066;
  border: 1px solid black;
  box-sizing: border-box;
  width: $width;

  &:after {
    content: '';
    display: block;
    margin-bottom: 100%;
  }

  &.black,
  &.white {
    &:before {
      border-radius: 50%;
      content: '';
      display: block;
      height: 75%;
      margin-left: auto;
      margin-right: auto;
      position: relative;
      top: 50%;
      transform: translateY(-50%);
      width: 75%;
    }
  }
  &.black {
    &::before {
      background: black;
      border: 2px solid white;
    }
  }
  &.white {
    &::before {
      background: white;
      border: 2px solid black;
    }
  }
}
</style>
