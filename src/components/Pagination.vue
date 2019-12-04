<template>
  <div class="VueCarousel-pagination">
    <div class="VueCarousel-dot-container">
      <div
        v-for="(page, index) in paginationCount"
        :key="`${page}_${index}`"
        class="VueCarousel-dot"
        :class="{ 'VueCarousel-dot--active': isCurrentDot(index) }"
        v-on:click="goToPage(index)"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'pagination',
  inject: ['carousel'],
  computed: {
    paginationCount() {
      return this.carousel && this.carousel.scrollPerPage
        ? this.carousel.pageCount
        : this.carousel.slideCount || 0;
    }
  },
  methods: {
    goToPage(index) {
      this.$emit('paginationclick', index);
    },

    isCurrentDot(index) {
      return index === this.carousel.currentPage;
    }
  }
};
</script>

<style scoped>
.VueCarousel-pagination {
  width: 100%;
  text-align: center;
}

.VueCarousel-dot-container {
  display: inline-block;
  margin: 8px auto 0;
  padding: 10px;
}

.VueCarousel-dot {
  display: inline-block;
  cursor: pointer;
  width: 8px;
  height: 8px;
  margin: 0 4px;
  border-radius: 100%;
  background-color: #efefef;
}

.VueCarousel-dot--active {
  background-color: #000;
}
</style>
