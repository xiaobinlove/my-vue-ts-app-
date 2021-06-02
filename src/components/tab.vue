<template>
  <div class="swiper-slide">
    <slot></slot>
  </div>
</template>
<script lang="ts">

export default {
  name: 'tab'
};
</script>
<style scoped>
.swiper-slide {
  flex-shrink: 0;
  width: 100%;
  height: 100%;
  position: relative;
  transition-property: transform;
}
</style>
