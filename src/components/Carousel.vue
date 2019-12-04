<template>
  <div class="VueCarousel">
    <div class="VueCarousel-wrapper" ref="VueCarousel-wrapper">
      <div
        ref="VueCarousel-inner"
        :class="[
          'VueCarousel-inner'
        ]"
        :style="{
          transform: `translate(${currentOffset}px, 0)`,
          transition: transitionStyle,
          'ms-flex-preferred-size': `${slideWidth}px`,
          'webkit-flex-basis': `${slideWidth}px`,
          'flex-basis': `${slideWidth}px`,
          visibility: slideWidth ? 'visible' : 'hidden',
          height: `${currentHeight}`,
        }"
      >
        <slot></slot>
      </div>
    </div>

    <navigation
      :clickTargetSize="navigationClickTargetSize"
      :nextLabel="navigationNextLabel"
      :prevLabel="navigationPrevLabel"
      @navigationclick="handleNavigation"
    />

    <pagination @paginationclick="goToPage($event, 'pagination')" />
  </div>
</template>
<script>
import Navigation from './Navigation.vue';
import Pagination from './Pagination.vue';

const transitionEndNames = {
  onwebkittransitionend: 'webkitTransitionEnd',
  onmoztransitionend: 'transitionend',
  onotransitionend: 'oTransitionEnd otransitionend',
  ontransitionend: 'transitionend'
};

const getTransitionEnd = () => {
  for (let name in transitionEndNames) {
    if (name in window) {
      return transitionEndNames[name];
    }
  }
};

export default {
  name: 'carousel',
  beforeUpdate() {
    this.computeCarouselWidth();
  },
  components: {
    Navigation,
    Pagination
  },
  data() {
    return {
      browserWidth: null,
      carouselWidth: 0,
      currentPage: 0,
      offset: 0,
      refreshRate: 16,
      slideCount: 0,
      transitionstart: 'transitionstart',
      transitionend: 'transitionend',
      currentHeight: 'auto'
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
     * Amount of padding to apply around the label in pixels
     */
    navigationClickTargetSize: {
      type: Number,
      default: 8
    },
    /**
     * Text content of the navigation next button
     */
    navigationNextLabel: {
      type: String,
      default: '&#9654'
    },
    /**
     * Text content of the navigation prev button
     */
    navigationPrevLabel: {
      type: String,
      default: '&#9664'
    },
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
    },
    /**
     * Support for v-model functionality
     */
    value: {
      type: Number
    }
  },
  watch: {
    value(val) {
      if (val !== this.currentPage) {
        this.goToPage(val);
        this.render();
      }
    },
    currentPage(val) {
      this.$emit('pageChange', val);
      this.$emit('page-change', val);
      this.$emit('input', val);
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
    /**
     * Increase/decrease the current page value
     * @param  {String} direction (Optional) The direction to advance
     */
    advancePage(direction) {
      if (direction && direction === 'backward') {
        this.goToPage(this.getPreviousPage(), 'navigation');
      } else if (!direction || (direction && direction !== 'backward')) {
        this.goToPage(this.getNextPage(), 'navigation');
      }
    },
    /**
     * A mutation observer is used to detect changes to the containing node
     * in order to keep the magnet container in sync with the height its reference node.
     */
    attachMutationObserver() {
      const MutationObserver =
        window.MutationObserver ||
        window.WebKitMutationObserver ||
        window.MozMutationObserver;

      if (MutationObserver) {
        let config = {
          attributes: true,
          data: true
        };
        this.mutationObserver = new MutationObserver(() => {
          this.$nextTick(() => {
            this.computeCarouselWidth();
            this.computeCarouselHeight();
          });
        });
        if (this.$parent.$el) {
          let carouselInnerElements = this.$el.getElementsByClassName(
            'VueCarousel-inner'
          );
          for (let i = 0; i < carouselInnerElements.length; i++) {
            this.mutationObserver.observe(carouselInnerElements[i], config);
          }
        }
      }
    },
    handleNavigation(direction) {
      this.advancePage(direction);
      this.$emit('navigation-click', direction);
    },
    /**
     * Stop listening to mutation changes
     */
    detachMutationObserver() {
      if (this.mutationObserver) {
        this.mutationObserver.disconnect();
      }
    },
    /**
     * Get the current browser viewport width
     * @return {Number} Browser"s width in pixels
     */
    getBrowserWidth() {
      this.browserWidth = window.innerWidth;
      return this.browserWidth;
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
     * Get the maximum height of the carousel active slides
     * @return {String} The carousel height
     */
    getCarouselHeight() {
      return 'auto';
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
    /**
     * Set the current page to a specific value
     * This function will only apply the change if the value is within the carousel bounds
     * for carousel scrolling per page.
     * @param  {Number} page The value of the new page number
     * @param  {string|undefined} advanceType An optional value describing the type of page advance
     */
    goToPage(page, advanceType) {
      if (page >= 0 && page <= this.pageCount) {
        this.offset = this.slideWidth * page;

        // update the current page
        this.currentPage = page;

        if (advanceType === 'pagination') {
          this.$emit('pagination-click', page);
        }
      }
    },
    render() {
      // add extra slides depending on the momemtum speed
      this.offset += this.slideWidth;

      const width = this.slideWidth;

      // lock offset to either the nearest page, or to the last slide
      const lastFullPageOffset = width;
      const remainderOffset = lastFullPageOffset + this.slideWidth * this.slideCount;
      if (this.offset > (lastFullPageOffset + remainderOffset) / 2) {
        this.offset = remainderOffset;
      } else {
        this.offset = width * Math.round(this.offset / width);
      }

      // clamp the offset between 0 -> maxOffset
      this.offset = Math.max(0, Math.min(this.offset, this.maxOffset));

      // update the current page
      this.currentPage = Math.round(this.offset / this.slideWidth);
    },
    /**
     * Re-compute the width of the carousel and its slides
     */
    computeCarouselWidth() {
      this.getSlideCount();
      this.getBrowserWidth();
      this.getCarouselWidth();
    },
    /**
     * Re-compute the height of the carousel and its slides
     */
    computeCarouselHeight() {
      this.getCarouselHeight();
    },
    handleTransitionStart() {
      this.$emit('transitionStart');
      this.$emit('transition-start');
    },
    handleTransitionEnd() {
      this.$emit('transitionEnd');
      this.$emit('transition-end');
    }
  },
  mounted() {
    this.attachMutationObserver();
    this.computeCarouselWidth();
    this.computeCarouselHeight();

    this.transitionstart = getTransitionEnd();
    this.$refs['VueCarousel-inner'].addEventListener(
      this.transitionstart,
      this.handleTransitionStart
    );
    this.transitionend = getTransitionEnd();
    this.$refs['VueCarousel-inner'].addEventListener(
      this.transitionend,
      this.handleTransitionEnd
    );

    this.$emit('mounted');
  },
  beforeDestroy() {
    this.detachMutationObserver();
    this.$refs['VueCarousel-inner'].removeEventListener(
      this.transitionstart,
      this.handleTransitionStart
    );
    this.$refs['VueCarousel-inner'].removeEventListener(
      this.transitionend,
      this.handleTransitionEnd
    );
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
