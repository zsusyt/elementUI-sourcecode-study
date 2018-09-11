tabs和里面的nav,pane,bar稍微有点复杂，所以一些不太重要的点就先忽略，比如closable,editable等。  
重点就放在nav和pane如何组织，如果互相通信上。  

首先看下组件的组织结构：
```html
<Tab>
    <header>
        <Tab-Nav>
            <Tab-Bar></Tab-Bar>
        </Tab-Nav>
    </header>

    <panels></panels>
</Tab>
```
