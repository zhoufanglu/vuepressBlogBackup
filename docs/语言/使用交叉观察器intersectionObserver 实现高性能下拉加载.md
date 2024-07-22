---
title: 使用交叉观察器intersectionObserver 实现高性能下拉加载
autoGroup-javascript: javascript  
categories: 语言
date: 2024-03-21
tags: [javascript]
---
# 原文地址 
> 掘金上的图片访问不了，做了限制，建议直接看原文。  
[戳这里](https://juejin.cn/post/7348384849404100660)
# 背景
> 其实网上大多都是监听scroll进行实现的，确实可以，偶然发现有个[intersectionObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/Intersection_Observer_API) api, 然后就试试如何实现下拉刷新。
# 1、对比`intersectionObserver`与监听`scroll`性能
> 目前测试的是下拉加载`300`条数据

### 1-1、chrome-performance测试
>我们利用`chrome`的`performance` 测试下， 。
* `intersectionObserver监听` 的 performance

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a2063c358dc34a55aaadf541b598e2d9~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=2064&h=696&s=78010&e=png&b=ffffff)
* `scroll监听 ` 的 performance


![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/45cc8f1eb3d74364b8f3f087c62afca5~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1618&h=728&s=80267&e=png&b=fefefe)
---

### 1-2、数据转换为表格对比下
* 300条数据对比

|  下拉加载方式   | JS Heap  | Documents| Listeners|
|  ----  | ----  |----|----|
| `scroll监听 `  | `12.5MB - 13.6MB` |4-4|94|
| `intersectionObserver监听`  | `6.3MB - 7.4MB` |1-2|50|  

> 使用`intersectionObserver`监听 ， 我们可以看到内存只占用了`scroll`的50%, 这还是`300`条数据的情况下，这性能差距肯定是指数级拉开的，所以用哪个就不言而喻了。
>
> 这时候，如果你抛出，`又不是不能跑`， 我只能回答`确实~`。

# 2、intersectionObserver介绍
* [intersectionObserver-文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Intersection_Observer_API)
* **说明了使用场景**
* -   在页面滚动时“懒加载”图像或其他内容。
* -   实现“无限滚动”网站，在滚动过程中加载和显示越来越多的内容，这样用户就不必翻页了。
* -   报告广告的可见度，以便计算广告收入。
* -   根据用户是否能看到结果来决定是否执行任务或动画进程。

### 2-1、思路
> 思路 我们只要监听 列表最下面的dom， 判断他在不在视图内， 若在，就加载更多.

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/834c7b28299f44af9fa4c713b1d48ac9~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=1684&h=1464&s=134048&e=png&b=ffffff)

### 2-2、代码
```js
<script setup lang="ts">
  const requestList = ref<any>(new Array(10).fill(0).map((_, i) => i + 1))
  const loadList = ref<any>([])
  const observerElement = ref<HTMLElement | null>(null)
  onMounted(() => {
    let observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          /**
           * isIntersecting
           * 0 表示目标元素完全不可见。
           * 1 表示目标元素完全可见。
           * 0~1 表示目标元素部分可见
           */
          console.log(15, entry.intersectionRatio)
          if (entry.intersectionRatio >= 0) {
            console.log('进入可视区域')
            loadMore()
          } else {
            console.log('不可见')
          }
        })
      },
      {
        threshold: 0,
      },
    )
    observer.observe(observerElement.value!)
  })

  const loadMore = () => {
    console.log('------')
    if (loadList.value.length >= 100) {
      console.log('已经满100条了')
      return
    }
    loadList.value.push(...requestList.value)
  }
</script>
<template>
  <div class="p-scroll">
    <!--?列表区域-->
    <div v-for="(i, index) in loadList" :key="index" class="card">{{ i }} -- {{ index }}</div>
    <!--?IntersectionObserver监听的对象-->
    <div ref="observerElement" class="observer-element">监听的dom</div>
  </div>
</template>

<style scoped lang="scss">
  .p-scroll {
    border: solid 1px red;
    overflow-y: auto;
    display: flex;
    flex-direction: column;
    padding: 50px;
    box-sizing: border-box;
    .card {
      height: 200px;
      border: solid 1px blue;
      display: flex;
      align-items: center;
      justify-content: center;
      //@include vertical-center;
      font-size: 30px;
      // transform: translateX(100px);
      transition: 500ms;
      // opacity: 0;
      margin: 6px 0;
    }
    .show {
      transform: translateX(0);
      opacity: 1;
    }
    .observer-element {
      border: solid 1px green;
      background: green;
      color: white;
      font-size: 20px;
      height: 100px;
    }
  }
</style>
```

# 3、监听scroll的实现
> 推荐使用`intersectionObserver`.
### 监听`scroll`,下拉刷新
```js
<script setup lang="ts">
  const requestList = ref<any>(new Array(10).fill(0).map((_, i) => i + 1))
  const loadList = ref<any>([])

  /** ********************滚动监视器***********************/
  const loadMore = () => {
    console.log('------')
    if (loadList.value.length >= 100) {
      console.log('已经满100条了')
      return
    }
    loadList.value.push(...requestList.value)
  }

  loadMore()

  // ?开始观察滚动触发元素
  const contentRef = ref()
  const handleScroll = () => {
    const container = contentRef.value
    // 判断是否滚动到底部
    if (container.scrollTop + container.clientHeight >= container.scrollHeight) {
      loadMore()
    }
  }
</script>
<template>
  <div ref="contentRef" class="p-scroll" @scroll="handleScroll">
    <div v-for="(i, index) in loadList" :key="index" class="card">{{ i }} -- {{ index }}</div>
  </div>
</template>
```

# 总结
> 以下内容参考自`chartgpt`
* IntersectionObserver 相比监听 scroll 事件，在性能上通常`更高效的原因`有几点：
1.  **异步执行：** IntersectionObserver 是异步执行的，它使用回调函数在元素进入或离开视口时触发，而不是像监听 scroll 事件一样在每次滚动时都触发。这样可以减少回调函数的执行次数，降低了性能开销。
1.  **硬件加速：** IntersectionObserver 使用浏览器底层的硬件加速技术来进行计算，因此在性能上更为高效。而监听 scroll 事件则需要频繁地进行 DOM 查询和计算，性能消耗较大。
1.  **支持懒加载：** IntersectionObserver 更适合实现懒加载功能，可以精确地监听元素是否进入视口，从而实现按需加载。而监听 scroll 事件则需要手动计算元素的位置，实现起来相对复杂并且性能较差。
