TableStore给人感觉有点Vuex的味道(?)  
本质就是一个构造函数，在table组件的数据中会实例化一个对象。 

为什么说有vuex的味道，因为有一个实例方法：commit  
和一个实例对象：mutations  
mutations列出了支持的操作，commit触发这些操作。  
