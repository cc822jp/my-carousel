<template>
  <div class="pagination">
    <div class="container">
      <div
        v-for="(page, index) in count"
        :key="`${page}_${index}`"
        :class="{
          dot: true,
          'dot--active': index === current
        }"
        v-on:click="$emit('click-pagination', index)"
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
  }
};
</script>

<style scoped>
.pagination {
  width: 100%;
  text-align: center;
}

.container {
  display: inline-block;
  margin: 8px auto 0;
  padding: 0;
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
  background-color: #999;
}
</style>
