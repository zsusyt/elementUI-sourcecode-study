# ELForm & ElFormItem

## ELForm
ELForm是个比较简单的组件(以下简称form)，他的主要功能是靠内部的ELFormItem(以下简称item)实现的。而form自身只是起到一个传递props和容器的作用。 form的验证功能是其逻辑的主要部分。

以下是form收集item的主要代码，注意到item上只有写了prop的才需要被form收集(以备后面验证之用)
```js
// form上在create的时候对两个事件建立起响应。
// 为什么要在create阶段建立监听，这个要温习下vue中父子组件事件周期执行次序的问题了。
data() {
  return {
    // 收集到的item都放在这个数组中
    fields: []
  };
},
created() {
  // 收集
  this.$on('el.form.addField', (field) => {
    if (field) {
      this.fields.push(field);
    }
  });
  // 销毁
  this.$on('el.form.removeField', (field) => {
    if (field.prop) {
      this.fields.splice(this.fields.indexOf(field), 1);
    }
  });
},
```
以下是item中发送消息的代码：
```js
mounted() {
  // this.prop就是需要验证的时候在item上加的那个属性，也就是需要验证的item才会被form收集
  if (this.prop) {
    this.dispatch('ElForm', 'el.form.addField', [this]);
  。。。
  }
},
beforeDestroy() {
  this.dispatch('ElForm', 'el.form.removeField', [this]);
}

```

下面看下form的props：
```js
props: {
  model: Object,
  rules: Object,
  labelPosition: String,
  labelWidth: String,
  labelSuffix: {
    type: String,
    default: ''
  },
  inline: Boolean,
  inlineMessage: Boolean,
  statusIcon: Boolean,
  showMessage: {
    type: Boolean,
    default: true
  },
  size: String,
  disabled: Boolean,
  validateOnRuleChange: {
    type: Boolean,
    default: true
  }
},
```
基本都是文档里面有些的东西，功能直接看文档就好。这里提一下validateOnRuleChange这个属性：实际开发中有这种需求，某个字段的验证条件会随着情况变化。
  这样可以有两种做法：
  - 1、在验证整个表单之前再去改变验证条件：
    - 好处是可以集中管理；
    - 缺点是变化的逻辑跟验证规则离的远了，不利阅读者理解；
  - 2、直接把规则写在computed中，因为computed中可以写逻辑，
  所以可以直接把变化的逻辑放在规则中：
    - 好处是变化的逻辑离规则很近，利于阅读者理解；
    - 坏处下面细说。

接下来说说刚才提到的坏处：form默认规则变化会把自动把所有规则重跑一遍，也就是一个规则变了，所有需要验证的item都会再验证一遍。这样粗听起来也没什么，反正都要验证，主要是外观上看起来会有一点点怪：一个item的规则变了，其他item如果暂时还不符合规则(比如必填item，暂时还没填到)就会都显示出红色提示(好在之前做的是内部系统，这个问题不大), 但是validateOnRuleChange这个属性就可以解决这个问题, 还是保持到用户点击提交再统一验证，或者其他item等他填写到的时候再各自验证。

如下为刚才所说form默认规则变化会把规则重跑一遍:
```js
watch: {
  rules() {
    if (this.validateOnRuleChange) {
      this.validate(() => {});
    }
  }
},
```
这里不需要deep：true，因为computed会返回一个新的Object，而不是只是修改原Object的一部分。

关于form最后再说一点:
```js
provide() {
  return {
    elForm: this
  };
},
```

provide这个api相当好用，虽然官方说不要把它用在业务逻辑中，但是研究过vue源码就会发现它并没有什么特殊之处，用在业务逻辑中完全没有问题。
至于它没有响应性的问题，也是可以解决的：传入一个引用类型的变量即可，或者把一个非引用类型的封装成引用类型的。
这个api也很类似react中的context，但是使用起来简洁很多，不用套那么多高阶组件，只需要在子组件中inject即可，这里要为vue点赞。
自此，所有内部组件都可以拿到最近的form父组件了。

## ElFormItem

