calculateNodeStyling是window.getComputedStyle这个api的封装，可以得出传入元素的css信息。  
因为是计算height相关的信息，所以这个函数只考虑了垂直方向的数值，比如padding计算是bottom和top的和，  
border计算的也是bottom和top的和。  
注意contextStyle是个字符串。
```js
function calculateNodeStyling(targetElement) {
  const style = window.getComputedStyle(targetElement);

  const boxSizing = style.getPropertyValue('box-sizing');

  const paddingSize = (
    parseFloat(style.getPropertyValue('padding-bottom')) +
    parseFloat(style.getPropertyValue('padding-top'))
  );

  const borderSize = (
    parseFloat(style.getPropertyValue('border-bottom-width')) +
    parseFloat(style.getPropertyValue('border-top-width'))
  );

  const contextStyle = CONTEXT_STYLE
    .map(name => `${name}:${style.getPropertyValue(name)}`)
    .join(';');

  return { contextStyle, paddingSize, borderSize, boxSizing };
}
```

后面calcTextareaHeight的思路是这样的：  
建一个空的textarea对象，然后把目标对象的相关的style(通过上面calculateNodeStyling函数取得)，
赋给这个textarea，当然还有一些附加style，都放在HIDDEN_STYLE字符串中。
然后取得目标对象的文字内容，也赋给空的textarea，通过scrollHeight这个webapi取得该元素在不  
使用滚动条的情况下为了适应视口中所用内容所需的最小高度。 具体可以看[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollHeight)。  
然后根据boxSizing是border-box还是content-box分别算出真实高度。这个height是后面所有计算的基础。
```js
  if (boxSizing === 'border-box') {
    height = height + borderSize;
  } else if (boxSizing === 'content-box') {
    height = height - paddingSize;
  }
```

接着把hiddenTextarea.value = ''是为了计算单行高度。
接着讨论了是否传入minRows和maxRows的情况，根据不同情况分别分别算出minHeight和height，
最终返回的结果result = {
    minHeight: XXX,
    height: XXX
}
