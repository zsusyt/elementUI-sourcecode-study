在很多组件内，你都会看到这个东西：
```js
inject: {
      elForm: {
        default: ''
      },
      elFormItem: {
        default: ''
      }
 },
```
其实对vuejs的api'比较熟悉的话，就知道这个是类似react里面context的东西，[provide$inject](https://cn.vuejs.org/v2/api/#provide-inject)
其中elForm是el-form这个组件提供的：
```js
provide() {
      return {
        elForm: this
      };
},
```
elFormItem是el-form-item这个组件提供的：
```js
provide() {
      return {
        elFormItem: this
      };
},
```
通过这两个东西，就可以从内部的任意组件上，取到form或者form-item组件本身的引用。
因为组件间可能是有联系的，组件间的联系其中机制之一就是这里实现的 ，还有其他机制另外文章会说。
