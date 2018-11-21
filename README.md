开个新坑，尽量不要烂尾。
最近两周开始用elementUI，对他的源码也挺感兴趣，应该也是一个学习vuejs高级技巧的途径。
希望能达到可以帮助官方修复bug的水平。
补充：这个系列并不牵涉css相关的代码，只分析vuejs相关的代码。
version: 2.4.6

补充：
[掘金上一个网易的妹子写的分析更详细一点，并且也分析了相关的sass代码，可以参考。](https://juejin.im/post/5b741fad6fb9a0098474bbb0)


---目录的分割线
指令系列
* [01-RepeatClick](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/%E6%8C%87%E4%BB%A4%E7%B3%BB%E5%88%97/01-RepeatClick.md)

0X系列-一些公共的东西，比如utils或者混入的代码，不跟具体哪个组件强绑定
* [00-简单开始](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/0X%E7%B3%BB%E5%88%97/00-%E7%AE%80%E5%8D%95%E5%BC%80%E5%A7%8B.md)
* [01-provide&inject](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/0X%E7%B3%BB%E5%88%97/01-provide%26inject.md)
* [02-Emitter](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/0X%E7%B3%BB%E5%88%97/02-Emitter.md)
* [03-calcTextareaHeight](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/0X%E7%B3%BB%E5%88%97/03-calcTextareaHeight.md)
* [04-debounce](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/0X%E7%B3%BB%E5%88%97/04-debounce.md)
* 05-Popper-todo
* 06-Scrollbar-todo

1X系列-Basic
* [10-Layout](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/1X%E7%B3%BB%E5%88%97/10-Layout.md)
* [11-Container&Icon](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/1X%E7%B3%BB%E5%88%97/11-Container%26Icon.md)
* [12-Button](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/1X%E7%B3%BB%E5%88%97/12-Button.md)

2X系列-Form
* [20-Radio](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/2X%E7%B3%BB%E5%88%97/20-Radio.md)
* [21-Checkbox](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/2X%E7%B3%BB%E5%88%97/21-Checkbox.md)
* [22-Input](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/2X%E7%B3%BB%E5%88%97/22-Input.md)
* [23-InputNumber](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/2X%E7%B3%BB%E5%88%97/23-InputNumber.md)
<!-- * [24-Select](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/2X%E7%B3%BB%E5%88%97/24-Select.md) -->
* 24-Select-todo
<!-- * [25-Cascader](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/2X%E7%B3%BB%E5%88%97/25-Cascader.md) -->
* 25-Cascader-todo
* [26-Switch](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/2X%E7%B3%BB%E5%88%97/26-Switch.md)
* 27-Slider-todo
* [28-TimePicker](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/2X%E7%B3%BB%E5%88%97/28-TimePicker.md)
* [29-DatePicker](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/2X%E7%B3%BB%E5%88%97/29-DatePicker.md)
* 210-Upload-todo
* 211-Rate-todo
* 212-ColorPicker-todo
* 213-Transfer-todo
* 214-Form-todo

3X系列-Data
* [31-Table-ing](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/3X%E7%B3%BB%E5%88%97/31-Table.md)
* [311-TableStore](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/3X%E7%B3%BB%E5%88%97/311-TableStore.md)
* [312-TableLayer](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/3X%E7%B3%BB%E5%88%97/312-TableLayer.md)
* [32-Tag](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/3X%E7%B3%BB%E5%88%97/32-Tag.md)
* 33-Progress-todo
* 34-Tree-todo
* 35-Pagination-todo
* 36-Badge-todo

4X系列-Notice
* 41-Alert-todo
* 42-Loading-todo
* 43-Message-todo
* 44-MessageBox-todo
* 45-Notification-todo

5X系列-Navigation
* 51-NavMenu-todo
* [52-Tabs](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/5X%E7%B3%BB%E5%88%97/52-Tabs.md)
* [53-Breadcrumb](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/5X%E7%B3%BB%E5%88%97/53-Breadcrumb.md)
* [54-Dropdown](https://github.com/zsusyt/elementUI-sourcecode-study/blob/master/5X%E7%B3%BB%E5%88%97/54-Dropdown.md)
* 55-Steps-todo

6X系列-Others
* 61-Dialog-todo
* [62-Tooltip]()
* 63-Popover-todo
* 64-Card-todo
* 65-Carousel-todo
* 66-Collapse-todo

7X系列-周边
* 71-async-validator