inputNumber跟普通input的区别就是对数字的处理，包含精度的处理。  
inputNumber就是一个div包装了两个控制增加、减少的控件和一个el-input输入框的组合控件。 

el-input为默认的text类型的input，value初始值为0。  
不管是在input中直接输入，还是点击increase、decrease按钮输入，最终都要靠以下逻辑处理：
```js
      setCurrentValue(newVal) {
        const oldVal = this.currentValue;
        if (typeof newVal === 'number' && this.precision !== undefined) {
          newVal = this.toPrecision(newVal, this.precision);
        }
        if (newVal >= this.max) newVal = this.max;
        if (newVal <= this.min) newVal = this.min;
        if (oldVal === newVal) {
          this.$refs.input.setCurrentValue(this.currentInputValue);
          return;
        }
        this.$emit('input', newVal);
        this.$emit('change', newVal, oldVal);
        this.currentValue = newVal;
      },
```
而watch中的逻辑应该是外部原因更改了value值才会走的逻辑，，可以看出跟setCurrentValue的逻辑很像： 
```js
    watch: {
      value: {
        immediate: true,
        handler(value) {
          let newVal = value === undefined ? value : Number(value);
          if (newVal !== undefined) {
            if (isNaN(newVal)) {
              return;
            }
            if (this.precision !== undefined) {
              newVal = this.toPrecision(newVal, this.precision);
            }
          }
          if (newVal >= this.max) newVal = this.max;
          if (newVal <= this.min) newVal = this.min;
          this.currentValue = newVal;
          this.$emit('input', newVal);
        }
      }
    },
```

这个组件中可以学到一个处理小数的方法，规避js处理小数的误差：
```js
const precisionFactor = Math.pow(10, this.numPrecision);
// Solve the accuracy problem of JS decimal calculation by converting the value to integer.
return this.toPrecision((precisionFactor * val + precisionFactor * step) / precisionFactor);

//辅助函数
      numPrecision() {
        const { value, step, getPrecision, precision } = this;
        const stepPrecision = getPrecision(step);
        if (precision !== undefined) {
          if (stepPrecision > precision) {
            console.warn('[Element Warn][InputNumber]precision should not be less than the decimal places of step');
          }
          return precision;
        } else {
          return Math.max(getPrecision(value), stepPrecision);
        }
      },

      toPrecision(num, precision) {
        if (precision === undefined) precision = this.numPrecision;
      return parseFloat(parseFloat(Number(num).toFixed(precision)));
      },

      getPrecision(value) {
        if (value === undefined) return 0;
        const valueString = value.toString();
        const dotPosition = valueString.indexOf('.');
        let precision = 0;
        if (dotPosition !== -1) {
          precision = valueString.length - dotPosition - 1;
        }
        return precision;
      },
```
