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
    interval: -1,
    isInViewPort: true,
  }),
  mounted() {
    this.counter = this.startFrom
    this.updateCountTo(this.$props.countTo)
    this.doAnimation()

    window.addEventListener('scroll', this.onScroll)
  },
  destroyed() {
    if(!this.interval)
      clearInterval(this.interval)
    window.removeEventListener('scroll', this.onScroll)
  },
  watch: {
    countTo(val: number) {
      this.updateCountTo(val)
    }
  },
  methods: {
    onScroll() {
      const box = this.$refs.box as HTMLElement
      this.isInViewPort = this.isInTheViewPort(box)
    },
    updateCountTo(val: number) {

      if(!this.$refs.box) {
        this.counter = parseFloat(val.toFixed(this.precision))
        return;
      }
      
      
      if (!this.isInViewPort){
        this.counter = parseFloat(this.countTo.toFixed(this.precision))
        clearInterval(this.interval)
        this.interval = 0;
        return;
      }

      if(!this.interval) {
        this.doAnimation()
      }

      this.updateBy = val - this.counter;
      this.timeDelta = Math.ceil(this.time / this.precision)
      this.intervalCounter = 0
    },
    easeOutQuint(x: number): number {
      return 1 - Math.pow(1 - x, 5);
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
      this.interval = setInterval(() => {
        if(this.intervalCounter > this.timeDelta || !this.updateBy || !this.isInViewPort) {
          this.interval = 0;
          clearInterval(this.interval)
          return;
        }
        const changeCo = this.updateBy * this.easeOutQuint(this.intervalCounter / this.timeDelta)
        this.updateBy -= changeCo
        this.counter += changeCo

        this.intervalCounter++
      }, 10 / this.precision) as unknown as number
    }
  }
});
</script>

<template>
  <span ref="box" class="num-anim-vue">
    {{ counter.toFixed(precision) }}
  </span>
</template>
