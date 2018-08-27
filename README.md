开个新坑，尽量不要烂尾。
最近两周开始用elementUI，对他的源码也挺感兴趣，应该也是一个学习vuejs高级技巧的途径。
希望能达到可以帮助官方修复bug的水平。
补充：这个系列并不牵涉css相关的代码，只分析vuejs相关的代码。
version: 2.4.6

一些约定：
0X系列：一些公共的东西，比如utils或者混入的代码，不跟具体哪个组件强绑定
1X系类：Basic
2X系列：Form
3X系列：Data
4X系列：Notice
5X系列：Navigation
6X系列：Others

补充：
[掘金上一个网易的妹子写的分析更详细一点，并且也分析了相关的sass代码，可以参考。](https://juejin.im/post/5b741fad6fb9a0098474bbb0)


---目录的分割线

0X系列
* [00-简单开始]()
* [01-provide&inject]()

1X系类
* [10-Layout]()
* [11-Container&Icon]()
* [12-Button]()
