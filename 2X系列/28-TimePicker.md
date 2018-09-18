TimePicker的内容可以结合DatePicker比较着来看，还是很类似的，因为先写的DatePicker，这里就不做详细讲解了。
time-select:
```js
import Picker from '../picker';
import Panel from '../panel/time-select';

export default {
  mixins: [Picker],

  name: 'ElTimeSelect',

  componentName: 'ElTimeSelect',

  props: {
    type: {
      type: String,
      default: 'time-select'
    }
  },

  beforeCreate() {
    this.panel = Panel;
  }
};
```

time-picker:
```js
import Picker from '../picker';
import TimePanel from '../panel/time';
import TimeRangePanel from '../panel/time-range';

export default {
  mixins: [Picker],

  name: 'ElTimePicker',

  props: {
    isRange: Boolean,
    arrowControl: Boolean
  },

  data() {
    return {
      type: ''
    };
  },

  watch: {
    isRange(isRange) {
      if (this.picker) {
        this.unmountPicker();
        this.type = isRange ? 'timerange' : 'time';
        this.panel = isRange ? TimeRangePanel : TimePanel;
        this.mountPicker();
      } else {
        this.type = isRange ? 'timerange' : 'time';
        this.panel = isRange ? TimeRangePanel : TimePanel;
      }
    }
  },

  created() {
    this.type = this.isRange ? 'timerange' : 'time';
    this.panel = this.isRange ? TimeRangePanel : TimePanel;
  }
};
```
