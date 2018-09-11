dropdown的render函数返回值是这样的:
```jsx
<div class="el-dropdown" v-clickoutside={hide}>
    {triggerElm}
    {this.$slots.dropdown}
</div>
```

整个DOM结构分成了两个部分：
triggerElm和this.$slot.dropdown。  

### 1、triggerElm
```js
let triggerElm = !splitButton
    ? this.$slots.default
    : (<el-button-group>
        <el-button type={type} size={dropdownSize} nativeOn-click={handleMainButtonClick}>
            {this.$slots.default}
        </el-button>
        <el-button ref="trigger" type={type} size={dropdownSize} class="el-dropdown__caret-button">
            <i class="el-dropdown__icon el-icon-arrow-down"></i>
        </el-button>
    </el-button-group>);
```
dropdown有个prop是splitButton，这个prop可以决定外观是一个单独的按钮，还是有个单独的触发按钮。  
里面nativeOn-click是使用JSX写render函数知识，如果深入学习可以看[这个](https://github.com/vuejs/babel-plugin-transform-vue-jsx#usage)和[这个](https://egoist.moe/2017/09/21/vue-jsx-full-guide/)

外部triggerElm通过this.visible这个data的属性与内部dropdownMenu通信：
```js
watch: {
    visible(val) {
        this.broadcast('ElDropdownMenu', 'visible', val);
        this.$emit('visible-change', val);
    },
}
```
```js
created() {
    this.$on('visible', val => {
    this.showPopper = val;
    });
},
```
```html
<ul class="el-dropdown-menu el-popper" :class="[size && `el-dropdown-menu--${size}`]" v-show="showPopper">
    <slot></slot>
</ul>
```

接下来最重要的就是以下函数：
```js
      initEvent() {
        let { trigger, show, hide, handleClick, splitButton, handleTriggerKeyDown, handleItemKeyDown } = this;
        this.triggerElm = splitButton
          ? this.$refs.trigger.$el
          : this.$slots.default[0].elm;

        let dropdownElm = this.dropdownElm = this.$slots.dropdown[0].elm;

        this.triggerElm.addEventListener('keydown', handleTriggerKeyDown); // triggerElm keydown
        dropdownElm.addEventListener('keydown', handleItemKeyDown, true); // item keydown
        // 控制自定义元素的样式
        if (!splitButton) {
          this.triggerElm.addEventListener('focus', () => {
            this.focusing = true;
          });
          this.triggerElm.addEventListener('blur', () => {
            this.focusing = false;
          });
          this.triggerElm.addEventListener('click', () => {
            this.focusing = false;
          });
        }
        // 所以这个部分，如果把鼠标放到triggerElm和dropdownElm中间间隙上，dropdownElm是会消失的。
        if (trigger === 'hover') {
          this.triggerElm.addEventListener('mouseenter', show);
          this.triggerElm.addEventListener('mouseleave', hide);
          dropdownElm.addEventListener('mouseenter', show);
          dropdownElm.addEventListener('mouseleave', hide);
        } else if (trigger === 'click') {
          this.triggerElm.addEventListener('click', handleClick);
        }
      },
```

还可以学习下对tabindex的处理：
```js
      resetTabindex(ele) { // 下次tab时组件聚焦元素
        this.removeTabindex();
        ele.setAttribute('tabindex', '0'); // 下次期望的聚焦元素
      },
      removeTabindex() {
        this.triggerElm.setAttribute('tabindex', '-1');
        this.menuItemsArray.forEach((item) => {
          item.setAttribute('tabindex', '-1');
        });
      },
```
分别在三个地方有调用：
```js
      hide() {
        if (this.triggerElm.disabled) return;
        this.removeTabindex();
        this.resetTabindex(this.triggerElm);
        clearTimeout(this.timeout);
        this.timeout = setTimeout(() => {
          this.visible = false;
        }, this.trigger === 'click' ? 0 : this.hideTimeout);
      },

      handleTriggerKeyDown(ev) {
        const keyCode = ev.keyCode;
        if ([38, 40].indexOf(keyCode) > -1) { // up/down
          this.removeTabindex();
          this.resetTabindex(this.menuItems[0]);
          this.menuItems[0].focus();
          ev.preventDefault();
          ev.stopPropagation();
        } else if (keyCode === 13) { // space enter选中
          this.handleClick();
        } else if ([9, 27].indexOf(keyCode) > -1) { // tab || esc
          this.hide();
        }
        return;
      },

      handleItemKeyDown(ev) {
        const keyCode = ev.keyCode;
        const target = ev.target;
        const currentIndex = this.menuItemsArray.indexOf(target);
        const max = this.menuItemsArray.length - 1;
        let nextIndex;
        if ([38, 40].indexOf(keyCode) > -1) { // up/down
          if (keyCode === 38) { // up
            nextIndex = currentIndex !== 0 ? currentIndex - 1 : 0;
          } else { // down
            nextIndex = currentIndex < max ? currentIndex + 1 : max;
          }
          this.removeTabindex();
          this.resetTabindex(this.menuItems[nextIndex]);
          this.menuItems[nextIndex].focus();
          ev.preventDefault();
          ev.stopPropagation();
        } else if (keyCode === 13) { // enter选中
          this.triggerElm.focus();
          target.click();
          if (this.hideOnClick) { // click关闭
            this.visible = false;
          }
        } else if ([9, 27].indexOf(keyCode) > -1) { // tab // esc
          this.hide();
          this.triggerElm.focus();
        }
        return;
      },
```

### 2、this.$slot.dropdown
由于是使用this.$slot.dropdown来定义这部分结构的，那么理论上来说，  
只要把这部分内容写成slot="dropdown"，并且实现类似dropdown-menu内部的功能， 
就可以作为dropdown的内容。 
前面讲过通过visible消息，实现了内外部通信，另外
```html
<slot></slot>
```
可以继续填入dropdown-item。
在dropdown-item中实现了command消息，可以从国这个消息，执行业务逻辑。
```js
    methods: {
      handleClick(e) {
        this.dispatch('ElDropdown', 'menu-item-click', [this.command, this]);
      }
    }
```
```js
this.$on('menu-item-click', this.handleMenuItemClick);

handleMenuItemClick(command, instance) {
    if (this.hideOnClick) {
        this.visible = false;
    }
    this.$emit('command', command, instance);
},
```
开发者可以响应command事件，从而自定义菜单项被点击时的业务逻辑。
