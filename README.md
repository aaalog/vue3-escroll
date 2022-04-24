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


## 可用方法

```
// 滚动到 指定位置
$refs.escroll.scrollTo({top: 10})
--- 一般与以下方法等效
$refs.escroll.smooth({top: 10})

```