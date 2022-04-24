<template>
    <div class="escroll escroll-container" ref="escroll" :class="className">
        <slot />
    </div>
    <div class="escroll-gotop-container" v-if="isGoTop" @click.stop="scrollUp">
        <slot name="gotop"><button index="-1" class="totop" tips="TOP" /></slot>
    </div>
</template>

<script>
    import { onMounted, onBeforeUnmount, ref, reactive } from 'vue';
    export default {
        name: 'escroll',
        emits: ['scrollstart', 'scroll', 'scrolllower', 'scrollupper', 'scrollstop', 'scrollrecovery'],
        props: {
            event: {type: [Boolean], default: false },
            name: {type: [String], default: 'scroll' },
            timeout: { type: [Number], default: 3000 },
            lowerThreshold: { type: [Number], default: 70 },
            upperThreshold: { type: [Number], default: 10 },
            scrollBehavior: {type: [Boolean], default: false },
            direction: { type: [String], default: 'vertical' },
            horizontal: { type: [Boolean], default: undefined },
            vertical: { type: [Boolean], default: undefined },
            size: { type: [String], default: 'mini' },
            top: { type: [Number], default: 0 },
        },
        computed: {
            isGoTop() { return this.options.scrollTop > this.top && this.options.isGoTop },
            className() {
                let rows = []
                ;(this.vertical || this.vertical === undefined) && rows.push('escroll--direction--vertical')
                ;(this.horizontal) && rows.push('escroll--direction--horizontal')
                ;(this.direction) && rows.push('escroll--direction--fixed--' + this.direction)
                ;(this.options.isNormalcy) && rows.push('escroll--normalcy')
                ;(this.size) && rows.push('escroll--size--' + this.size)
                return rows
            }
        },
        setup(props, context) {
            // Attribute (非响应式对象，等同于 $attrs)
            let escroll = ref(null)
            let options = reactive({ scrollTop: 0, isNormalcy: false, isGoTop: context.slots.gotop || props.top > 0 ? true : false })
            let timeout = parseInt(props.timeout, 10) || 3000
            let option = { ticking: false, isScrollstart: true, islower: false, isupper: false, timerScrollstop: null, release: true }
            let data   = { direction: props.direction, code: 0, left: 0, top: 0, total: 0, name: 'escroll:' + (props.name || 'scroll') }
            onMounted(() => {
                escroll.value.addEventListener('scroll', function(e) {
                    let target = e.srcElement ? e.srcElement : e.target
                    options.scrollTop = target.scrollTop
                    if (!props.event || option.ticking) { return null } // 停止滚动事件监听
                    clearTimeout(option.timerScrollstop)
                    option.ticking = true
                    requestAnimationFrame(function(){ /*rAF 触发锁*/
                        Object.assign(data, { total: data.total+1, code: data.left < target.scrollLeft || data.top < target.scrollTop ? 1 : 0, left: target.scrollLeft, top: target.scrollTop })
                        if (option.isScrollstart) {
                            option.isScrollstart = false
                            context.emit('scrollstart', data, 'scrollstart')
                        }
                        option.ticking = false
                        context.emit('scroll', data, 'scroll')
                        if (props.lowerThreshold && data.code) {
                            let threshold = target.scrollHeight - (data.top + target.offsetHeight)
                            if (threshold <= props.lowerThreshold && !option.islower) {
                                option.islower = true
                                context.emit('scrolllower', data, 'scrolllower', () => { option.islower = false })
                                setTimeout(() => { option.islower = false }, timeout)
                            }
                        } else if (!data.code && props.upperThreshold && props.upperThreshold >= data.top && !option.isupper) {
                            option.isupper = true
                            context.emit('scrollupper', data, 'scrollupper', () => { option.isupper = false })
                            setTimeout(() => { option.isupper = false }, timeout)
                        }
                        option.timerScrollstop = setTimeout(() => {
                            option.isScrollstart = true
                            options.isNormalcy = true
                            context.emit('scrollstop', data, 'scrollstop')
                            sessionStorage.setItem(data.name + '-y', data.top);
                            sessionStorage.setItem(data.name + '-x', data.left);
                        }, 350)
                    })
                }, true)

                if (props.scrollBehavior) {
                    let y = parseFloat(sessionStorage.getItem(data.name + '-y')) || 0;
                    let x = parseFloat(sessionStorage.getItem(data.name + '-x')) || 0;
                    options.isNormalcy = false
                    scrollTo({top: y, left: x})
                }
            })
            onBeforeUnmount(() => {})

            function anchorPlace(target, offset) {
                if (target && target !== escroll.value && target.offsetParent) {
                    offset.top  += parseInt(target.offsetTop, 10) || 0
                    offset.left += parseInt(target.offsetLeft, 10) || 0
                    return anchorPlace(target.offsetParent, offset)
                }
                return offset
            }

            function scrollTo(value) { escroll.value.scrollTo(value) }
            function smooth(value) { scrollTo({ ...value, behavior: 'smooth' }) }
            function scrollUp() { smooth({ top: 0, left: 0 }) }
            function scrollDown() { smooth({ top: escroll.value.scrollHeight, left: escroll.value.scrollWidth }) }
            function anchorTo(name) {
                let anchor = escroll.value.querySelector(`[anchor="${name}"]`)
                anchor && smooth(anchorPlace(anchor, {top: 0, left: 0}))
            }
            context.expose({ anchorTo, scrollUp, scrollDown, smooth, scrollTo })
            // console.log('props', props)
            // console.log(context.attrs)
            // // 插槽 (非响应式对象，等同于 $slots)
            // console.log(context.slots)
            // // 触发事件 (方法，等同于 $emit)
            // console.log(context.emit)
            // // 暴露公共 property (函数)
            // console.log(context.expose)
            return { escroll, options, scrollUp }
        },
        methods: {}
    }
