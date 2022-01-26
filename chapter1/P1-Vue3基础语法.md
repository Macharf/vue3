## 一、Vue3简介

Vue (读音 /vjuː/，类似于 **view**) 是一套用于构建用户界面的**渐进式框架**。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与[现代化的工具链](https://v3.cn.vuejs.org/guide/single-file-component.html)以及各种[支持类库](https://github.com/vuejs/awesome-vue#components--libraries)结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。

> 注意：
>
> 1、一般了解Vue3的同学都是已经有Vue2的相关开发与学习经验，因此本套教程中有部分如果你已经懂了的话，可以选择绕过。为了照顾绝大部分学员，本教程依然会从零开始帮助大家掌握Vue3。
>
> 2、有React基础的同学学习Vue3将会更加容易理解。

## 二、脚手架安装与项目创建

### 1、全局安装脚手架

在本机上全局安装Vue脚手架：

```shell
$ yarn global add @vue/cli
# 或
$ npm install -g @vue/cli
```

然后在 Vue 项目中运行：

```shell
$ vue upgrade --next
```

### 2、Vite创建项目

[Vite](https://cn.vitejs.dev/) 是一个 web 开发构建工具，由于其原生 ES 模块导入方式，可以实现闪电般的冷服务器启动。通过在终端中运行以下命令，可以使用 Vite 快速构建 Vue 项目。

使用 npm：

```shell
# npm 6.x
$ npm init vite@latest <project-name> --template vue

# npm 7+，需要加上额外的双短横线
$ npm init vite@latest <project-name> -- --template vue

$ cd <project-name>
$ npm install
$ npm run dev
```

或者 yarn：

```shell
$ yarn create vite <project-name> --template vue
$ cd <project-name>
$ yarn
$ yarn dev
```

## 三、VsCode扩展安装

在做Vue脚手架开发时，需要一些语法提示等，可以在VsCode搜索并安装 `Volar` 扩展：

![image-20220126204925653](https://tva1.sinaimg.cn/large/008i3skNgy1gyrd5qttsfj31d60u0wja.jpg)

## 四、数据渲染方式

项目运行正常后，删除 `HelloWorld.vue`，并删除 `App.vue` 中对它的引用。在 `components` 下新建 `Test1.vue`，敲下基本模板：

```vue
<template>
    
</template>
 
<script>
export default {
    
}
</script>
 
<style scoped>
</style>
```

随后在 `App.vue` 中引入并使用：

```vue
<script setup>
import Test1 from './components/Test1.vue'
</script>

<template>
  <Test1 />
</template>
```

> 注意：
>
> 1、script标签上的setup属性后文会提及，可以先忽略它；
>
> 2、Test1不需要像Vue2一样在components下注册后才能使用，Vue3允许你直接引入就使用；

### 1、模板语法

当我们需要在页面中显示一些文字，并且这些文字是可以修改的，就可以在 `export default {}` 中使用data函数来声明字段，并且使用 `{{}}` 来显示内容：

```vue
<template>
    <div>{{msg}}</div>
</template>
 
<script>
export default {
    data() {
        return {
            msg: "你好世界"
        }
    }
}
</script>
```

### 2、v-html与v-text指令

以上的模板语法其实也可以使用指令来完成。所谓的指令可以暂时理解为标签属性，实际上它也是标签属性：

```vue
<!-- 以下三种方式的写法效果一致 -->
<template>
    <div>{{msg}}</div>
    <div v-html="msg"></div>
    <div v-text="msg"></div>
</template>
```

> 注意：
>
> template下不再需要根标签，这与Vue2稍有不同。

既然 `v-html` 和 `v-text` 相同，为什么需要两个不同的指令呢？回答：二者当然是不一样的，`v-html` 可以解析标签到浏览器中，而 `v-text` 只能将标签名字直接作为文本内容显示在浏览器中：

```vue
<template>
    <div>{{msg}}</div>
    <div v-html="msg"></div>
    <div v-text="msg"></div>
</template>
 
<script>
export default {
    data() {
        return {
            msg: `<li>你好世界</li>`
        }
    }
}
</script>
```

出来的效果如图：

![image-20220126210107148](https://tva1.sinaimg.cn/large/008i3skNgy1gyrdhteoecj30ai05at8r.jpg)

因此，可以看出，`v-text` 与模板语法是相同的，而 `v-html` 在解析标签上，还是不一样的。

> 注意：
>
> 如果前端接收了后端的富文本内容，一般就需要使用v-html来渲染了。

## 五、事件

如果希望有一个按钮可以修改msg，那么可以给按钮添加一个事件：

```vue
<template>
    <div>{{msg}}</div>
		<!-- @是v-on指令的简写，完整的写法为：v-on:click -->
    <button @click="changeMsg">修改msg</button>
</template>
 
<script>
export default {
    data() {
        return {
            msg: `你好世界`
        }
    },
    methods: {
      	// 按钮点击事件
        changeMsg(){
            this.msg = 'Hello World'
        }
    }
}
</script>
```

学到这里，部分接触过Vue2的同学会觉得这与Vue2也没什么区别呀！

确实，Vue3本来也不是完全独立于Vue2的一个版本，接下来就要使用Vue3的组合式Api来实现上面这个功能了！

## 六、组合式API

### 1、为什么是组合式API？

单个组件代码的碎片化，会造成理解和维护复杂组件变得困难。选项的分离掩盖了潜在的逻辑问题。此外，在处理单个逻辑关注点时，我们必须不断地“跳转”相关代码的选项块。如果能够将同一个逻辑关注点相关代码收集在一起会更好。而这正是组合式 API 使我们能够做到的。

### 2、Ref

在 Vue 3.0 中，我们可以通过一个新的 `ref` 函数使任何响应式变量在任何地方起作用：

```vue
<script>
import { ref } from 'vue'
const msg = ref('你好世界');
console.log(msg.value)  // 你好世界
</script>
```

`ref` 为我们的值创建了一个**响应式引用**，在整个组合式 API 中会经常使用**引用**的概念。

> 注意：
>
> 将值封装在一个对象中，看似没有必要，但为了保持 JavaScript 中不同数据类型的行为统一，这是必须的。这是因为在 JavaScript 中，`Number` 或 `String` 等基本类型是通过值而非引用传递的。在任何值周围都有一个封装对象，这样我们就可以在整个应用中安全地传递它，而不必担心在某个地方失去它的响应性。

![](https://blog.penjee.com/wp-content/uploads/2015/02/pass-by-reference-vs-pass-by-value-animation.gif)

### 3、Setup

组合式API只能在Setup方法中使用。`setup` 选项是一个接收 `props` 和 `context` 的函数，我们将 `setup` 返回的所有内容都暴露给组件的其余部分 (计算属性、方法、生命周期钩子等等) 以及组件的模板。

> 在 `setup` 中你应该避免使用 `this`，因为它不会找到组件实例。`setup` 的调用发生在 `data` 、`computed`  或 `methods` 被解析之前，所以它们无法在 `setup` 中被获取。

将 `Test1.vue` 中的代码改为：

```vue

```





