item中有两个东西感觉是一样的, 并且在取form属性的时候也有混用的情况。
```js
inject: ['elForm'],

computed: {    
  form() {
    let parent = this.$parent;
    let parentName = parent.$options.componentName;
    while (parentName !== 'ElForm') {
      if (parentName === 'ElFormItem') {
        this.isNested = true;
      }
      parent = parent.$parent;
      parentName = parent.$options.componentName;
    }
    return parent;
  },
```

接下来我们看看mounted的内容：
```js
mounted() {
  if (this.prop) {
    // 前面已讲过
    this.dispatch('ElForm', 'el.form.addField', [this]);

    let initialValue = this.fieldValue;
    if (Array.isArray(initialValue)) {
      initialValue = [].concat(initialValue);
    }
    Object.defineProperty(this, 'initialValue', {
      value: initialValue
    });

    let rules = this.getRules();

    if (rules.length || this.required !== undefined) {
      this.$on('el.form.blur', this.onFieldBlur);
      this.$on('el.form.change', this.onFieldChange);
    }
  }
},
```
其中this.fieldValue比较有意思：
```js
fieldValue() {
  const model = this.form.model;
  if (!model || !this.prop) { return; }

  let path = this.prop;
  if (path.indexOf(':') !== -1) {
    path = path.replace(/:/, '.');
  }

  return getPropByPath(model, path, true).v;
},
```
原来prop属性也可以写' : '这个分隔符号的，反正都会转化为' . ', 最后却不是直接从model上取值，而是调用了getPropByPath函数：
```js
export function getPropByPath(obj, path, strict) {
  let tempObj = obj;
  path = path.replace(/\[(\w+)\]/g, '.$1');
  path = path.replace(/^\./, '');

  let keyArr = path.split('.');
  let i = 0;
  for (let len = keyArr.length; i < len - 1; ++i) {
    if (!tempObj && !strict) break;
    let key = keyArr[i];
    if (key in tempObj) {
      tempObj = tempObj[key];
    } else {
      if (strict) {
        throw new Error('please transfer a valid prop path to form item!');
      }
      break;
    }
  }
  return {
    o: tempObj,
    k: keyArr[i],
    v: tempObj ? tempObj[keyArr[i]] : null
  };
};
```
obj就是model, path就是转化后的prop, 这里我们发现原来prop中'[ ]'这种写法也是可以的, 下面就是常规操作了，最后返回了返回对象的v这个key的value。
再看如何获取规则rules：
```js
getRules() {
  let formRules = this.form.rules;
  const selfRules = this.rules;
  const requiredRule = this.required !== undefined ? { required: !!this.required } : [];

  const prop = getPropByPath(formRules, this.prop || '');
  formRules = formRules ? (prop.o[this.prop || ''] || prop.v) : [];

  return [].concat(selfRules || formRules || []).concat(requiredRule);
},
```
从这里可以看出item自身也是可以提供rule的，并且会覆盖form提供的rule.

### 这里有个技巧：不论用何种方式提供的rules，prop属性都是需要的。如果只是在元素上加了":required='true'"那么只是多了个标志必填的*号，并不会让规则起作用。只有加上了prop属性才行。

好了，到这里，基本表单相关的东西都分析的7788了，就是还有一点点小东西可以再谈下，mounted中有这么一段：
```js
if (rules.length || this.required !== undefined) {
  this.$on('el.form.blur', this.onFieldBlur);
  this.$on('el.form.change', this.onFieldChange);
}
```
item为什么还要响应消息？难道item也是容器？
是的，item本身也是容器，真正的控件比如button、input、select、checkbox都是放在item里面的。而这些控件如果再要与上层交互的话，就要借助item了，所以item还要响应消息。说到这里让我想起在项目中封装了一个基于el-select的组件，但是验证的时候会有问题，现在想想应该是中间消息通讯断了，里面包装的原生elementUI组件发的el.form.blur和el.form.change事件没有继续往上传导致的，所以以后要封装基于elementUI的组件需要考虑这些事情。

我们看看这些事件的响应函数：
```js
onFieldBlur() {
  this.validate('blur');
},
onFieldChange() {
  if (this.validateDisabled) {
    this.validateDisabled = false;
    return;
  }

  this.validate('change');
},
```
是的，都是跟验证有关的逻辑。

看了这么多，你发现一个form组件本身是很简单的，但是它的验证逻辑占了80%的代码量。


