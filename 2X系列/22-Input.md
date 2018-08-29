input有很多feature，比如有前置、后置元素，前置、后置内容可以很自由的定义外观；  
可以区分一般input还是textarea，以及textarea外观的控制(主要通过03文章所说的calcTextareaHeight相关代码实现)；
还可以由validateState:
```js
validateState() {
    return this.elFormItem ? this.elFormItem.validateState : '';
},
```
这个功能应该是跟父元素elFormItem相关的，这里也暂时不涉及；
比较有特色的是对输入的处理，不是简单实用input事件，因为中文输入法可能会有一些预输入字符，  
但这不是我们想要的，所以要使用composition系列事件，具体可以看这部分代码:
```js
handleComposition(event) {
    if (event.type === 'compositionend') {
        this.isOnComposition = false;
        this.currentValue = this.valueBeforeComposition;
        this.valueBeforeComposition = null;
        this.handleInput(event);
    } else {
        const text = event.target.value;
        const lastCharacter = text[text.length - 1] || '';
        this.isOnComposition = !isKorean(lastCharacter);
        if (this.isOnComposition && event.type === 'compositionstart') {
        this.valueBeforeComposition = text;
        }
    }
},
```
其中借助isOnComposition状态标识变量和其他一些中间变量，很好兼容了中文输入。
其他功能都属于正常input输入框相关的功能，看看代码就可以了。

---
autocomplete组件是个很有意思的组件，他基本上跟一个普通input差不多，但是点击后会有一个提示菜单，
这一切都从这个地方看起：
autocomplete.vue:
```js
@focus="handleFocus"

handleFocus(event) {
    this.activated = true;
    this.$emit('focus', event);
    if (this.triggerOnFocus) {
        this.debouncedGetData(this.value);
    }
},

close(e) {
    this.activated = false;
},

suggestionVisible() {
    const suggestions = this.suggestions;
    let isValidData = Array.isArray(suggestions) && suggestions.length > 0;
    return (isValidData || this.loading) && this.activated;
},

watch: {
    suggestionVisible(val) {
    this.broadcast('ElAutocompleteSuggestions', 'visible', [val, this.$refs.input.$refs.input.offsetWidth]);
    }
},

```
autocomplete-suggestions.vue:
```js
created() {
    this.$on('visible', (val, inputWidth) => {
        this.dropdownWidth = inputWidth + 'px';
        this.showPopper = val;
    });
}

v-show="showPopper"
```
以上就是整个控制提示菜单显隐相关的代码。


