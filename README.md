## vue-intro-step

### 安装

```shell
npm i vue-intro-step --save
```

> [可选] 为了更好的使用 *vue-intro-step* 使引导组件显示、隐藏不突兀
> 可以安装 animate.css 实现动画效果
> ```shell
> npm i animate.css --save
> 
> # 在main.js中引入
> import 'animate.css'
> ```

### 全局引用

`main.js`

```js
import Vue from 'vue'
import VueIntroStep from 'vue-intro-step'

Vue.component('vue-intro-step', VueIntroStep);

```

### 局部引用

```vue

<template>
  <vue-intro-step
      v-model="show" 
      :config="config"
      @close="closeIntro"
  />
</template>

<script>
import VueIntroStep from 'vue-intro-step'

export default {
  name: 'App',
  components: {
    VueIntroStep
  },
  data () {
    return {
      show: false,
      config: {
        tips: [
          {
            el: '#intro_title',
            tipPosition: 'bottom',
            title: '欢迎使用问答管理系统',
            content: '点击左侧菜单进行操作',
          },
          {
            el: '#intro_mine',
            tipPosition: 'left',
            // title: '点击进入个人中心',
            content: '查看个人信息，修改密码查看个人信息，修改密码查看个人信息，修改密码查看个人信息，修改密码查看个人信息，修改密码查看个人信息，修改密码查看个人信息，修改密码查看个人信息，修改密码'
          },
          {
            el: '#intro_user',
            tipPosition: 'right',
            title: '点击进入用户管理',
            content: '查看用户信息，添加用户',
            onNext: () => {
              return Promise.resolve(true)
            }
          },
          {
            el: '#intro_save',
            tipPosition: 'top',
            title: '点击进入用户管理',
            content: '查看用户信息，添加用户'
          }
        ]
      }
    }
  },
  methods: {
    closeIntro() {
      // 不需要this.show = false，因为v-model会自动更新
      console.log('关闭');
    },
  }
}
</script>
```

## 组件参数

> `v-model` 参数：控制引导步骤组件是否显示
> 
> `config` 参数：配置引导步骤组件的参数
>   - `tips` 参数：用于盛放哪些元素需要引导
>       - `el` 参数：元素的选择器，切记目前只支持id选择器
>       - `tipPosition` 参数：引导元素提示信息的位置，可选值有：`top`, `bottom`, `left`, `right`
>      - `title?` 参数：引导元素提示信息的标题
>      - `content` 参数：引导元素提示信息的内容
>     - `onNext?` 参数：引导元素提示信息点击 *下一步* 按钮时的回调函数，返回一个promise，如果返回的promise成功，则继续下一步，否则不继续下一步
> 
> `@close` 事件参数：关闭引导步骤组件时会触发的事件
