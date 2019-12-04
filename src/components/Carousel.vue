<template>
  <div class="VueCarousel">
    <div class="VueCarousel-wrapper" ref="VueCarousel-wrapper">
      <div
        ref="VueCarousel-inner"
        class="VueCarousel-inner"
        :style="{
          transform: `translate(${currentOffset}px, 0)`,
          transition: transitionStyle,
          'ms-flex-preferred-size': `${slideWidth}px`,
          'webkit-flex-basis': `${slideWidth}px`,
          'flex-basis': `${slideWidth}px`,
          visibility: slideWidth ? 'visible' : 'hidden'
        }"
      >
        <slot></slot>
      </div>
    </div>

    <navigation
      @navigation-click-prev="handleNavigationPrev"
      @navigation-click-next="handleNavigationNext"
    />

    <pagination @paginationclick="goToPage($event, 'pagination')" />
  </div>
</template>
<script>
import Navigation from './Navigation.vue';
import Pagination from './Pagination.vue';

export default {
  name: 'carousel',
  components: {
    Navigation,
    Pagination
  },
  data() {
    return {
      carouselWidth: 0,
      currentPage: 0,
      offset: 0,
      slideCount: 0
    };
  },
  // use `provide` to avoid `Slide` being nested with other components
  provide() {
    return {
      carousel: this
    };
  },
  props: {
    /**
     * The fill color of the active pagination dot
     * Any valid CSS color is accepted
     */
    paginationActiveColor: {
      type: String,
      default: '#000000'
    },
    /**
     * The fill color of pagination dots
     * Any valid CSS color is accepted
     */
    paginationColor: {
      type: String,
      default: '#efefef'
    },
    /**
     * The padding inside each pagination dot
     * Pixel values are accepted
     */
    paginationPadding: {
      type: Number,
      default: 10
    },
    /**
     * The size of each pagination dot
     * Pixel values are accepted
     */
    paginationSize: {
      type: Number,
      default: 10
    },
    /**
     * Slide transition speed
     * Number of milliseconds accepted
     */
    speed: {
      type: Number,
      default: 500
    },
    /**
     * Name (tag) of slide component
     * Overwrite when extending slide component
     */
    tagName: {
      type: String,
      default: 'slide'
    }
  },
  computed: {
    /**
     * The horizontal distance the inner wrapper is offset while navigating.
     * @return {Number} Pixel value of offset to apply
     */
    currentOffset() {
      return this.offset * -1;
    },
    /**
     * Maximum offset the carousel can slide
     * @return {Number}
     */
    maxOffset() {
      return Math.max(this.slideWidth * (this.slideCount - 1), 0);
    },
    /**
     * Calculate the number of pages of slides
     * @return {Number} Number of pages
     */
    pageCount() {
      return this.slideCount;
    },
    /**
     * Calculate the width of each slide
     * @return {Number} Slide width
     */
    slideWidth() {
      return this.carouselWidth;
    },
    transitionStyle() {
      const speed = `${this.speed / 1000}s`;
      return `${speed} ease transform`;
    }
  },
  methods: {
    /**
     * @return {Number} The index of the next page
     * */
    getNextPage() {
      if (this.currentPage < this.pageCount - 1) {
        return this.currentPage + 1;
      }
      return 0;
    },
    /**
     * @return {Number} The index of the previous page
     * */
    getPreviousPage() {
      if (this.currentPage > 0) {
        return this.currentPage - 1;
      }
      return this.pageCount - 1;
    },
    handleNavigationPrev() {
      this.goToPage(this.getPreviousPage());
    },
    handleNavigationNext() {
      this.goToPage(this.getNextPage());
    },
    /**
     * Get the width of the carousel DOM element
     * @return {Number} Width of the carousel in pixels
     */
    getCarouselWidth() {
      let carouselInnerElements = this.$el.getElementsByClassName(
        'VueCarousel-inner'
      );
      for (let i = 0; i < carouselInnerElements.length; i++) {
        if (carouselInnerElements[i].clientWidth > 0) {
          this.carouselWidth = carouselInnerElements[i].clientWidth || 0;
        }
      }
      return this.carouselWidth;
    },
    /**
     * Filter slot contents to slide instances and return length
     * @return {Number} The number of slides
     */
    getSlideCount() {
      this.slideCount =
        (this.$slots &&
          this.$slots.default &&
          this.$slots.default.filter(
            slot =>
              slot.tag &&
              slot.tag.match(`^vue-component-\\d+-${this.tagName}$`) !== null
          ).length) ||
        0;
    },
    goToPage(page) {
      this.offset = this.slideWidth * page;
      this.currentPage = page;
    },
    render() {
      // add extra slides depending on the momemtum speed
      this.offset += this.slideWidth;

      const width = this.slideWidth;

      // lock offset to either the nearest page, or to the last slide
      const lastFullPageOffset = width;
      const remainderOffset =
        lastFullPageOffset + this.slideWidth * this.slideCount;
      if (this.offset > (lastFullPageOffset + remainderOffset) / 2) {
        this.offset = remainderOffset;
      } else {
        this.offset = width * Math.round(this.offset / width);
      }

      // clamp the offset between 0 -> maxOffset
      this.offset = Math.max(0, Math.min(this.offset, this.maxOffset));

      // update the current page
      this.currentPage = Math.round(this.offset / this.slideWidth);
    }
  },
  mounted() {
    this.getSlideCount();
    this.getCarouselWidth();
    this.$emit('mounted');
  }
};
</script>
<style>
.VueCarousel {
  display: flex;
  flex-direction: column;
  position: relative;
}

.VueCarousel-wrapper {
  width: 100%;
  position: relative;
  overflow: hidden;
}

.VueCarousel-inner {
  display: flex;
  flex-direction: row;
  backface-visibility: hidden;
}
</style>
