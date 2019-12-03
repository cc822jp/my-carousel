<template>
  <div class="Wrapper" v-bind:style="style">
    <ul class="Tabs" ref="tabs">
      <li
        class="Tab"
        data-tab-index="1"
        v-bind:active="tabActiveIndex === '1'"
        v-on:click="_handleClick"
      >
        Tab1
      </li>
      <li
        class="Tab"
        data-tab-index="2"
        v-bind:active="tabActiveIndex === '2'"
        v-on:click="_handleClick"
      >
        Tab2
      </li>
      <li
        class="Tab"
        data-tab-index="3"
        v-bind:active="tabActiveIndex === '3'"
        v-on:click="_handleClick"
      >
        Tab3
      </li>
    </ul>
    <div class="TabPanel" v-bind:active="tabActiveIndex === '1'">
      Tab1Content
    </div>
    <div class="TabPanel" v-bind:active="tabActiveIndex === '2'">
      Tab2Content
    </div>
    <div class="TabPanel" v-bind:active="tabActiveIndex === '3'">
      Tab3Content
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      style: {
        '--tab-count': 0,
        '--tab-active-panel': 0
      },
      tabActiveIndex: '1'
    };
  },
  mounted: function() {
    this.style['--tab-count'] = [...this.$refs.tabs.children].length;
    this.style['--tab-active-panel'] = this.tabActiveIndex;
    document.querySelector('hogehoge');
  },
  methods: {
    _handleClick(event) {
      const target = event.currentTarget;
      const { tabIndex } = target.dataset;
      this.tabActiveIndex = tabIndex;
      this.style['--tab-active-panel'] = tabIndex;
    }
  }
};
</script>

<style scoped>
.Wrapper {
  --tab-count: 0;
  --tab-active-panel: 0;
}

ul {
  padding: 0;
  margin: 0;
}

li {
  list-style: none;
  margin: 0;
}

.Tabs {
  position: relative;
  padding: 0;
  margin: 0;
  display: inline-flex;
}

.Tab {
  padding: 1rem 2rem;
  text-align: center;
}

.Tabs::after {
  content: '';
  position: absolute;
  bottom: -1px;
  left: calc(100% / var(--tab-count) * calc(var(--tab-active-panel) - 1));
  height: 1px;
  width: calc(100% / var(--tab-count));
  background-color: darkcyan;
  margin: 0 auto;
  transition: all 0.2s ease-in-out;
}

.TabPanel {
  display: none;
  padding: 2rem;
  background: #eee;
}

.Tab[active],
.TabPanel[active] {
  display: block;
  color: darkcyan;
}
</style>
