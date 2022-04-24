<template>
    <div class="scene-application-container">
        <escroll ref="scroll" scrollBehavior event :top="100" :lowerThreshold="120" @scrollstart="handScrollstart" @scrollstop="handScrollstop" @scroll="handScroll" @scrolllower="handScrolllower">
            <div class="content" v-for="n in 100" :key="n" :anchor="n" @click="$refs.scroll.anchorTo(n)">{{n}} - container [anchor:{{n}}]</div>
            <!-- <template #gotop>Go Top</template> -->
        </escroll>
    </div>
</template>

<script>
	import escroll from '@/components/escroll'
    export default {
        name: 'app',
		components: {escroll},
        data() {
            return {}
        },
        methods: {
            handScrollstart(e) { console.log('handScrollstart', e) },
            handScrollstop(e) { console.log('handScrollstop', e) },
            handScroll(e) { console.log('handScroll', e) },
            handScrolllower(e) { console.log('handScrolllower', e) },
        },
    }
</script>

<style lang="less">
    // 全局通用样式
    body, html { width: 100%; height: 100%; padding: 0; margin: 0; background-color: transparent; font-family: Verdana,arial,"Microsoft YaHei"; }
    #app, .scene-application-container{ width: 100%; height: 100%; overflow: hidden; position: relative; }
    
    .escroll .content{ padding: 10px; background-color: #EBEBEB; }
    .escroll .content:nth-child(2n+1){ background-color: transparent !important; }
</style>
