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
        tips: [
          {
            el: '#xxx',
            tipPosition: 'bottom',
            title: '欢迎使用问答管理系统',
            content: '点击左侧菜单进行操作',
          }
        ]
      }
    }
  }
});
</script>

<template>
  <div id="app">
    <img id="xxx" alt="Vue logo" src="http://pic.iask.cn/fimg/183674993053.jpg">
    <button @click="show = true">改变</button>
    <vue-intro-step v-model="show" :config="config" />
  </div>
</template>
