# vue3-escroll
vue3 scroll 在 vue 中的使用

```
<template>
    <escroll ref="escroll">
        // 你的内容
    </escroll>
</template>

<script>
    import escroll from '@/components/escroll'
    export default {
        components: {escroll},
        methods: {},
    }
</script>
```

其中，相关的代码可参考 `src/App.vue`


## 配置

### event

类型：  Boolean

默认值：false

说明：  如果只是简单使用滚动，不监听滚动中的任何事件，那么建议配置为 false

```
<escroll ref="escroll" event></escroll>
```


### name

类型：  String

默认值：scroll

说明：  组件命名

```
<escroll ref="escroll" name="escroll01"></escroll>
```


### timeout

类型：  Number

默认值：3000

说明：  scrolllower 以及 scrollupper 事件的超时时间

```
<escroll ref="escroll" :timeout="3000"></escroll>
```


### lowerThreshold

类型：  Number

默认值：70

说明：  滚动条在距离底部指定距离时将触发 scrolllower 事件

```
<escroll ref="escroll" :lowerThreshold="70"></escroll>
```


### upperThreshold

类型：  Number

默认值：10

说明：  滚动条在距离顶部指定距离时将触发 scrollupper 事件

```
<escroll ref="escroll" :upperThreshold="10"></escroll>
```


### top

类型：  Number

默认值：0

说明：  滚动位置大于指定位置时，将会显示返回顶部按钮

```
<escroll ref="escroll" :top="0"></escroll>
```


### scrollBehavior

类型：  Boolean

默认值：false

说明：  是否要记住每一次的滚动位置，在组件被恢复时自动回到上一次的滚动位置

```
<escroll ref="escroll" scrollBehavior></escroll>
```


### direction

类型：  String

默认值：vertical

说明：  限制滚动方向，接受传值 “vertical” 以及 “horizontal”

```
<escroll ref="escroll" direction="vertical"></escroll>
```


### horizontal

类型：  Boolean

默认值：undefined

说明：  是否允许横向滚动

```
<escroll ref="escroll" horizontal></escroll>
```


### vertical

类型：  Boolean

默认值：undefined

说明：  是否允许竖向滚动，传入 true 或 undefined 时都是启用

```
<escroll ref="escroll" vertical></escroll>
```


### size

类型：  String

默认值：mini

说明：  滚动条尺寸，接受传值 “mini” “none” “small” 以及 “medium”

```
<escroll ref="escroll" size="mini"></escroll>
```



##  事件


### scrollstart

事件：滚动开始


### scroll

事件：滚动中


### scrollstop

事件：滚动结束

说明：滚动停止后 350ms 再无滚动动作，将视为滚动结束


### scrolllower

事件：滚动到底部


### scrollupper

事件：滚动到顶部



## 方法

```
// 滚动到 指定位置

$refs.escroll.scrollTo({top: 10})
或
$refs.escroll.smooth({top: 10})


// 滚动到顶部 、左侧
$refs.escroll.scrollUp()

// 滚动到底部 、右侧
$refs.escroll.scrollDown()


```


## 锚点

为元素添加属性节点 anchor

```
例如：<div class="content" anchor="a">锚点位置，ID = a</div>

使用方法 anchorTo 可以滚动到指定节点位置

例如
$refs.escroll.anchorTo('a')
```