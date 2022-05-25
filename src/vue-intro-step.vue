<template>
  <transition
      name="custom-classes-transition"
      enter-active-class="animate__animated animate__fadeIn animate__faster"
      leave-active-class="animate__animated animate__fadeOut animate__faster"
  >
    <div v-if="show" id="intro_box">
      <div class="top" :style="{height: `${originalBox.top}px`, backgroundColor: `rgba(0, 0, 0, ${config.backgroundOpacity ? config.backgroundOpacity : 0.9})`}"/>
      <div class="content" :style="{height: `${originalBox.height}px`}">
        <div class="left"
             :style="{top: `${originalBox.top}px`, width: `${originalBox.left}px`, height: `${originalBox.height}px`, backgroundColor: `rgba(0, 0, 0, ${config.backgroundOpacity ? config.backgroundOpacity : 0.9})`}"/>

        <div class="original-box"
             :style="{top: `${originalBox.top}px`, left: `${originalBox.left}px`,width: `${originalBox.width}px`, height: `${originalBox.height}px`}">
          <div class="round round-flicker"></div>
        </div>
        <div class="tip-box" :style="tipBoxStyle">
          <div class="tip-content">
            <div v-if="config.tips[currentIndex].title"
                 class="title"
                 :style="{textAlign: config.titleStyle ? config.titleStyle.textAlign ? config.titleStyle.textAlign : 'center' : 'center', fontSize: config.titleStyle ? config.titleStyle.fontSize ? config.titleStyle.fontSize : '19px' : '19px'}"
            >
              {{ config.tips[currentIndex].title }}
            </div>
            <div class="content"
                 :style="{textAlign: config.contentStyle ? config.contentStyle.textAlign ? config.contentStyle.textAlign : 'center' : 'center', fontSize: config.contentStyle ? config.contentStyle.fontSize ? config.contentStyle.fontSize : '15px' : '15px'}"
            >
              {{ config.tips[currentIndex].content }}
            </div>
            <div class="action"
                 :style="{ justifyContent: 'center' }"
            >
              <slot name="prev" v-bind:index="currentIndex" v-bind:tipItem="config.tips[currentIndex]" v-if="currentIndex !== 0">
                <div class="item prev" @click="prev">上一步</div>
              </slot>
              <slot name="next" v-bind:index="currentIndex" v-bind:tipItem="config.tips[currentIndex]" v-if="currentIndex !== config.tips.length - 1">
                <div class="item next" @click="next">下一步</div>
              </slot>
              <slot name="done" v-bind:index="currentIndex" v-bind:tipItem="config.tips[currentIndex]" v-if="currentIndex === config.tips.length - 1">
                <div class="item done" @click="done">完成</div>
              </slot>
              <slot v-else name="skip" v-bind:index="currentIndex" v-bind:tipItem="config.tips[currentIndex]">
                <div class="item skip" @click="done">跳过</div>
              </slot>
            </div>
          </div>
        </div>
        <div class="right"
             :style="{top: `${originalBox.top}px`, left: `${originalBox.left + originalBox.width}px`,width: `calc(100% - ${originalBox.left + originalBox.width}px)`, height: `${originalBox.height}px`, backgroundColor: `rgba(0, 0, 0, ${config.backgroundOpacity ? config.backgroundOpacity : 0.9})`}"
             ref="tip_box"/>
      </div>
      <div class="bottom" :style="{height: `calc(100% - ${originalBox.top}px - ${originalBox.height}px)`, backgroundColor: `rgba(0, 0, 0, ${config.backgroundOpacity ? config.backgroundOpacity : 0.9})`}"/>
    </div>
  </transition>
</template>

