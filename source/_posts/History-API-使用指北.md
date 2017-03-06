---
title: History API 使用指北
date: 2017-03-03 17:54:22
tags: HTML5 History 
---

{% asset_img html5-history.png History API %}

HTML5 History API，基本上是为了SPA(单页应用)而生。

History API能够在不刷新页面的情况下，通过和`url`匹配历史堆栈中的数据取出来，这样就能大大减少数据请求，提高用户体验。

其实说实话，能不能提高用户体验，我不知道；在历史记录中切换（前进、后退）真的是如丝般顺滑。

<!-- more -->

### History API 简介

在浏览器环境下，我们可以通过`window.history`访问我们的浏览器访问历史。
{% asset_img console.png %}
可能说的有点问题，你只能得到三个值`length`,`scrollRestoration`和`state`。其中只有`state`的值是我们需要的，接下来会提到。

#### `back()`

`back()`方法相当于点击浏览器的后退按钮。

```
window.history.back();
```

#### `forward()`

`forward()`方法相当于点击浏览器的前进按钮。

```
window.history.forward();
```

#### `go(n)`

`go(n)`方法允许你在历史**session**前进或者后退`n`次。

```
// Go back two entries.
window.history.go(-2);

// Go forward 3 entries.
window.history.go(3);
```

#### `length`

就是我们上文在浏览器得到的`length`属性。

下面就是我们的重头戏，**HTML5 History API**，上面的属性方法只是简单的热身，你可能不需要热身。

### HTML5 History API

HTML5 History API包含两个方法和一个事件。

+ `pushState()`
+ `replaceState()`
+ `popstate`

在详细介绍之前，我们先来看看各个浏览器对于HTML5 History API支持情况。

{% asset_img caniuse.png caniuse.com %}

可以说现在主流的浏览器对于HTML5 History API支持都是很好的，不过也要考虑向下兼容。

```
if (!!history.pushState) {
    // support
} else {
    // dont support
}

```

#### `pushState()`

```
pushState(state, title, url)
```
+ `state`: 传递给`history.state`
+ `title`: 似乎浏览器还没有很好支持，传`null`就好
+ `url`: 可选，这个参数会改变你的浏览器`url`

这个方法是在浏览器堆栈历史中`push`一条新的数据，然后将指针指向这条数据。

#### `replaceState()`

```
replaceState(state, title, url)
```
`replace()`方法与`pushState()`方法类似，主要是能够替换更新`pushState()`的`state`数据。

参数描述详见`pushState()`的描述。

#### `popstate`事件

当用户点击浏览器的前进/后退按钮，`popstate`事件会被调用。
```
window.addEventListener('popstate', function (event) {
    // update the page content
});

// or 
window.onpopstate = function (event) {
    // update the page content
}
```

