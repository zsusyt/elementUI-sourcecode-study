el-container, el-main, el-header, el-footer, el-aside:
这些组件还没有用render函数改写，不过template模板里面的东西本来就不多，也没必要了吧。

el-container:
主要就是对方向的判断，有个isVertical的计算属性，代码不难。

---
el-header:
默认60px高

---
el-footer:
默认60px高

---
el-aside:
默认300px宽

---
el-main：
里面几乎没啥东西，就是个空壳子(<slot></slot>)

---
el-icon:
靠传入的name拼接成'el-icon-' + name这样的class名，所以直接写class也一样，就是有点违背data-driven的目标了。
