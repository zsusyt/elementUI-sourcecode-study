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
如果checkbox处于一个checkboxGroup中的话，_checkboxGroup就是这个checkboxGroup元素。
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
isChecked -> this.model -> this.store 
如果是checkboxGroup内部的元素的话，最终是由this._checkboxGroup.value决定的。 
也就是最终只有改变了this._checkboxGroup.value的值，才能改变checkbox的状态。

整个点击过程如下： 
先触发input上的原生change事件： 
```js
      handleChange(ev) {
        if (this.isLimitExceeded) return;
        let value;
        if (ev.target.checked) {
          value = this.trueLabel === undefined ? true : this.trueLabel;
        } else {
          value = this.falseLabel === undefined ? false : this.falseLabel;
        }
        this.$emit('change', value, ev);
        this.$nextTick(() => {
          if (this.isGroup) {
            this.dispatch('ElCheckboxGroup', 'change', [this._checkboxGroup.value]);
          }
        });
      }
```
检查this.isLimitExceeded的值，而他的值则在每次set model的时候都会维护。 
然后触发一个组件的change事件，这样外面包装的组件就可以响应这个事件，实现了行为和业务逻辑分离。 
其实这个套路是做UI框架常用的方式，因为框架开发者没办法知道业务逻辑是怎么样的。 

触发原生change事件的同时，也在对model进行set操作：
```js
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
```
