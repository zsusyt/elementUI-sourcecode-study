tabs和里面的nav,pane,bar稍微有点复杂，所以一些不太重要的点就先忽略，比如closable,editable等。  
重点就放在nav和pane如何组织，如果互相通信上。  

首先看下组件的组织结构：
```jsx
<Tab>
    <header>
        <newButton>
        <Tab-Nav>
            <Tab-Bar></Tab-Bar>
            <tabs>
        </Tab-Nav>
    </header>

    <panels></panels>
</Tab>
```
newButton由editable和addable属性控制是否显示;  
tab-bar只是在type没有设置的时候，用来标志当前active表项的一条带颜色的线条;  
表头项主要在tab-nav内生成：
```js
      const tabs = this._l(panes, (pane, index) => {
        let tabName = pane.name || pane.index || index;
        const closable = pane.isClosable || editable;

        pane.index = `${index}`;

        const btnClose = closable
          ? <span class="el-icon-close" on-click={(ev) => { onTabRemove(pane, ev); }}></span>
          : null;

        const tabLabelContent = pane.$slots.label || pane.label;
        const tabindex = pane.active ? 0 : -1;
        return (
          <div
            class={{
              'el-tabs__item': true,
              [`is-${ this.rootTabs.tabPosition }`]: true,
              'is-active': pane.active,
              'is-disabled': pane.disabled,
              'is-closable': closable,
              'is-focus': this.isFocus
            }}
            id={`tab-${tabName}`}
            aria-controls={`pane-${tabName}`}
            role="tab"
            aria-selected={ pane.active }
            ref="tabs"
            tabindex={tabindex}
            refInFor
            on-focus={ ()=> { setFocus(); }}
            on-blur ={ ()=> { removeFocus(); }}
            on-click={(ev) => { removeFocus(); onTabClick(pane, tabName, ev); }}
            on-keydown={(ev) => { if (closable && (ev.keyCode === 46 || ev.keyCode === 8)) { onTabRemove(pane, ev);} }}
          >
            {tabLabelContent}
            {btnClose}
          </div>
        );
      });
```
你一定很好奇，这里的panes从哪里来？
因为一开始panes为一个空数组。 
接下来我们看看tab-pane这个组件，就可以回答这个问题了。 
```js
    mounted() {
      this.$parent.addPanes(this);
    },
```
我们看到原来主要是依赖父组件的这个函数实现： 
```js
      addPanes(item) {
        const index = this.$slots.default.indexOf(item.$vnode);
        this.panes.splice(index, 0, item);
      },
```

tabs组件做了很多细节的东西，比如表项过多的时候，提供了scroll的功能。 
这个功能主要是在tab-nav组件内实现的：
```js
      const scrollBtn = scrollable
        ? [
          <span class={['el-tabs__nav-prev', scrollable.prev ? '' : 'is-disabled']} on-click={scrollPrev}><i class="el-icon-arrow-left"></i></span>,
          <span class={['el-tabs__nav-next', scrollable.next ? '' : 'is-disabled']} on-click={scrollNext}><i class="el-icon-arrow-right"></i></span>
        ] : null;
```
其中scrollable初始为false,后续在update中是这样定义的：
```js
          this.scrollable = this.scrollable || {};
          this.scrollable.prev = currentOffset;
          this.scrollable.next = currentOffset + containerSize < navSize;
```
