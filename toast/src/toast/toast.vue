<template>
    <div ref="wrapper">
        <slot></slot>
    </div>
</template>

<script>
  import BScroll from 'better-scroll'
  export default {
    props: {
        /*1 滚动的时候会派发scroll事件，会截流。
         2 滚动的时候实时派发scroll事件，不会截流。
         3 除了实时派发scroll事件，在swipe的情况下仍然能实时派发scroll事件     */
      // 有时候我们需要知道滚动的位置。可选值：1、2、3，默认值：0
      // 当 probeType 为 1 的时候，会非实时（屏幕滑动超过一定时间后）派发scroll 事件；
      // 当 probeType 为 2 的时候，会在屏幕滑动的过程中实时的派发 scroll 事件；
      // 当 probeType 为 3 的时候，不仅在屏幕滑动的过程中，而且在 momentum 滚动动画运行过程中实时派发 scroll 事件。
      // 如果没有设置该值，其默认值为 0，即不派发 scroll 事件。
      probeType: {
        type: Number, // 类型：Number
        default: 1
      },
      //  点击列表是否派发click事件
      click: {
        type: Boolean,
        default: true
      },
      // 当滚动超过边缘的时候会有一小段回弹动画。设置为 true 则开启动画。默认值：true
      bounce: {
      	type: Boolean, // 类型：Boolean | Object
        default: true
      },
      //  是否开启横向滚动
      scrollX: {
        type: Boolean,
        default: false
      },
      // 是否开启纵向滚动
      scrollY: {
        type: Boolean,
        default: true
      },
      //  是否派发滚动事件
      listenScroll: {
        type: Boolean,
        default: false
      },
      //  列表的数据
      data: {
        type: Array,
        default: null
      },
      /** * 是否派发滚动到底部的事件，用于上拉加载 */
      pullup: {
        type: Boolean,
        default: false
      },
      /** * 是否派发顶部下拉的事件，用于下拉刷新 */
      pulldown: {
        type: Boolean,
        default: false
      },
      /** * 是否派发列表滚动开始的事件 */
      beforeScroll: {
        type: Boolean,
        default: false
      },
      /** * 当数据更新后，刷新scroll的延时。 */
      refreshDelay: {
        type: Number,
        default: 20
      },
      // 这个配置用于 PC 端的鼠标滚轮，默认为 false，。当设置为 true 或者是一个 Object 的时候，可以开启鼠标滚轮，默认值：false
      mouseWheel: {
      	type: Boolean,
        default: true
      },
      // 忽略滚动的方向，可选值：'vertical'、'horizontal'
      // 有时候我们使用 better-scroll 在某个方向模拟滚动的时候，希望在另一个方向保留原生的滚动（
      // 比如轮播图，我们希望横向模拟横向滚动，而纵向的滚动还是保留原生滚动，
      // 我们可以设置 eventPassthrough 为 vertical；相应的，如果我们希望保留横向的原生滚动，可以设置eventPassthrough为 horizontal）。
      // 备注：eventPassthrough 的设置会导致其它一些选项配置无效，需要小心使用它。
      eventPassthrough: { 
        type: String,
        default: ''
      },
    },
    mounted() {
      // 保证在DOM渲染完毕后初始化better-scroll
      setTimeout(() => {
        this.initScroll()
      }, 20)
    },
    methods: {
      initScroll() {
        if (!this.$refs.wrapper) {
          return
        }
        // better-scroll的初始化
        this.scroll = new BScroll(this.$refs.wrapper, {
          probeType: this.probeType,
          click: this.click,
          scrollX: this.scrollX,
          scrollY: this.scrollY,
          mouseWheel: this.mouseWheel,
          eventPassthrough: this.eventPassthrough
        })
        // 是否派发滚动事件
        if (this.listenScroll) {
          let me = this
          this.scroll.on('scroll', (pos) => {
            me.$emit('scroll', pos)
          })
        }
        // 是否派发滚动到底部事件，用于上拉加载
        if (this.pullup) {
          this.scroll.on('scrollEnd', () => { // 滚动到底部
            if (this.scroll.y <= (this.scroll.maxScrollY + 50)) {
              this.$emit('scrollToEnd')
            }
          })
        }
        // 是否派发顶部下拉事件，用于下拉刷新
        if (this.pulldown) {
          this.scroll.on('touchend', (pos) => { // 下拉动作
            if (pos.y > 50) {
              this.$emit('pulldown')
            }
          })
        }
        // 是否派发列表滚动开始的事件
        if (this.beforeScroll) {
          this.scroll.on('beforeScrollStart', () => {
            this.$emit('beforeScroll')
          })
        }
      },
      disable() {
        // 代理better-scroll的disable方法
        this.scroll && this.scroll.disable()
      },
      enable() {
        // 代理better-scroll的enable方法
        this.scroll && this.scroll.enable()
      },
      refresh() {
        // 代理better-scroll的refresh方法
        this.scroll && this.scroll.refresh()
      },
      scrollTo() {
        // 代理better-scroll的scrollTo方法
        this.scroll && this.scroll.scrollTo.apply(this.scroll, arguments)
      },
      scrollToElement() {
        // 代理better-scroll的scrollToElement方法
        this.scroll && this.scroll.scrollToElement.apply(this.scroll, arguments)
      }
    },
    watch: {
      // 监听数据的变化，延时refreshDelay时间后调用refresh方法重新计算，保证滚动效果正常
      data() {
        setTimeout(() => {
          this.refresh()
        }, this.refreshDelay)
      }
    }
  }
</script>

<style>
</style>