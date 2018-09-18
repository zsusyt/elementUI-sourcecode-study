date-picker的文件夹内文件比较多，一开始会感觉有点没有头绪。
我们从index.js开始看，就可以摸索出一点线索来：
可以看到从src/picker/data-picker.js引入的DatePicker这个组件。
这个文件的内容是非常简单的：
```js
import Picker from '../picker';
import DatePanel from '../panel/date';
import DateRangePanel from '../panel/date-range';

const getPanel = function(type) {
  if (type === 'daterange' || type === 'datetimerange') {
    return DateRangePanel;
  }
  return DatePanel;
};

export default {
  mixins: [Picker],

  name: 'ElDatePicker',

  props: {
    type: {
      type: String,
      default: 'date'
    },
    timeArrowControl: Boolean
  },

  watch: {
    type(type) {
      if (this.picker) {
        this.unmountPicker();
        this.panel = getPanel(type);
        this.mountPicker();
      } else {
        this.panel = getPanel(type);
      }
    }
  },

  created() {
    this.panel = getPanel(this.type);
  }
};
```
可以看到这只是一个壳，主要靠mixin的Picker中封装的逻辑来实现组件。
this.panel也是一个主要属性，通过type属性来确定。

下面看看封装的Picker(在picker.vue这个文件中)：
观察他的DOM结构，只有一个input输入框(或者两个，如果是range类型的话)。那么问题来了，日历面板是如何生成的？

根据input框被点击之后会弹出日历面板的行为，加上input框没有响应点击事件，我们估计是focus事件的行为，观察handleFocus函数，发现他只是把this.pickerVisible设为true了，那么一般就是computed或者watch中继续响应了。
watch中如果发现如果pickerVisble为true的话，有个showPicker的函数，继续追查，
```js
if (!this.picker) {
    this.mountPicker();
}
```
继续观察this.mountPicker函数，第一句就是：
this.picker = new Vue(this.panel).$mount();
回忆this.panel就是日历面板的组件，其他属性可能是跟这个面板与外部交互行为的逻辑，这个面板自身的逻辑应该是封装在this.panel的代码中的。

接下来就是对各种"panel"的分析了(todo)