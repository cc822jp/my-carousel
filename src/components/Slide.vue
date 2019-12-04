<template>
  <div class="slide">
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: 'slide'
};
</script>

<style scoped>
.slide {
  flex-basis: inherit;
  flex-grow: 0;
  flex-shrink: 0;
}
</style>
