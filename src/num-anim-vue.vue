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
    updatedTimeDelta: 0,
    updatedUpdatedBy: 0,
    isCounterUpdated: false,
    isInViewPort: true,
  }),
  mounted() {
    this.counter = this.startFrom
    this.updateCountTo(this.$props.countTo)
    this.doAnimation()

    window.addEventListener('scroll', this.onScroll)
  },
  destroyed() {
    if(this.interval)
      clearInterval(this.interval)
    window.removeEventListener('scroll', this.onScroll)
  },
  watch: {
    countTo(val: number) {
      const change = val - this.counter;
      if(change >= Math.pow(10, -this.precision)) {
        this.updateCountTo(val)
      }
    }
  },
  methods: {
    async onScroll() {
      const box = this.$refs.box as HTMLElement
      this.isInViewPort = this.isInTheViewPort(box)
    },
    async updateCountTo(val: number) {

      if(!this.$refs.box) {
        this.counter = parseFloat(val.toFixed(this.precision))
        return;
      }
      
      
      if (!this.isInViewPort){
        clearInterval(this.interval)
        this.counter = parseFloat(val.toFixed(this.precision))
        this.timeDelta = 0;
        this.updateBy = 0;
        this.intervalCounter = 1;
        this.interval = 0;
        return;
      }

      this.isCounterUpdated = true;
      this.updatedUpdatedBy = val - this.counter;
      this.updatedTimeDelta = Math.ceil(this.time / this.precision)

      if(!this.interval && this.isInViewPort) {
        this.intervalCounter = 0;
        this.doAnimation()
      }
    },
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
    async doAnimation() {
      this.interval = setInterval(async () => {

        if(this.isCounterUpdated) {
          this.updateBy = this.updatedUpdatedBy
          this.timeDelta = this.updatedTimeDelta
          this.isCounterUpdated = false
        }

        if(this.intervalCounter > this.timeDelta || !this.updateBy || !this.isInViewPort) {
          this.interval = 0;
          clearInterval(this.interval)
          return;
        }
        const changeCo = this.updateBy * this.easeInSine(this.intervalCounter / this.timeDelta)
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
