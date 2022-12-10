---
title: Vue 全局事件总线 $bus
date: 2022-12-07 15:44:32
tags: 事件总线
---

全局事件总线

# 全局事件总线
*它也是组件通信的一种方式，可以用于实现任意组件间通信。*
# 安装全局事件总线
在main.js中：

```typescript
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
  el: '#app',
  render: h => h(App),
  //安装全局事件总线
  beforeCreate() {
    Vue.prototype.$bus = this
  }
})

```

# 使用全局事件总线
（1）数据接收方要给 $bus 绑定自定义事件，数据接收方还应该要有该事件的回调（即自定义事件发生后，接收方该怎么做）

（2）数据发送方负责触发这个自定义事件，并捎带数据过去

# 例子与代码
## 例子
![在这里插入图片描述](https://img-blog.csdnimg.cn/8ee6b9f366d4486b880adbec1af03c10.png#pic_center)
## 代码
**接收方 Son.vue**
```typescript
<template>
  <div>
      <h3>我是 {{name}} 组件</h3>
      <h3>我今年 {{age}} 岁了</h3>
      <h3>我uncle是 {{uncleName ? uncleName : '不知道'}} 组件</h3>
      <h3>我uncle今年 {{uncleAge ? uncleAge : '不知道多少'}} 岁了</h3>
  </div>
</template>

<script>
  export default {
    name: 'Son',
    data() {
      return {
        name: 'son',
        age: 6,
        uncleName: '',
        uncleAge: 0
      }
    },
    methods: {
      showUncle(name,age) {
        this.uncleName = name
        this.uncleAge = age
        //console.log('@@@',name,age);
      }
    },
    mounted() {
    //给$bus绑定自定义事件与它的回调
      this.$bus.$on('sendToSon',this.showUncle)
    },
    //最好给$bus解绑用不到的自定义事件
    beforeDestroy() {
      console.log('will be destroyed'); 
      this.$bus.$off('sendToSon')
    }
  }
</script>
```
**发送方 Uncle.vue**

```typescript
<template>
  <div>
      <h3>我是 {{name}} 组件</h3>
      <h3>我今年 {{age}} 岁了</h3>
  </div>
</template>

<script>
  export default {
    name: 'Uncle',
    data() {
      return {
        name: 'uncle',
        age: 36
      }
    },
    mounted() {
    //触发自定义事件
      this.$bus.$emit('sendToSon',this.name,this.age)
    }
  }
</script>
```
## 效果
![在这里插入图片描述](https://img-blog.csdnimg.cn/3b2cc4f027964fc3b788d474ba96fb5e.png#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/0e8573999e0644549076ed511646a0c1.jpeg#pic_center)

