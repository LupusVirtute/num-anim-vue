<script lang="ts">
import Vue from 'vue';

export default Vue.extend({
  name: 'NumAnimVue',
  props: {
    countTo: {
      type: Number,
      default: 0,
    },
    time: {
      type: Number,
      default: 4000,
    },
    startFrom: {
      type: Number,
      default: 0,
    },
    precision: {
      type: Number,
      default: 2,
    },
  },
  data: () => ({
    counter: 0,
    timeDelta: 0,
    intervalCounter: 0,
    updateBy: 0,
    updateDelta: 0,
  }),
  mounted() {
    this.counter = this.startFrom
    this.doAnimation()
  },
  watch: {
    countTo(val: number) {
      if(!this.$refs.box) {
        this.counter = parseFloat(val.toFixed(this.precision))
        return;
      }
      
      const box = this.$refs.box as HTMLElement
      
      if (!this.isInTheViewPort(box)){
        this.counter = parseFloat(val.toFixed(this.precision))
        return;
      }
      this.updateBy = val - this.counter;

      this.timeDelta = Math.ceil(this.time / this.precision)
      this.intervalCounter = 0
    }
  },
  methods: {
    easeInSine(x: number): number {
      return 1 - Math.cos((x * Math.PI) / 2);
    },
    isInTheViewPort(element: HTMLElement): boolean {
      const bounding = element.getBoundingClientRect();
      const { offsetHeight: height, offsetWidth: width } = element;
      return bounding.top >= -height 
        && bounding.left >= -width
        && bounding.right <= (window.innerWidth || document.documentElement.clientWidth) + width
        && bounding.bottom <= (window.innerHeight || document.documentElement.clientHeight) + height

    },
    getLowestNumberForGivenPrecision(precision: number): number {
      return 1 / (10*precision)
    },
    doAnimation() {
      setInterval(() => {
        if(this.intervalCounter >= this.timeDelta) {
          return;
        }
        const changeCo = this.updateBy * this.easeInSine(this.intervalCounter / this.timeDelta)
        this.updateBy -= changeCo
        this.counter += changeCo

        this.intervalCounter++
      }, this.precision)
    }
  }
});
</script>

<template>
  <span ref="box" class="num-anim-vue">
    {{ counter.toFixed(precision) }}
  </span>
</template>
