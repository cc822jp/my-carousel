<template>
  <div class="carousel">
    <div class="container">
      <div
        ref="inner"
        class="inner"
        :style="{
          transform: `translate(${currentOffset}px, 0)`,
          transition: transitionStyle,
          'flex-basis': `${slideWidth}px`
        }"
      >
        <slot></slot>
      </div>
    </div>

    <navigation @click-prev="prev" @click-next="next" />

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
      slideWidth: 0,
      currentSlide: 0,
      offset: 0,
      slideCount: 0
    };
  },
  props: {
    // アニメーションのスピード
    speed: {
      type: Number,
      default: 1000
    }
  },
  /**
   * 算出プロパティ(getter的なやつ)
   */
  computed: {
    minSlideIndex() {
      return 0;
    },
    maxSlideIndex() {
      return this.slideCount - 1;
    },
    currentOffset() {
      return this.offset * -1;
    },
    transitionStyle() {
      const speed = `${this.speed / 1000}s`;
      return `${speed} ease transform`;
    }
  },
  methods: {
    next() {
      const idx =
        this.currentSlide === this.maxSlideIndex
          ? this.minSlideIndex
          : this.currentSlide + 1;
      this.goto(idx);
    },
    prev() {
      const idx =
        this.currentSlide === this.minSlideIndex
          ? this.maxSlideIndex
          : this.currentSlide - 1;
      this.goto(idx);
    },
    goto(idx) {
      this.offset = this.slideWidth * idx;
      this.currentSlide = idx;
    }
  },
  /**
   * mount時
   */
  mounted() {
    this.slideCount = this.$slots.default.length;
    this.slideWidth = this.$refs.inner.clientWidth;
    this.$emit('mounted');
  }
};
</script>

<style>
.carousel {
  display: flex;
  flex-direction: column;
  position: relative;
}

.container {
  width: 100%;
  position: relative;
  overflow: hidden;
}

.inner {
  display: flex;
  flex-direction: row;
  backface-visibility: hidden;
}
</style>
