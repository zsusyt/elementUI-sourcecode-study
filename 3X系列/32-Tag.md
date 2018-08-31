```js
<template>
  <transition :name="disableTransitions ? '' : 'el-zoom-in-center'">
    <span
      class="el-tag"
      :class="[
        type ? 'el-tag--' + type : '',
        tagSize && `el-tag--${tagSize}`,
        {'is-hit': hit}
      ]"
      :style="{backgroundColor: color}">
      <slot></slot>
      <i class="el-tag__close el-icon-close"
        v-if="closable"
        @click.stop="handleClose"></i>
    </span>
  </transition>
</template>
<script>
  export default {
    name: 'ElTag',
    props: {
      text: String,
      closable: Boolean,
      type: String,
      hit: Boolean,
      disableTransitions: Boolean,
      color: String,
      size: String
    },
    methods: {
      handleClose(event) {
        this.$emit('close', event);
      }
    },
    computed: {
      tagSize() {
        return this.size || (this.$ELEMENT || {}).size;
      }
    }
  };
</script>
```
tag标签代码比较简单，可以从这个代码中了解下如何封装组件的内部事件。  
在elementUI的组件中经常可以看到类似这样的代码：
```js
this.$emit('close', event);
```
因为通用组件并不知道点击后会执行什么业务逻辑，  
所以组件只能把事件本身发出去，让业务使用方根据事件名称去执行对应的业务代码。  
这应该是封装组件常用的技巧，可以学习。
