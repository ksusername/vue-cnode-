<template>
  <div class="svg-wrap">
    <svg class="svg" :width="width + 'px'" :height="height + 'px'" viewBox="0 0 80 80">
      <path class="svg-bg"
            d="M40,10C57.351,10,71,23.649,71,40.5S57.351,71,40.5,71 S10,57.351,10,40.5S23.649,10,40.5,10z"></path>
      <path class="svg-circle"
            d="M40,10C57.351,10,71,23.649,71,40.5S57.351,71,40.5,71 S10,57.351,10,40.5S23.649,10,40.5,10z"></path>
    </svg>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        $circle: null,
        totalLen: 0,
        preLoading: false // 预先的loading 状态
      };
    },
    props: {
      width: {
        type: Number,
        default: 60,
        validator: val => val >= 10
      },

      height: {
        type: Number,
        default: 60,
        validator: val => val >= 10
      },

      state: {
        default: 'start' // start | end
      }
    },
    mounted() {
      this.$circle = this.$el.querySelector('.svg-circle');
      this.totalLen = this.$circle.getTotalLength();
      this.init();
    },
    watch: {
      state(newVal) {
        switch (newVal) {
          case 'start':
            this.startLoading();
            break;
          case 'end':
            if (this.preLoading) {
              this.endLoading();
            }
            break;
          case 'init':
            this.init();
            break;
          default:
            return;
        }
      }
    },
    methods: {
      init() {
        this.$circle.style.strokeDasharray = this.$circle.style.strokeDashoffset = this.totalLen;
      },

      startLoading() {
        let progress = 0;

        const timer = window.setInterval(() => {
          progress = Math.min(progress + (Math.random() * 0.1), 1);

          this.draw(progress);

          if ((1 - progress) <= 0.3) {
            window.clearInterval(timer);
            this.preLoading = true;

            if (this.state === 'end') {
              this.endLoading();
            }
          }
        }, 80);
      },

      draw(val) {
        this.$circle.style.strokeDashoffset = this.totalLen * (1 - val);
      },

      endLoading() {
        this.preLoading = false;
        this.draw(1);

        // 确保完成最后一次的draw
        window.setTimeout(() => {
          // 初始化状态
          // this.$circle.style.strokeDasharray = this.$circle.style.strokeDashoffset = this.totalLen;

          this.$emit('loaded');
        }, 500);
      }
    }
  };
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
  .svg {
    path {
      fill: none;
      stroke-width: 6;
    }

    &-bg {
      stroke: #ddd;
    }

    &-circle {
      transition: stroke-dashoffset 0.2s;
      stroke: #ef6e7e;
    }
  }
</style>
