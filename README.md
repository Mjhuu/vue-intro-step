## vue-intro-step

### 请注意！！！这里是基于vue2版本的组件库

> 基于vue2的系统步骤引导组件。
> 更加便捷的操作步骤引导。
> vue3版本的步骤引导组件请移步 [vue3-intro-step](https://www.npmjs.com/package/vue3-intro-step)

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
            el: '#intro_title',
            tipPosition: 'bottom',
            title: '欢迎使用问答管理系统',
            content: '点击左侧菜单进行操作',
            onNext: () => {
              return new Promise((resolve, reject) => {
                // 延迟2秒 再执行下一步
                setTimeout(() => {
                  resolve(true);
                }, 2000);
              });
            }
          },
          {
            el: '#intro_mine',
            tipPosition: 'left',
            // title: '点击进入个人中心',
            content: '查看个人信息，修改密码查看个人信息，修改密码查看个人信息，修改密码查看个人信息，修改密码查看个人信息，修改密码查看个人信息，修改密码查看个人信息，修改密码查看个人信息，修改密码',
            // 点击上一步时，触发的事件
            onPrev: () => {
              return new Promise((resolve) => {
                resolve(true);
              });
            },
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

### 自定义底部按钮

```vue
<template>
  <vue-intro-step v-model="show" :config="config" ref="myIntroStep">
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
</template>

<script >
export default {
  methods: {
    done(){
      this.show = false;
    },
    next(tipItem){
      // tipItem当前的提示项信息
      // 调用vue-intro-step的next方法 手动触发下一步
      this.$refs.myIntroStep.next()
      console.log(tipItem);
    },
    prev(tipItem, currentIndex){

      // 调用vue-intro-step的prev方法 手动触发上一步
      this.$refs.myIntroStep.prev()
      console.log(tipItem, currentIndex);
    }
  }
}
</script>
```

## 组件参数

> `v-model` 参数：控制步骤引导组件是否显示
>
> `config` 参数：配置步骤引导组件的参数
>   - `backgroundOpacity?` 参数：步骤引导组件的背景透明度，默认值为0.9，取值范围0-1
>  - `titleStyle?` 参数：步骤引导组件的标题样式
>     - `textAlign?` 参数：标题文字的居中样式，默认值为 `center`，可选值有：`left`, `center`, `right`
>     - `fontSize?` 参数：标题文字的字体大小样式
>  - `contentStyle?` 参数：步骤引导组件的内容样式
>     - `textAlign?` 参数：内容文字的居中样式，默认值为 `center`，可选值有：`left`, `center`, `right`
>     - `fontSize?` 参数：内容文字的字体大小样式
>  - `tips` 参数：用于盛放哪些元素需要引导
>      - `el` 参数：元素的选择器，切记目前只支持id选择器
>      - `tipPosition` 参数：引导元素提示信息的位置，可选值有：`top`, `bottom`, `left`, `right`
>     - `title?` 参数：引导元素提示信息的标题
>     - `content` 参数：引导元素提示信息的内容
>     - `onNext?` 参数：引导元素提示信息点击 *下一步* 按钮时的回调函数，返回一个promise，如果返回的promise成功，则继续下一步，否则不继续下一步
>     - `onPrev?` 参数：引导元素提示信息点击 *上一步* 按钮时的回调函数，返回一个promise，如果返回的promise成功，则继续上一步，否则不继续上一步
>
> `@close` 事件参数：关闭步骤引导组件时会触发的事件
