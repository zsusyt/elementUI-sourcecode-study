radio mixin了Emitter，也就是具有了向上或者向下发消息的能力，关于Emitter的分析可以看[这里](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/0X%E7%B3%BB%E5%88%97/02-Emitter.md)

radio有很多与外观有关的代码，也逻辑有关的代码最主要就是这段：
```js
handleChange() {
    this.$nextTick(() => {
        this.$emit('change', this.model);
        this.isGroup && this.dispatch('ElRadioGroup', 'handleChange', this.model);
    });
}
```
本身会向父组件发一个消息，同时又会向ELRadioGroup发一个消息(即使没有)。
如果是业务代码，是不用这么写的，因为你知道radio是否处于一个ElRadioGroup中，但是作为框架代码，不可能知道具体使用场景的信息，
所以一定要这么写。

注意这里使用了$nextTick这个api，这里面为什么不直接发消息，而是要nextTick呢？
README.md中提到的掘金上的小姐姐也提出了这个问题，但是她没有给出解答。
我们看看model相关代码：
```js
model: {
    get() {
        return this.isGroup ? this._radioGroup.value : this.value;
    },
    set(val) {
        if (this.isGroup) {
        this.dispatch('ElRadioGroup', 'input', [val]);
        } else {
        this.$emit('input', val);
        }
    }
},
```
我们想想这个过程：如果是radioGroup的情况，必然是各种消息，各种异步，肯定是不会马上拿到点击的这个radio的值的；
那么如果不在radioGroup中，而只是几个radio呢？这样仍然是通过异步来更新值的，因为v-model本身只是语法糖：
> 自定义事件也可以用于创建支持 v-model 的自定义输入组件。记住：
> 
>```js
> <input v-model="searchText">
>```
> 等价于：
> ```js
> <input
>   v-bind:value="searchText"
>   v-on:input="searchText = $event.target.value"
> >
>```
点击radio之后，先发一个消息：this.$emit('input', val);
这个消息发出去之后，父组件响应这个消息之前，再发这个消息的时候：this.$emit('change', this.model);
this.model还是老的值，所以就需要nextTick来加入队列，等上述异步流程执行完，再取this.model，就可以
拿到真正想要的值了。

---
radioGroup也mixin了Emitter，所以他也可以向上或者向下发送消息。  
首先看看这段代码：
```js
created() {
    this.$on('handleChange', value => {
    this.$emit('change', value);
    });
},
```
是把子组件radio的change事件接力传递上去。  

```js
mounted() {
    // 当radioGroup没有默认选项时，第一个可以选中Tab导航
    const radios = this.$el.querySelectorAll('[type=radio]');
    const firstLabel = this.$el.querySelectorAll('[role=radio]')[0];
    if (![].some.call(radios, radio => radio.checked) && firstLabel) {
    firstLabel.tabIndex = 0;
    }
},
```
mounted这部分代码的作用参看注释即可。

```js
handleKeydown(e) { // 左右上下按键 可以在radio组内切换不同选项
    const target = e.target;
    const className = target.nodeName === 'INPUT' ? '[type=radio]' : '[role=radio]';
    const radios = this.$el.querySelectorAll(className);
    const length = radios.length;
    const index = [].indexOf.call(radios, target);
    const roleRadios = this.$el.querySelectorAll('[role=radio]');
    switch (e.keyCode) {
        case keyCode.LEFT:
        ...
        default:
        break;
    }
    }
},
```
这部分代码可以让你在radioGroup中使用键盘在各个radio中导航。
算是体验优化。

最后这一段代码有些不太明白，不知道哪些因素可以导致这个value变化(?todo)：
```js
watch: {
    value(value) {
    this.dispatch('ElFormItem', 'el.form.change', [this.value]);
    }
}
```
