<template>
  <div id="app">
    <carousel>
      <slide>
        <a href="http://www.google.com" target="_blank">
          <img class="img" src="./assets/1.png" />
        </a>
      </slide>
      <slide>
        <img class="img" src="./assets/2.png" />
      </slide>
      <slide>
        <img class="img" src="./assets/3.png" />
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

<style>
.img {
  display: block;
  width: 100%;
}
</style>
