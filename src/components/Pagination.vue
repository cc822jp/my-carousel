<template>
  <div v-show="carousel.pageCount > 1" class="VueCarousel-pagination">
    <div
      class="VueCarousel-dot-container"
      role="tablist"
      :style="dotContainerStyle"
    >
      <button
        v-for="(page, index) in paginationCount"
        :key="`${page}_${index}`"
        class="VueCarousel-dot"
        aria-hidden="false"
        role="tab"
        :title="getDotTitle(index)"
        :value="getDotTitle(index)"
        :aria-label="getDotTitle(index)"
        :aria-selected="isCurrentDot(index) ? 'true' : 'false'"
        v-bind:class="{ 'VueCarousel-dot--active': isCurrentDot(index) }"
        v-on:click="goToPage(index)"
        :style="dotStyle(index)"
      ></button>
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
    },
    dotContainerStyle() {
      const { carousel } = this;
      return {
        'margin-top': `${carousel.paginationPadding * 2}px`
      };
    }
  },
  methods: {
    /**
     * Change page by index
     * @param {number} index
     * return {void}
     */
    goToPage(index) {
      /**
       * @event paginationclick
       * @type {number}
       */
      this.$emit('paginationclick', index);
    },

    /**
     * Check on current dot
     * @param {number} index - dot index
     * @return {boolean}
     */
    isCurrentDot(index) {
      return index === this.carousel.currentPage;
    },

    /**
     * Generate dot title
     * @param {number} index - dot index
     * @return {string}
     */
    getDotTitle(index) {
      return this.carousel.$children[index].title
        ? this.carousel.$children[index].title
        : `Item ${index}`;
    },
    /**
     * Control dots appear and disappear
     * @param {number} index - dot index
     * @return {Object} - dot(buttn) style
     */
    dotStyle(index) {
      const { carousel } = this;
      const basicBtnStyle = {};

      Object.assign(basicBtnStyle, {
        padding: `${carousel.paginationPadding}px`,
        width: `${carousel.paginationSize}px`,
        height: `${carousel.paginationSize}px`,
        'background-color': `${
          this.isCurrentDot(index)
            ? carousel.paginationActiveColor
            : carousel.paginationColor
        }`
      });

      return basicBtnStyle;
    }
  }
};
</script>

<style scoped>
.VueCarousel-pagination {
  width: 100%;
  text-align: center;
}

.VueCarousel-pagination--top-overlay {
  position: absolute;
  top: 0;
}

.VueCarousel-pagination--bottom-overlay {
  position: absolute;
  bottom: 0;
}

.VueCarousel-dot-container {
  display: inline-block;
  margin: 0 auto;
  padding: 0;
}

.VueCarousel-dot {
  display: inline-block;
  cursor: pointer;
  appearance: none;
  border: none;
  background-clip: content-box;
  box-sizing: content-box;
  padding: 0;
  border-radius: 100%;
  outline: none;
}

.VueCarousel-dot:focus {
  outline: 1px solid lightblue;
}
</style>
