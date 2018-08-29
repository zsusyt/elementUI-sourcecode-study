debounce和throttle一般放在一起说，不过他们的区别还是有的。  
简单来说，debounce就是连续触发只想取一次有效，哪怕是连续100次；  
但是如果是throttle，如果限速每10次触发一次的话，  
100次就会触发10次。所以有的场合还是要区分debounce和throttle的。  
这里有两个链接，讲的都非常好，尤其图形非常直观：  
[一个是文字](https://github.com/lishengzxc/bblog/issues/7)  
[一个是图形](http://demo.nimius.net/debounce_throttle/)

在这个项目中，debounce的主要用法就是debounce(timeRange, callback)，  
在timeRange的时间内，只触发一次callback的调用。  
后面有机会再补上整个的实现(todo)