<script>
function throttle(fn, delay) {
  let timerId = null;
  let flag = true;
  return function () {
    if (!flag) return;
    flag = false;
    let self = this;
    let args = arguments;
    timerId && clearTimeout(timerId);
    timerId = setTimeout(function () {
      flag = true;
      fn.apply(self, args);
    }, delay || 1000);
  }
}
export default {
  name: "vueIntroStep",
  props: {
    show: {
      type: Boolean,
      default: false,
      required: true
    },
    config: {
      type: Object,
      required: true
    }
  },
  model: {
    prop: "show",
    event: "close"
  },
  data() {
    return {
      // 聚焦盒子的信息
      originalBox: {
        left: 250,
        top: 250,
        width: 200,
        height: 100
      },
      // 提示盒子的位置
      tipBoxPosition: 'bottom',
      // 当前显示的提示进度索引
      currentIndex: 0,
    }
  },
  watch: {
    config: {
      deep: true,
      handler() {
        // 监听配置变化 重置当前显示的索引
        this.currentIndex = 0;
      },
      immediate: true
    },
    show(val) {
      if (val) {
        this.setBoxInfo();
      }else {
        // 允许页面滚动
        document.body.style.overflow = 'auto';
      }
    }
  },
  computed: {
    // 计算提示盒子的位置
    // eslint-disable-next-line vue/return-in-computed-property
    tipBoxStyle() {
      // 如果提示盒子的位置是right
      if (this.tipBoxPosition === 'right') {
        return {
          left: `${this.originalBox.left + this.originalBox.width}px`,
          top: `${this.originalBox.top}px`
        }
      } else if (this.tipBoxPosition === 'left') {
        return {
          right: `${window.innerWidth - this.originalBox.left}px`,
          top: `${this.originalBox.top}px`
        }
      } else if (this.tipBoxPosition === 'top') {
        return {
          left: `${this.originalBox.left}px`,
          bottom: `${window.innerHeight - this.originalBox.top}px`
        }
      } else if (this.tipBoxPosition === 'bottom') {
        return {
          left: `${this.originalBox.left > window.innerWidth - 300 ? window.innerWidth - 300 : this.originalBox.left}px`,
          top: `${this.originalBox.top + this.originalBox.height}px`
        }
      }
    }
  },
  created() {
    this.init();
  },
  mounted() {
    window.onresize = throttle(() => {
      if (this.show) {
        this.setBoxInfo();
      }
    }, 100)
  },
  beforeDestroy() {
    window.onresize = null;
  },
  methods: {
    async prev() {
      // 判断是否有onPrev 是否可以继续往下走
      let flag = true;
      if (this.config.tips[this.currentIndex] && this.config.tips[this.currentIndex].onPrev) {
        flag = await this.config.tips[this.currentIndex].onPrev();
      }

      // 如果不能继续往下走
      if(!flag){
        throw new Error('onPrev 需要 Promise.resolve(true) 才可以继续往下走');
      }
      this.setBoxInfo(this.currentIndex - 1);
    },
    async next() {
      // 判断是否有onNext 是否可以继续往下走
      let flag = true;
      if (this.config.tips[this.currentIndex] && this.config.tips[this.currentIndex].onNext) {
        flag = await this.config.tips[this.currentIndex].onNext();
      }

      // 如果不能继续往下走
      if(!flag){
        throw new Error('onNext 需要 Promise.resolve(true) 才可以继续往下走');
      }
      this.setBoxInfo(this.currentIndex + 1);
    },
    done() {
      this.$emit('close', false);
    },
    // 根据标签获取盒子的位置
    async setBoxInfo(index) {
      try {
        if (index === undefined) {
          index = this.currentIndex;
        }

        if(this.show){
          // 禁止页面滚动
          document.body.style.overflow = 'hidden';
        }

        let el = this.config.tips[index].el;
        let box = document.querySelector(el);
        if (!box) {
          throw new Error('没有找到相应的元素');
        }
        let rect = box.getBoundingClientRect();
        this.originalBox = {
          left: rect.left,
          top: rect.top,
          width: rect.width,
          height: rect.height
        }
        this.tipBoxPosition = this.config.tips[index].tipPosition;

        this.currentIndex = index;
      } catch (e) {
        throw new Error(e.message);
      }
    },
    // 根据配置初始化
    init() {
      // 获取config中的tips数组
      const {tips} = this.config;
      let timer = null;
      // 判断tips是否存在 并且tips是否是数组
      if (tips && Array.isArray(tips)) {
        // 如果tips存在 并且tips是数组
        // 判断tips数组是否有数据
        if (tips.length > 0) {
          // 如果tips数组有数据
          // 初始化当前提示进度索引
          this.currentIndex = 0;
          // 获取第一个盒子
          try {
            let firstBox = document.querySelector(tips[0].el);
            timer = setInterval(() => {
              firstBox = document.querySelector(tips[0].el);
              if (firstBox) {
                // 如果第一个盒子存在
                this.setBoxInfo(0);
                clearInterval(timer);
              }
            }, 0)
          } catch (e) {
            throw new Error(e.message)
          }
        } else {
          throw new Error('tips数组不能为空');
        }
      } else {
        throw new Error('config中的tips不存在或者不是数组');
      }
    }
  }
}
</script>

