<template>
  <div class="pagination">
    <div class="dot-container">
      <div
        v-for="(page, index) in count"
        :key="`${page}_${index}`"
        :class="{
          'dot': true,
          'dot--active': isCurrentDot(index)
        }"
        v-on:click="handleClick(index)"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'pagination',
  props: {
    count: {
      type: Number,
      default: 0
    },
    current: {
      type: Number,
      default: -1
    }
  },
  methods: {
    handleClick(index) {
      this.$emit('click-pagination', index);
    },

    isCurrentDot(index) {
      return index === this.current;
    }
  }
};
</script>

<style scoped>
.pagination {
  width: 100%;
  text-align: center;
}

.dot-container {
  display: inline-block;
  margin: 8px auto 0;
  padding: 10px;
}

.dot {
  display: inline-block;
  cursor: pointer;
  width: 8px;
  height: 8px;
  margin: 0 4px;
  border-radius: 100%;
  background-color: #efefef;
}

.dot--active {
  background-color: #000;
}
</style>
