<template>
  <div
    class="block"
    :class="{
      active: active,
      blockType0: blockType == 0,
      blockType1: blockType == 1,
      blockType2: blockType == 2,
      blockType3: blockType == 3,
    }"
  ></div>
</template>

<script>
export default {
  name: "BaseGameBlock",
  props: {
    active: {
      type: Boolean,
    },
    "block-type": {
      type: Number,
    },
  },
  //   data() {
  //     return {
  //       active: false,
  //     };
  //   },
};
</script>

<style lang="scss">
@import "@/scss/variables";
.block {
  width: $block-size;
  height: $block-size;
  border: 1px solid black;
  cursor: pointer;

  border-radius: 4px;

  &.blockType0 {
    background-color: $bc1;
    &.active {
      background-color: $bc1-active;
    }
  }
  &.blockType1 {
    background-color: $bc2;
    &.active {
      background-color: $bc2-active;
    }
  }
  &.blockType2 {
    background-color: $bc3;
    &.active {
      background-color: $bc3-active;
    }
  }
  &.blockType3 {
    background-color: $bc4;
    &.active {
      background-color: $bc4-active;
    }
  }
}
</style>