以上就把**History API**简略介绍了一下，详细说明请访问[MDN](https://developer.mozilla.org/en-US/docs/Web/API/History)。

### 手把手实战

#### 需求分析

网上也不乏介绍H5 History API应用的文章，大概也都是比较简单的Demo。

{% asset_img demo.png http://html5demos.com/history %}

点击链接**first、second、third..**,`url`会改变为`http://html5demos.com/history#fitst`,`http://html5demos.com/history#second`...

获取不同`#first`,`#second`的数据，渲染页面中的数据。具体实现[源码](http://blog.teamtreehouse.com/getting-started-with-the-history-api)。

本文，我想介绍的不是这种demo，而是比较实际的项目开发。

实现一个文件系统的浏览页面，通过`url`哈希值`#...`记录文件夹的路径。

例如，`www.demo.com/filesystem.html#abc/`就是根目录下`abc`文件夹；`www.demo.com/filesystem.html#abc/def/`代表`abc`文件夹下的`def`文件夹。

{% asset_img fs.jpg file system %}

如上图，该目录下有文件和文件夹，点击**文件**会直接在浏览器打开该文件或者下载，点击**文件夹**会进入该文件夹，页面显示该文件夹的内容。

这样就需要我们把`url`的`hash`值动态改变，然后根据`hash`值确定所在文件路径向后台获取资源。

#### 前后端接口API

##### 显示目录

请求：
```
GET /api/v1/fileproxy/pub/abc/ HTTP/1.1
```

响应：
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{
    "error": 0,
    "errormsg": "success",
    "data": [
        {
            "name": "a.txt",
            "size": 1234,
            "time": 1235153531,
            "isdir": false,
        },
        {
            "name": "a.txt",
            "size": 1234,
            "time": 1235153531,
            "isdir": false,
        }
    ]
}
```

##### 打开/下载文件

请求：
```
GET /api/v1/fileproxy/pub/abc/a.txt HTTP/1.1
```

从后端获取到的文件信息包括`name`文件(夹)名,`size`大小,`time`修改时间,`isdir`是否是文件夹。

#### 流程

+ `initGetfile()`
+ `openDir(str)`
+ `window.onpopstate`

`initGetfile()`函数，首次进入或者刷新页面自动执行该函数，渲染文件列表。

`openDir(str)`函数，当点击文件夹时调用该函数，获取新的文件列表。

`window.onpopstate`也就是`popstate`事件。

在没有使用`History API`时，是通过`hashchange`事件来触发文件列表的更新，这就导致每一次前进后退都会发起一次`ajax`请求，没有把之前请求过的数据进行缓存，影响用户体验。

> 对了在此要声明一下，`psuhState()`和`replaceState()`不会触发`hashchange`事件

怎么理解呢，就是当你通过`psuhState()`和`replaceState()`的第三个参数`url`对于hash值有更改的时候，也不会触发`hashchange`事件。

**Chrome 和 Safari浏览器在重载页面的时候会触发`popstate`事件，Firefox浏览器不会。**

#### 重头戏

下面将会贴上我的代码，使用Vue2 和 [axios](https://github.com/mzabriskie/axios)实现。

##### HTML
```
<div class="file-list">
    <div class="weui-cells">
        <a v-if="fileList.length" v-for="item in fileList" href="javascript:;" class="weui-cell weui-cell_access">
                <!-- Dir -->
                <div v-if="item.isdir">
                    <div class="weui-cell__hd">
                        <span class="icon-size an-folder"></span>
                    </div> 
                    <div class="weui-cell__bd" @click="openDir(item)">
                        <p>{{ item.name }}</p>
                    </div>
                </div>
                <!-- File -->
                <div v-else>
                    <div v-else class="weui-cell__hd">
                        <span class="icon-size an-file"></span>
                    </div>
                    <div class="weui-cell__bd" @click="openFile(item)">
                        <p>{{ item.name }}</p>
                    </div>
                
                </div>
        </a>
        <a v-if="!fileList.length" class="weui-cell weui-cell_access" href="javascript:;"> 
            <div class="weui-cell__bd"> 
                    <p>No such file or directory</p>
            </div>
        </a>
    </div>
</div>
```
将文件夹和文件分成两类进行渲染，绑定不同的点击函数，并传递参数为文件(夹)名，前面的前后端API也提示我们是通过`name`进行请求。

##### JS
```
(function (exports) {
    exports.app = new Vue({
        el: '.file-list',

        data: {
            haveFile: true,
            fileList: [],
        },

        mounted: fucntion () {  // 挂载之后，自动执行
            this.$nextTick(function () {
                this.initGetfile();
            })
        }，

        methods: {
            initGetfile: function () {
                var _this = this;
                var hash = _this.getHash();
                var url = '/api/v1/fileproxy/' + hash;
                axios.get(url)
                    .then(function (res){
                        _this.fileList = res.data.data;
                        
                        // replaceState()
                        history.replaceState(_this.fileList, null);

                        if (!res.data.data.length) {
                            _this.haveFile = false;
                        } else {
                            _this.haveFile = true;
                        }
                    })
            },

            openDir: function (item) {
                var _this = this;
                _this.loadFile = true;
                var hash = _this.getHash();
                var url = '/api/v1/fileproxy/-/' + hash + item.name +'/';

                axios.get(url)
                    .then(function (res) {
                        _this.fileList = res.data.data;
                       
                        _this.loadFile = false;

                        var newUrl = window.location.href + item.name + '/';

                        // pushState()
                        history.pushState(_this.fileList,null, newUrl);

                        if (!res.data.data.length) {
                            _this.haveFile = false;
                        } else {
                            _this.haveFile = true;
                        }
                    });
            },

            openFile: function (item) {
                var _this = this;
                var hash = _this.getHash();
                var url = '/api/v1/fileproxy/-/' + hash + item.name;
                window.open(url);
            },

            getHash: function () {
                var hash = window.location.hash.substr(1);
                if (hash.substr(-1) != '/') {
                    hash += '/';
                }
                return hash;
            },

        }
    });

    window.onpopstate = function (e) {
        console.log('pop state');
        console.log('state', JSON.stringify(e.state));
        if (e.state != null) {
            app.fileList = e.state;
        } else {
            window.location.reload();
        }
    }
})(window);
```

以上就是我完成基本功能的源码，添加**History API**到项目中并没有做太多改动：

1. 增加`pushState()`和`repalceState()`函数

2. 替换原有的`hashchange`事件为`popstate`事件。

### 结语

本没有对源码做过多的分析，因为可能需求不一样采取的处理方法也不一样，我只是提供一个`#abc/def...`这种hash值比较复杂需求的一种实现。

希望能够对你有所启发，也是现学现卖，如有不妥之处，望指正。

