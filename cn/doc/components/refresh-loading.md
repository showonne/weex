# &lt;refresh-loading&gt;

### 概述

 refresh/loading 不是一个独立的控件，是scroller和list的附属控件。必须写在scroller和list的标签中才会生效。
### 用法

refresh/loading 可以像其他标签一样直接使用。
标签内部可以包含任何其他空间，比如text、img等。
特别: weex 实现了一个默认的加载动画的indicator。用法可以参考如下：

```
<loading  display="{{loading_display}}" onloading="onloading">
      <loading-indicator style="height:60;width:60;color:#3192e1"></loading-indicator>
</loading>
```
#### 属性

具有通用控件的一般属性
#### 事件
-  onrefresh scroller/list 下拉时触发。
- onloading scroller/list 上拉到底部时触发。
- onpullingdown scoller/list 下拉时触发，会把下拉的距离和能够下拉的最大距离透传给前端。前端可以根据这个事件做不同的动画。  iOS目前不支持，预计0.9版本会支持。
  参数描述如下：  
  dy:refresh垂直方向滑动距离  
  headerHeight：refresh控件下滑距离  
  maxHeight：refresh控件能够下滑的最大距离  
### 当前存在的问题
- refresh/loading 不支持remove. 0.9 版本会修复。
- refresh/loading 不再使用时设置display=‘hide' 滑动时任然会出现，并有onrefresh/loaing 事件。解决方法：1. 修改提示语句，在onfresh/onloaing 事件中添加判断语句。可以参考下面的例子。2. 0.9版本后可以remove refresh/loading 控件解决。
- refresh/loading 只能通过属性 display进行展示和隐藏，且必须成对出现，及设置display='show',必须有对应的display='hide'。否则，程序会出现假死的问题。
### loading标签 和 onloadmore事件的区别。

loading是一个标签,这个标签可以响应onloading事件，只有在scroller/list 滑动到底部时才会触发。
onloadmore 是 scroller/list 标签的事件，必须设置loadmoreoffset才会生效。当scroller/list 剩余没有显示的高度小于loadmoreoffset时会触发onloadmore 事件。
例如：scroller 总高度1000，loadmoreoffset=600 当scroller已经显示400时再滑动就会触发onloadmore事件。list同理。
### 使用范例
#### scroller

```
<template>
    <scroller onloadmore="onloadmore" loadmoreoffset="1000">
        <refresh onrefresh="onrefresh" display="{{refreshDisplay}}">
            <text id="refreshText">{{refreshText}}</text>
        </refresh>
        <div repeat="{{v in items}}">
            <text style="font-size: 40px; color: black">{{v.item}}</text>
        </div>
        <loading onloading="onloading" display="{{loadingDisplay}}">
            <text id="loadingText">{{loadingText}}</text>
        </loading>
    </scroller>
</template>
<script>
    module.exports = {
        data: {
            refreshDisplay: 'show',
            loadingDisplay: 'show',
            loadingText: '上拉加载更多',
            refreshText: '下拉刷新',
            items: []
        },
        created: function () {
            for (var i = 0; i < 30; i++) {
                this.items.push({'item': '测试数据' + i});
            }
        },
        methods: {
            onrefresh: function () {
                var vm = this;
                vm.refreshDisplay = 'show'
                if (vm.items.length > 50) {
                    vm.refreshText = "没有更新了"
                    vm.refreshDisplay = 'hide'
                    return;
                }
                var len = vm.items.length;
                for (var i = len; i < (len + 20); i++) {
                    vm.items.unshift({'item': '测试数据' + i});
                }
                vm.refreshDisplay = 'hide'
            },
            onloading: function () {
                var vm = this;
                vm.loadingDisplay = 'show'
                if (vm.items.length > 30) {
                    vm.loadingText = "没有更多了!"
                    vm.loadingDisplay = 'hide'
                    return;
                }

                var len = vm.items.length;
                for (var i = len; i < (len + 20); i++) {
                    vm.items.push({'item': '测试数据' + i});
                }
                vm.loadingDisplay = 'hide'
            },
            onloadmore:function(){
                console.log("into--[onloadmore]")
            }
        }
    }
</script>
```
#### list

```
<template>
    <list>
        <refresh onrefresh="onrefresh" display="{{refreshDisplay}}">
            <text id="refreshText">{{refreshText}}</text>
        </refresh>
        <cell repeat="{{v in items}}">
            <text style="font-size: 40px; color: black">{{v.item}}</text>
        </cell>
        <loading onloading="onloading" display="{{loadingDisplay}}">
            <text id="loadingText">{{loadingText}}</text>
        </loading>
    </list>
</template>
<script>
    module.exports = {
        data: {
            refreshDisplay: 'show',
            loadingDisplay: 'show',
            loadingText: '上拉加载更多',
            refreshText: '下拉刷新',
            items: []
        },
        created: function () {
            for (var i = 0; i < 30; i++) {
                this.items.push({'item': '测试数据' + i});
            }
        },
        methods: {
            onrefresh: function () {
                var vm = this;
                vm.refreshDisplay = 'show'
                if (vm.items.length > 50) {
                    vm.refreshText = "没有更新了"
                    vm.refreshDisplay = 'hide'
                    return;
                }
                var len = vm.items.length;
                for (var i = len; i < (len + 20); i++) {
                    vm.items.unshift({'item': '测试数据' + i});
                }
                vm.refreshDisplay = 'hide'
            },
            onloading: function () {
                var vm = this;
                vm.loadingDisplay = 'show'
                if (vm.items.length > 30) {
                    vm.loadingText = "没有更多了!"
                    vm.loadingDisplay = 'hide'
                    return;
                }

                var len = vm.items.length;
                for (var i = len; i < (len + 20); i++) {
                    vm.items.push({'item': '测试数据' + i});
                }
                vm.loadingDisplay = 'hide'
            }
        }
    }
</script>
```
