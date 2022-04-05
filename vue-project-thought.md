## Vue 项目工程 -- 战斗思维与技巧

有库用库，有便捷函数用便捷函数，有规范用用规范。———— Me


## 目录

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [纯组件](#%E7%BA%AF%E7%BB%84%E4%BB%B6)
  - [示例](#%E7%A4%BA%E4%BE%8B)
- [数据的展示与修改](#%E6%95%B0%E6%8D%AE%E7%9A%84%E5%B1%95%E7%A4%BA%E4%B8%8E%E4%BF%AE%E6%94%B9)
  - [大对象的数组](#%E5%A4%A7%E5%AF%B9%E8%B1%A1%E7%9A%84%E6%95%B0%E7%BB%84)
    - [示例](#%E7%A4%BA%E4%BE%8B-1)
- [书写 变体组件，应该用 `<component :is="name" />`](#%E4%B9%A6%E5%86%99-%E5%8F%98%E4%BD%93%E7%BB%84%E4%BB%B6%E5%BA%94%E8%AF%A5%E7%94%A8-component-isname-)
  - [示例](#%E7%A4%BA%E4%BE%8B-2)
  - [Mixin](#mixin)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->


## 纯组件

认清楚纯组件，是一件相当重要的事情。因为它可以让你辨别**业务**与**表现UI**的分别。

在我的认知里面，不带有**请求业务API**的组件，就是纯组件，

- 它会是一个叶子。
- 它应该放在 Components 文件夹下，
- 甚至说，常用的组件库，本身的每一组件，都可以看作纯组件。
- 按钮就是一个按钮，它的属性`:value`和方法`@click`，都是由调用它的组件来完成。

### 示例

将 `Son` 比作一个按钮


``` js
// Father.vue
<Son :value="value" @click="doSomething">

const value = ref(0)
const doSomething = (fromSon) => {
  // 处理来自 Son 的 click 事件的回调
}
```

> 为什么，我会从纯组件的角度出发，感觉我是在讲废话的, VUE 的作用不就是可以写组件吗，然后重用吗，谁不知道呢。

<!-- TODO: 解答 2022-4-3 为什么呢 -->

## 数据的展示与修改

### 大对象的数组


#### 示例

举个栗子：人员对象的数组。

```js
const personList = [{
  name: '张',
  age: 15,
  //...
  height: 177,
  // or
  info: {
    position: "地址",
    father: "Id",
    mother: "Id"
  }
}]
```

## 书写 变体组件，应该用 `<component :is="name" />`

### 示例

我们把题目的类型列出：

```ts
const types = ['单选题', '多选题', '填空题', '问答题',' 连线题', 
              '自定义题'
              // 等等
              ]
```

根据传递的参数 `typeID`，应用不同的组件

-  **Question.vue**
-  
``` js

<script>
/* 传入，题目类型，与题目的列表 */
props: ['typeID', 'questions']
components: {
  Single: () => import("path/to/Single"),
  Muliple: () => import("path/to/Muliple"),
  Fillin: () => import("path/to/Fillin"),
  QandA: () => import("path/to/QandA"),
  // ...
}
methods: {
  Which(componentName) {
    if(this.convertIdToName(this.typeID) == componentName)
  },
  convertIdToName(typeID) {
    // TODO: 将 类型常量id 转换 名称
    // return Name
  }
}
</script>

<template>

<div>
  <!-- 组件集合 -->
  <template v-for="(componentName) in ['Single','Muliple','Fillin','QandA', // ...]" 
  >
  /* 判断是哪个组件 */
    <template v-if="Which(componentName)">
    /* 以及使用 */
    <component v-for="(question, idx) in questions"  
      :is="componentName" :key="componentName + idx" 
      :question="question" />
    </template>
  </template>
</div>
</template>

```

### Mixin

我们要明白，`['Single','Muliple','Fillin','QandA',//...]` 这些个兄弟组件，应该具有相同的 **props**。(`question`)接口方面也应该统一，至于它们的差异，应在组件内部消化。

- 也就意味着，它们会有个 兄弟级别的 Mixin 组件，定义 props 与通用方法。

- **Single.vue**

``` js
<script>
import setupQuestionMixin from "path/to/questionMixin"
export default {
  name: "Single",
  mixins: ['setupQuestionMixin']
}
</script>
```
- **Muliple.vue**

``` js
// .vue
<script>
import setupQuestionMixin from "path/to/questionMixin"
export default {
  name: "Muliple",
  mixins: ['setupQuestionMixin']
}
</script>
```
- **questionMixin.vue**

```js
export default {
  name: "questionMixin",
  props: {
    question: {
      type: Object,
      required: true
    }
  },
  methods: {
    // ...
  }
}
```