```js
function broadcast(componentName, eventName, params) {
  this.$children.forEach(child => {
    var name = child.$options.componentName;

    if (name === componentName) {
      child.$emit.apply(child, [eventName].concat(params));
    } else {
      broadcast.apply(child, [componentName, eventName].concat([params]));
    }
  });
}
export default {
  methods: {
    dispatch(componentName, eventName, params) {
      var parent = this.$parent || this.$root;
      var name = parent.$options.componentName;

      while (parent && (!name || name !== componentName)) {
        parent = parent.$parent;

        if (parent) {
          name = parent.$options.componentName;
        }
      }
      if (parent) {
        parent.$emit.apply(parent, [eventName].concat(params));
      }
    },
    broadcast(componentName, eventName, params) {
      broadcast.call(this, componentName, eventName, params);
    }
  }
};
```
这部分代码都是用来mixin的，所以mixin之后，组件内会多两个方法：
* dispatch
* broadcast

因为mixin到组件内部了，所以这里面的this都指的是vm 。  
先看dispatch：
while内部代码的意图是，找到一个父组件，他的componentName是我们指定的name，
如果可以找到这个组件，就在这个组件上$emit一个指定事件；
如果找不到，就不触发事件（比如在根组件上parent = parent.$parent就会导致这个parent变成undefined）。

再看broadcast：
在组件的children中找到指定componentName的子组件，如果可以找到，就在这个子组件上触发指定事件；
如果找不到，在子组件上递归调用broadcast，如果最终还是没有找到，就什么也不发。

这里的向上以及向下发消息的技巧，在其他地方也很实用，如果自己要基于vuejs写ui框架，也可以参考。
