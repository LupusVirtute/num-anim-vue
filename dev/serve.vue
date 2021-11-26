<script lang="ts">
import Vue from 'vue';
import NumAnimVue from '@/num-anim-vue.vue';

export default Vue.extend({
  name: 'ServeDev',
  components: {
    NumAnimVue
  },
  data() {
    return {
      counter: 2,
    };
  },
  mounted() {
    setInterval(() => {
      this.counter += Math.random();
      console.log(this.counter)
    }, 8000);
  }
});
</script>

<template>
  <div id="app">
    <num-anim-vue :precision="18" :countTo="counter"/>
  </div>
</template>