</script>

<style scoped lang="less">
    .escroll-gotop-container{
        position: absolute; right: 20px; bottom: 20px; z-index: 1999; overflow: hidden;
        background-color: transparent; border: 0; cursor: pointer; transition: all 1s;
        button.totop{
            width: 36px; font-size: 12px; text-align: center; color: #333; background-color: transparent; border: 0;
            padding: 0; margin: 0; outline: none; pointer-events: none;
            &::after, &::before{ display: block; pointer-events: none; }
            &::after{ content: attr(tips); font-weight: bold;  background-color: rgba(255, 255, 255, 0.5); padding: 4px 0; }
            &::before{
                content: ''; width: 0; height: 0; margin-top: -8px;
                border-width: 8px 18px; border-color: transparent transparent #333 transparent; border-style: solid;
            }
        }
    }
    .escroll.escroll-container{
        width: 100%; height: 100%; padding: 0; margin: 0; box-sizing: border-box; position: relative; overflow: hidden; z-index: 1;
        &::-webkit-scrollbar { width: 10px; height: 10px; }
        &::-webkit-scrollbar-track { background: transparent!important; }
        &::-webkit-scrollbar-corner { background: transparent!important; }
        &::-webkit-scrollbar-button { width: 0; height: 0; background: transparent!important; }
        &::-webkit-scrollbar-thumb{ background: #909399!important; }
        
        // 滚动条尺寸
        &.escroll--size--medium::-webkit-scrollbar { width: 10px; height: 10px; background: transparent!important; }
        &.escroll--size--small::-webkit-scrollbar { width: 6px; height: 6px; background: transparent!important; }
        &.escroll--size--mini::-webkit-scrollbar { width: 4px; height: 4px; background: transparent!important; }
        &.escroll--size--none::-webkit-scrollbar { width: 0px; height: 0px; background: transparent!important; }
        
        // 滚动类型
        &.escroll--normalcy{ scroll-behavior: smooth; -webkit-overflow-scrolling: touch; overflow-scrolling: touch; }
        &.escroll--direction--horizontal{ overflow-x: auto!important; }
        &.escroll--direction--vertical{ overflow-y: auto!important; }
        
        &.escroll--direction--fixed--horizontal{ justify-content: space-between; white-space:nowrap; }
    }
</style>