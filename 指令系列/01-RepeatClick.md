指令的代码不多：
```js
import { once, on } from 'element-ui/src/utils/dom';

export default {
  bind(el, binding, vnode) {
    let interval = null;
    let startTime;
    const handler = () => vnode.context[binding.expression].apply();
    const clear = () => {
      if (new Date() - startTime < 100) {
        handler();
      }
      clearInterval(interval);
      interval = null;
    };

    on(el, 'mousedown', (e) => {
      if (e.button !== 0) return;
      startTime = new Date();
      once(document, 'mouseup', clear);
      clearInterval(interval);
      interval = setInterval(handler, 100);
    });
  }
};
```
里面有个知识点：如何获取到指令所在组件上的method： 
<code>vnode.context[binding.expression].apply()</code>
(todo: 这个要深入了解vnode才行？)

接下来的逻辑不复杂： 
在bind钩子函数里为指令所在的元素绑定mousedown事件，  
如果不是左键，就不做任何动作，  
然后记录一下startTime，  
再为document绑定mouseup事件，一次性事件。  
然后利用setInterval执行handler，延时100ms，  
如果用户一直按着鼠标左键，每100ms就会把handler放到event loop中一次，  
如果用户松开鼠标，就睡触发clear事件，在clear事件中先判断一下是否到达100ms，  
这个地方是为了针对用户点击后极短事件内松开，那么至少会执行一次，因为这个时候   
未满100ms，setInterval还未把handler放到event loop中，如果不这么做一次也不会  
执行。  
