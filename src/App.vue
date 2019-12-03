<template>
  <div id="app">
    <carousel :per-page="1">
      <slide>
        Slide 1 Content
      </slide>
      <slide>
        Slide 2 Content
      </slide>
    </carousel>
  </div>
</template>

<script>
import Carousel from './components/Carousel';
import Slide from './components/Slide';

export default {
  name: 'app',
  components: {
    Carousel,
    Slide
  }
};
</script>
