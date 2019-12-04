<template>
  <div id="app">
    <div class="carousel">
      <carousel :navigation-enabled="true">
        <slide>
          <img class="img" src="./assets/1.png">
        </slide>
        <slide>
          <img class="img" src="./assets/2.png">
        </slide>
        <slide>
          <img class="img" src="./assets/3.png">
        </slide>
      </carousel>
    </div>
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

<style>
  .carousel {
    width: 80%;
    max-width: 800px;
    margin: 0 auto;
  }

  .img {
    display: block;
    width: 100%;
  }
</style>
