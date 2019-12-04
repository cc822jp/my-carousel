<template>
  <div class="VueCarousel">
    <div class="VueCarousel-wrapper" ref="VueCarousel-wrapper">
      <div
        ref="VueCarousel-inner"
        class="VueCarousel-inner"
        :style="{
          transform: `translate(${currentOffset}px, 0)`,
          transition: transitionStyle,
          'flex-basis': `${slideWidth}px`
        }"
      >
        <slot></slot>
      </div>
    </div>

    <navigation
      @click-prev="prev"
      @click-next="next"
    />

    <pagination
      :count="slideCount"
      :current="currentSlide"
      @click-pagination="goto($event)"
    />
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
      currentSlide: 0,
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
    // アニメーションのスピード
    speed: {
      type: Number,
      default: 500
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
    next() {
      const index = (this.currentSlide < this.slideCount - 1) ? this.currentSlide + 1 : 0;
      this.goto(index);
    },
    prev() {
      const index = (this.currentSlide > 0) ? this.currentSlide - 1 : this.slideCount - 1;
      this.goto(index);
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
              slot.tag.match(`^vue-component-\\d+-slide$`) !== null
          ).length) ||
        0;
    },
    goto(page) {
      this.offset = this.slideWidth * page;
      this.currentSlide = page;
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
      this.currentSlide = Math.round(this.offset / this.slideWidth);
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
