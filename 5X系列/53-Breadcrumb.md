通过separator和separatorClass来控制外观，其中separator是字符,separatorClass可以用Icon。

breadcrumb通过provide把自身传给breadcrumb-item,这也是provide/inject这一对api的常用场景了。

在breadcrumb的mounted中还是对子元素做了不少控制，一定意义上是耦合了，不够都是基于一些固定的值，还好。

---
在breadcrumb中可以看到，separatorClass是优先于separator的。
```html
    <i v-if="separatorClass" class="el-breadcrumb__separator" :class="separatorClass"></i>
    <span v-else class="el-breadcrumb__separator" role="presentation">{{separator}}</span>
  </span>
```

通过$link来做dom操作。