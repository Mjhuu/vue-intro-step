<script>
import Vue from 'vue';
import VueIntroStep from '@/vue-intro-step.vue';

export default Vue.extend({
  name: 'ServeDev',
  components: {
    VueIntroStep
  },
  data(){
    return{
      show: false,
      config: {
        backgroundOpacity: 0.8,
        titleStyle: {
          textAlign: 'center',
          fontSize: '19px',
        },
        contentStyle: {
          textAlign: 'center',
          fontSize: '15px',
        },
        tips: [
          {
            el: '#xxx',
            tipPosition: 'bottom',
            title: '欢迎使用问答管理系统',
            content: '点击左侧菜单进行操作',
            onNext: () => {
              return new Promise((resolve) => {
                // 当页面很长，下一步的内容不在此可视区域内时，可进行下面操作
                document.body.style.overflow = 'auto';
                // 让body滚动到顶部
                window.scrollTo(0,0)
                resolve(true);
              });
            }
          },
          {
            el: '#right',
            tipPosition: 'bottom',
            title: '欢迎使用问答管理系统',
            content: '点击左侧菜单进行操作点击左侧菜单进行操作点击左侧菜单进行操作点击左侧菜单进行操作点击左侧菜单进行操作点击左侧菜单进行操作',
            // 点击上一步时，触发的事件
            onPrev: () => {
              return new Promise((resolve) => {
                resolve(true);
              });
            },
          },
          {
            el: '#xxx',
            tipPosition: 'bottom',
            title: '欢迎使用问答管理系统',
            content: '点击左侧菜单进行操作',
          }
        ]
      }
    }
  },
  methods: {
    close(){
      this.show = false;
    },
    done(){
      this.show = false;
    },
    next(tipItem){
      // tipItem当前的提示项信息
      // 调用vue3-intro-step的next方法
      this.$refs.myIntroStep.next()
      console.log(tipItem);
    },
    prev(tipItem, currentIndex){

      // 调用vue3-intro-step的prev方法
      this.$refs.myIntroStep.prev()
      console.log(tipItem, currentIndex);
    }
  }
});
</script>

<template>
  <div id="app">
    <button @click="show = true">改变</button>
    <div style="height: 400px;">
      123
    </div>
    <div id="xxx" style="width: 100px;height: 100px;background-color: seagreen;">
      443
    </div>
    <div id="right" style="position:absolute;right: 10px;top: 10px;width: 100px;height: 60px;background-color: skyblue;">哟次</div>

    <div style="height: 400px;">
      3432
    </div>
    <button @click="show = true">改变</button>
    {{show}}
    <vue-intro-step v-model:show="show" :config="config" ref="myIntroStep">
      <!--
        插槽-自定义底部按钮

        tipItem当前的提示信息 index当前提示新的索引

        不写的话，会显示默认的底部按钮
       -->
      <template #prev="{tipItem, index}">
        <button @click="prev(tipItem, index)">上一步</button>
      </template>
      <template #next="{tipItem}">
        <button @click="next(tipItem)">下一步</button>
      </template>
      <template #done>
        <button @click="done">done</button>
      </template>
      <template #skip>
        <button @click="done">skip</button>
      </template>
    </vue-intro-step>
  </div>
</template>
