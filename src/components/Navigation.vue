<template>
  <div class="navigation">
    <div
      class="button button--prev"
      v-on:click.prevent="handleNavigationPrev"
    ></div>
    <div
      class="button button--next"
      v-on:click.prevent="handleNavigationNext"
    ></div>
  </div>
</template>

<script>
export default {
  name: 'navigation',
  methods: {
    handleNavigationPrev() {
      this.$emit('navigation-click-prev');
    },
    handleNavigationNext() {
      this.$emit('navigation-click-next');
    }
  }
};
</script>

<style scoped>
.button {
  position: absolute;
  cursor: pointer;
  top: 0;
  bottom: 50px;
  width: 32px;
  height: 32px;
  margin: auto;
  border-radius: 50%;
  background-color: rgba(0, 0, 0, 0.2);
  display: flex;
  justify-content: center;
  align-items: center;
}

.button:after {
  content: '';
  width: 0;
  height: 0;
  border-style: solid;
}

.button--prev {
  left: 4px;
}

.button--next {
  right: 4px;
}

.button.button--prev:after {
  margin-left: -2px;
  border-width: 8px 8px 8px 0;
  border-color: transparent #fff transparent transparent;
}

.button.button--next:after {
  margin-right: -2px;
  border-width: 8px 0 8px 10px;
  border-color: transparent transparent transparent #fff;
}
</style>
