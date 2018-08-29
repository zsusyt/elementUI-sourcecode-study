switch这个组件没什么特别的地方，这篇可以讲讲一个细节：  
背景颜色。 
背景颜色主要是一个函数来控制的：
```js
setBackgroundColor() {
    let newColor = this.checked ? this.activeColor : this.inactiveColor;
    this.$refs.core.style.borderColor = newColor;
    this.$refs.core.style.backgroundColor = newColor;
},
```
只有两个地方有调用：
1、mounted
2、watch  checked这个属性中
mounted是初始化，后续所有操作中，都不会单独控制背景颜色，而是把背景颜色归结为checked的状态，
状态变了，背景颜色才变，状态不变，背景不变。

这样做的好处也是显而易见的，那就是完全无需关系背景颜色了，一切都由checked“代理”了，  
这其实跟vuejs数据驱动的思路是一样的。

另外在这个函数中的注释值得说下：
```js
handleChange(event) {
    this.$emit('input', !this.checked ? this.activeValue : this.inactiveValue);
    this.$emit('change', !this.checked ? this.activeValue : this.inactiveValue);
    this.$nextTick(() => {
        // set input's checked property
        // in case parent refuses to change component's value
        this.$refs.input.checked = this.checked;
    });
},
```
in case parent refuses to change component's value  
就是这句，意思是以防父组件不允许改变状态，才要使用nextTick。