<style scoped>
#intro_box {
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  z-index: 99999;
}
#intro_box > .top {
  width: 100%;
}
#intro_box > .content {
  width: 100%;
}
#intro_box > .content > .left {
  position: absolute;
  left: 0;
}
#intro_box > .content > .original-box {
  position: absolute;
  background-color: transparent;
  transition: all 0.3s cubic-bezier(0, 0, 0.58, 1);
}
#intro_box > .content > .original-box .round {
  position: absolute;
  left: 10px;
  top: 50%;
  transform: translateY(-50%);
  width: 10px;
  height: 10px;
  border-radius: 50%;
  opacity: 0.65;
  background-color: #9900ff;
}
#intro_box > .content > .original-box .round-flicker:before,
#intro_box > .content > .original-box .round-flicker:after {
  content: '';
  width: 100%;
  height: 100%;
  position: absolute;
  left: -1px;
  top: -1px;
  box-shadow: #9900ff 0px 0px 2px 2px;
  border: 1px solid rgba(153, 0, 255, 0.5);
  border-radius: 50%;
  animation: warn 2s linear 0s infinite;
}
@keyframes warn {
  0% {
    transform: scale(0.5);
    opacity: 1;
  }
  25% {
    transform: scale(1);
    opacity: 0.75;
  }
  50% {
    transform: scale(1.5);
    opacity: 0.5;
  }
  75% {
    transform: scale(2);
    opacity: 0.25;
  }
  100% {
    transform: scale(2.5);
    opacity: 0;
  }
}
#intro_box > .content > .tip-box {
  position: absolute;
  /*宽度应为内容宽*/
  width: fit-content;
  max-width: 300px;
  box-sizing: border-box;
  /*高度应为内容高度*/
  height: fit-content;
  transition: all 0.3s;
  z-index: 99999;
  padding: 12px;
  font-size: 15px;
}
#intro_box > .content > .tip-box > .tip-content {
  border-radius: 10px;
  overflow: hidden;
  padding: 10px;
  color: #fff;
}
#intro_box > .content > .tip-box > .tip-content > .title {
  font-weight: bold;
  margin-bottom: 10px;
}
#intro_box > .content > .tip-box > .tip-content > .content {
  white-space: normal;
  overflow-wrap: break-word;
  line-height: 1.5;
}
#intro_box > .content > .tip-box > .tip-content > .action {
  margin-top: 15px;
  width: 100%;
  display: flex;
}
#intro_box > .content > .tip-box > .tip-content > .action > .item {
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  border-radius: 15px;
  font-size: 12px;
  cursor: pointer;
  transition: all 0.3s;
  padding: 5px 15px;
  color: #fff;
  font-weight: bold;
  border: 1px solid #ccc;
  margin: 5px;
}
#intro_box > .content > .tip-box > .tip-content > .action > .item.prev {
  color: #ccc;
}
#intro_box > .content > .tip-box > .tip-content > .action > .item.next {
  color: #ccc;
}
#intro_box > .content > .tip-box > .tip-content > .action > .item.done {
  color: #ccc;
}
#intro_box > .content > .tip-box > .tip-content > .action > .item.skip {
  color: #ccc;
}
#intro_box > .content > .right {
  position: absolute;
  background-color: rgba(0, 0, 0, 0.9);
}
#intro_box > .bottom {
  width: 100%;
  background-color: rgba(0, 0, 0, 0.9);
}
</style>