el-row和el-col:
1、el-row:
没有再使用template来写dom结构，而是直接写render函数了，这可能也是向react看齐的意思，毕竟render函数可以利用js所有的能力，某些情况下表达力也更强（可以参看vuejs官方文档的解释）。
这里的知识点就是this.$slots.default了，不会的还是好好看看官方文档和api，否则后面复杂的组件根本看不懂。
一个不足之处就是这里的处理：
```js
if (this.gutter) {
        ret.marginLeft = `-${this.gutter / 2}px`;
        ret.marginRight = ret.marginLeft;
}
```

因为使用了固定的单位px，可能对移动端不太友好，不过elementUI本来也就是主要面对pc项目的。。。

---
2、el-col:
这里有个技巧：
```js
let parent = this.$parent;
      while (parent && parent.$options.componentName !== 'ElRow') {
        parent = parent.$parent;
}
```
可能很多库都是用的这个技巧来找父元素的。

这两个组件都比较简单，也没有使用其他的组件来组合成复杂的结构。