checkbox有个store的概念：
```js
      store() {
        return this._checkboxGroup ? this._checkboxGroup.value : this.value;
      },
```
这个_checkboxGroup又是通过isGroup确定的：
```js
      isGroup() {
        let parent = this.$parent;
        while (parent) {
          if (parent.$options.componentName !== 'ElCheckboxGroup') {
            parent = parent.$parent;
          } else {
            this._checkboxGroup = parent;
            return true;
          }
        }
        return false;
      },
```
如果checkbox处于一个checkboxGroup中的话，_checkboxGroup就是这个ElCheckGroup元素。
而与checkbox状态相关联的model定义如下：
```js
      model: {
        get() {
          return this.isGroup
            ? this.store : this.value !== undefined
              ? this.value : this.selfModel;
        },

        set(val) {
          if (this.isGroup) {
            this.isLimitExceeded = false;
            (this._checkboxGroup.min !== undefined &&
              val.length < this._checkboxGroup.min &&
              (this.isLimitExceeded = true));

            (this._checkboxGroup.max !== undefined &&
              val.length > this._checkboxGroup.max &&
              (this.isLimitExceeded = true));

            this.isLimitExceeded === false &&
            this.dispatch('ElCheckboxGroup', 'input', [val]);
          } else {
            this.$emit('input', val);
            this.selfModel = val;
          }
        }
      },
```
而checkbox最终显示的状态是由isChecked控制：
```js
      isChecked() {
        if ({}.toString.call(this.model) === '[object Boolean]') {
          return this.model;
        } else if (Array.isArray(this.model)) {
          return this.model.indexOf(this.label) > -1;
        } else if (this.model !== null && this.model !== undefined) {
          return this.model === this.trueLabel;
        }
      },
```
isChecked -> this.model -> this.store -> this._checkboxGroup.value
也就是最终只有改变了this._checkboxGroup.value的值，才能改变checkbox的状态
