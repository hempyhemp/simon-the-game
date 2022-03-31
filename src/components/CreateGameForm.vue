<template>
  <div>
    <base-game-block
      class="blocksRoot"
      :blocks="[
        { value: 'red' },
        { value: 'green' },
        { value: 'blue' },
        { value: 'pink' },
      ]"
    />
  </div>
</template>

<script>
import BaseGameBlock from "@/components/BaseGameBlock.vue";

export default {
  name: "CreateGameForm",
  components: {
    BaseGameBlock,
  },
};
</script>

<style lang="scss">
@import "@/scss/variables";

.blocksRoot {
  height: $block-size * 2;
  width: $block-size * 2;
  margin: 0 auto;
  transform: rotate(45deg);
}
</style>
