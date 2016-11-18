---
title: '(译)学习如何构建自动化、跨浏览器的JavaScript单元测试'
date: 2016-10-25 12:52:50
tags: 翻译
---
>作者：Philip Walton
译者：Yeaseon
原文链接：[点此查看](https://philipwalton.com/articles/learning-how-to-set-up-automated-cross-browser-javascript-unit-testing)


我们都知道在多个浏览器中测试我们的代码是多么的重要。至少在我们发布第一个项目的时候，我认为我们在网络开发社区做大部分工作还是相当不错的。
我们做的不够好的工作是测试代码时每一次做出的改变。
我个人对此感到很惭愧。我已经把“学习如何构建自动化、跨浏览器的JavaScript的单元测试”列在我的年度to-do清单中，但我每一次坐下来真正想要做的时候，我又退却了。虽然我肯定这一部分原因是因为我的懒惰，同时我认为这也是由于缺乏良好的可用信息在这个主题上。
有许多工具和框架（例如 Karma）宣称“要使自动化的JavaScript测试变得简单”，但以我的经验看来这些工具引入的复杂性比他们摆脱的复杂性更多。在我的工作经验中，如果你是一个专家这些工具“能工作”的很好，但对于一个初学者是很糟糕的。我想要真正了解的是这个流程是如何在引擎中工作的，以便在它出现问题的时候（总会出现问题的），我能解决它。
对我来说，充分了解这些是如何工作的最好方法就是尝试从头开始重新创建它。所以我决定去构建我自己的测试工具，然后把我的所学分享到社区中。
<!--more-->

## 手工测试流程

在我解释自动化过程之前，我认为最重要的是确保我们都在同一页面上进行手工测试工作。

毕竟，自动化是关于使用机器来关闭负载的重复部分的现有工作流程。如果你在充分理解手工过程之前尝试去开始自动化，它也不会像你理解了自动化过程一样。

在手工过程中，你写了一个你的测试文件，它可能看起来像是：

```
var assert = require('assert');
var SomeClass = require('../lib/some-class');

describe('SomeClass', function() {
  describe('someMethod', function() {
    it('accept thing A and transforms it into thing B',function() {
      var sc = new SomeClass();
      assert.equal(sc.someMethod('A'), 'B');
    });
  });
});
```

这个例子用了[Mocha](https://mochajs.org)和Node.js [资源](https://nodejs.org/api/assert.html)模块，但是重要的不是你是用的测试库或者断言库，它可以使任意一个。

在Mocha中运行Node.js，在你终端通过命令行你就能运行这个测试：

    mocha test/some-class-test.js

你需要一个带有`<script>`标签的HTML文件加载这段脚本，才能在浏览器运行这个测试，浏览器并不认识`require`声明，你需要一个像是[browserify](http://browserify.org/)或者[webpack](https://webpack.github.io/)的模块打包工具去解决这些依赖。

    browserify test/*-test.js > test/index.js

像是browserify或是webpack的模块打包工具的好处就是它能整合你的所有测试（也包括依赖）到一个单一的文件中，这样就能很容易加载到你的测试页面。

一个用Mocha写的典型测试文件看起来像是这样的：

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Tests<title>
  <link href="../node_modules/mocha/mocha.css" rel="stylesheet" />
  <script src="../node_modules/mocha/mocha.js"></script>
</head>
<body>

  <!-- A container element for the visual Mocha results -->
  <div id="mocha"></div>

  <!-- Mocha setup and initiation code -->
  <script>
    mocha.setup('bdd');
    window.onload = function() {
      mocha.run();
    };
  </script>
  <!-- The script under test -->
  <script src="index.js></script>
</body>
</html>
```

如果你不使用Node.js，那么你的起点看起来已经很像这个HTML文本了，唯一不同的是你的依赖可能需要列成一个个单独的`<script>`标签。

### 错误检测

如果一个测试由于是断言不正确，你的断言库任何时间都会抛出一个错误，这个时候你的测试框架就能发现这个错误。测试框架运行在每个测试的try/catch中来捕获可能会抛出的错误，这些错误报告会显示在你的页面中或是在console中显示这些log。

大多数的测试框架（像是Mocha）将会提供钩子，这样你就能在测试过程中让页面中的其他脚本访问测试结果。这是一个自动化测试过程的一个关键特征，因为为了自动化工作，自动化脚本需要能够提取测试脚本的结果。

### 手工方式的优点

在浏览器中手工运行测试的最大好处是，假如你的一个测试失败了你能用浏览器的开发者工具去调试它。

像是这样的一个简单例子：

```
describe('SomeClass', () => {
  describe('someMethod', () => {
    it('accepts thing A and transforms it into thing B', () => {
      const sc = new SomeClass();

      debugger:
        assert.equal(sc.someMethod('A'), 'B');
    )};
  )};
)};
```

现在当你重新打包并刷新浏览器打开开发者工具，你就可以通过你的代码，很容易定位到问题的根源所在。

相比之下，大多数流行的自动化测试框架使这变得很困难！它们提供的方便之处是它们捆绑了你的单元测试并且为你创建一个宿主的HTML页面。

在你的任何一个测试都不会失败的时候，这是很好的方式。因为当它们这样做时，就没有办法轻松地reproduce和本地调试。

## 自动化流程

手工流程有它的优点，同时也有一些缺点。打开几个浏览器去运行测试，每次你想做出改动的时候都会变得繁琐且容易出错。更不用说，我们大部分人没有安装每一个浏览器的每一个版本到我们的本地开发机器上。

如果你在认真的测试你的代码，并希望确保它的每一个变化都做适当的，那么你需要自动化这个流程。

无论你是多么的自觉，手动测试是太容易忘记或忽略，最终它不会充分利用你的时间。

但是自动化测试同样也有它的不足。过于频繁的自动测试工具引入了一个全新的问题。轻微不同的构建，测试就会变得不同，测试失败的话面临的将是痛苦的调试。

当我计划如何构建我的自动化测试系统的时候，我不想再掉进这个陷阱和失去手工测试流程的便利性。所以我决定在开始之前做一个需求列表。

毕竟，一个自动化系统如果引入了新的令人头疼的麻烦和复杂性，那它就不是一个成功的自动化系统。

### 需求

+ 我需要能够使用命令行运行测试
+ 我需要能够在本地调试失败测试
+ 我需要所有必需的依赖通过`npm`运行测试就能被安装，所以任何人查看我的代码就能很简单的运行，通过
      npm install && npm test
+ 我需要运行在CI机器上的测试流程和运行在我的开发机器一样简单。这样构建方式是相同的，并且无需检查新的变化就能调试错误。
+ 我需要所有的测试我（或者任意人）提交新的变化或者拉取请求都能在任何时间自动化运行。

有了这个粗略的列表之后，下一步就是深入到在主流的云测试如何自动化，跨浏览器测试的工作。

### 如何进行云测试

有很多云测试的供应商，每个供应商都有自己的长处和短处。我是一个开源作者，所以我只看那些提供开源项目的供应商，它们之中，只有[Sauce Labs](https://saucelabs.com/opensauce/)是唯一一个不需要我邮箱支持就能启动一个新的开源账户。

更令我吃惊的是当我真正开始钻研Sauce Labs关于JavaScript单元测试的文档是有多么简单。由于好多测试框架都有声称让单元测试变得简单，我认为这真的很难！

我前面强调了一点就是，我不想我的自动化流程和我的手工流程有什么根本上的不同。事实证明，Sauce Labs提供的自动化方法真的很像我的手工方法。

这里是所涉及的步骤：
1.你给Sauce Labs一个你测试页面的URL以及你要运行的测试的浏览器/平台列表。
2.Sauce Labs使用[selenium webdriver](http://www.seleniumhq.org/projects/webdriver/)去加载你给它的每一个浏览器和平台的组合的测试页面。
3.WebDriver检查网页是否测试失败，并将结果存储。
4.Sauce Labs将有用的结果给你。

这真的很简单。

我错误地假设你不得不把你的JavaScript代码给Sauce Labs，并且它将会运行在它的机器上，而不是它们只是去访问你给它们的URL。这样的话看起来就像手工流程了；唯一不同的是Sauce Labs去打开所有的浏览器并为你记录下结果。

### API方法

Sauce Labs有两个运行单元测试的API方法：

+ [StartJS Unit Tests](https://wiki.saucelabs.com/display/DOCS/JavaScript+Unit+Testing+Methods#JavaScriptUnitTestingMethods-StartJSUnitTests)
+ [Get JS Unit Test Status](https://wiki.saucelabs.com/display/DOCS/JavaScript+Unit+Testing+Methods#JavaScriptUnitTestingMethods-GetJSUnitTestStatus)

StartJS Unit Tests方法在你指定的浏览器/平台启动一个测试页面。

文档给了一个使用`curl`的例子：

```
curl https://saucelabs.com/rest/v1/SAUCE_USERNAME/js-tests \
   -X POST \
   -u SAUCE_USERNAME:SAUCE_ACCESS_KEY \
   -H 'Content-Type: application/json' \
   --data '{"url": "https://example.com/tests.html", "framework": "mocha", "platforms": [["Windows 7", "firefox", "27"], ["Linux", "chrome", "latest"]]}'
```

因为这是JavaScript单元测试，我将给你一个使用node模块[request](https://www.npmjs.com/package/request)的例子，如果你正在用Node.js它可能更接近你最终要做的：

```
request({
  url: `https://saucelabs.com/rest/v1/${username}/js-tests`,
  method: 'POST',
  auth: {
    username: process.env.SAUCE_USERNAME,
    password: process.env.SAUCE_ACCESS_KEY
  },
  json: true,
  body: {
    url: 'https://example.com/tests.html',
    framework: 'mocha',
    platforms: [
      ['Windows 7', 'firefox', '27'],
      ['Linux', 'chrome', 'latest']
    ]
  }
}, (err, response) => {
  if (err) {
    console.error(err);
  } else {
    console.log(response.body);
  }
});
```

你注意到body中的`framework: 'mocha'`。Sauce Labs供应商支持许多主流的JavaScript单元测试框架，包括 Mocha，Jasmine，Qunit和YUI。“支持”意味着Sauce Labs的webdriver客户端知道去哪获取测试结果。

如果你没有使用上面提到的测试框架，你能可以通过设置`framework: 'custom'`，Sauce Labs将会代替找到的全局变量`window.global_test_results`。格式化的结果被列在文档中的[自定义框架](https://wiki.saucelabs.com/display/DOCS/Reporting+JavaScript+Unit+Test+Results+to+Sauce+Labs+Using+a+Custom+Framework)一节中。

#### 让Mocha测试结果对于Sauce Labs的webdriver客户端有用

尽管你在最初的请求中告诉Sauce Labs你在使用Mocha，你仍然需要去更新你的HTML页面，去存储Sauce Labs能访问的全局变量的测试结果。

为你的HTML页面增加Mocha支持：

```
<script>
  mocha.setup('bdd');
  window.onload = function() {
    mocha.run();
  };
</script>
```

做一些事情，像下面这样：

```
<script>
  mocha.setup('bdd');
  window.onload = function() {
    var runner = mocha.run();
    var failedTests = [];

    runner.on('end', function() {
      window.mochaResults = runner.stats;
      window.mochaResults.reports = failedTests;
    });
    runner.on('fail', logFailure);

    function logFailure(test, err){
      var flattenTitles = function(test){
        var titles = [];
        while (test.parent.title){         
          titles.push(test.parent.title);
          test = test.parent;
        }
        return titles.reverse();
      };

      failedTests.push({
        name: test.title,
        result: false,
        message: err.message,
        stack: err.stack,
        titles: flattenTitles(test)
      });
    };
  };
</script>
```

在上面的代码和默认的Mocha模板中唯一不同的是分配给测试结果的变量名，就像Sauce Labs期望的格式一样叫做`window.mochaResults`。因为这个新的代码不会影响正在浏览器中运行的手工测试，你不妨就开始使用它作为默认的Mocha模板。

再次强调一点，当Sauce Labs“运行”你的测试时，它并没有做任何事，它只是单纯的访问一个页面，等到发现一个`window.mochaResults`对象，然后记录下这个结果。

#### 确定你的测试通过还是失败

StartJS Unit Tests 方法会告诉Sauce Labs去挨个在你指定的浏览器/平台运行测试，但是它不会返回测试的结果。

它返回所有工作队列中的ID，响应看起来像是这样的：

```
{
  "js tests": [
    "9b6a2d7e6c8d4fd2afeeb0ff7e54e694",
    "d38688ec7256497da6966f4523ddee76",
    "14054e68ccd344c0bed77a798a9ce1e8",
    "dbc54181f7d947458f52201ea5fcb901"
  ]
}
```

要确定你测试通过还是失败，你要调用GetJS Unit Status方法，它接手一个工作队列并且返回当前每个工作的工作状态。

这个想法是你要定期调用这个方法，知道所有工作都完成。

```
request({
  url: `https://saucelabs.com/rest/v1/${username}/js-tests/status`,
  method: 'POST',
  auth: {
    username: process.env.SAUCE_USERNAME,
    password: process.env.SAUCE_ACCESS_KEY
  },
  json: true,
  body: jsTests, // The response.body from the first API call.
}, (err, response) => {
  if (err) {
    console.error(err);
  } else {
    console.log(response.body);
  }
});
```

响应的结果看起来像是这样：

```
{
  "completed": false,
   "js tests": [
     {
      "url": "https://saucelabs.com/jobs/75ac4cadb85e415fae957f7811d778b8",
      "platform": [
        "Windows 10",
        "chrome", "latest"
       ],
       "result": {
        "passes": 29,
        "tests": 30,
        "end": {},
        "suites": 7,
        "reports": [],
        "start": {},
        "duration": 97,
        "failures": 0,
        "pending": 1
      },
      "id": "1f74a237d5ba4a47b5a42570ae1e7999",
      "job_id": "75ac4cadb85e415fae957f7811d778b8"
    },
    // ... the rest of the jobs
  ]
}
```

一旦`response.body.complete`属性值为`true`，就表示你的测试已经运行完成，然后你就可以通过检查每个工作流程的通过还是失败。

### 本地访问测试

我已经解释过Sauce Labs“运行”你的测试通过访问一个URL。当然，这意味着这个URL必须是公开在网络上可访问的链接。

有一个问题就是如果你的测试服务启动在`localhost`。

有很多解决这个问题的方案，包括[Sauce Connect](https://wiki.saucelabs.com/display/DOCS/Sauce+Connect+Proxy)（官方推荐的一种），这是一个由Sauce Labs创建的代理服务器，在Sauce Labs虚拟机和本地主机之间开启一个安全连接。

Sauce Labs是处于安全性的考虑被设计的，并且使得外部无法获得你的代码。它的缺点就是十分复杂的设置与使用。

如果你的代码涉及到安全性，它可能值得你去弄清楚Sauce Labs；如果不是的话，有许多相似的方案去更简单的解决这个问题。

我选择的方案是[ngrok](https://ngrok.com/)

#### ngrok

ngrok是一个用于创建安全隧道连接工具。它给你一个公共的URL到web服务器运行在你的本地机器上，确切的是你需要运行测试在Sauce Labs上。

如果你在虚拟机上进行开发或手动测试，你可能已经听说过ngrok，如果没有，那你应该去查阅一下了，它是极其有用的工具。

在你的机器上安装ngrok像是下载二进制文件，然后添加到你的路径中一样简单；如果你将会在Node中使用ngrok，你也需要通过npm安装它。

`npm install ngrok`

你可以用下面的代码以编程方式从Node中开始ngrok进程：

```
const ngrok = require('ngrok');

ngrok.connect(port, (err, url) => {
  if (err) {
    console.error(err);
  } else {
    console.log(`Tests now accessible at: ${url}`);
  }
});
```

只要你有一个公共的URL能访问你的测试文件，用Sauce Labs跨浏览器测试你的本地代码会变得十分容易。

## 整合碎片化

这篇文章包含了很多主题，给人的印象是自动化的，跨浏览器的JavaScript单元测试是复杂的。但情况并非如此。

我从我的角度来看这篇文章-当我试图去解决这个问题。然后回顾我之前的经验，真正复杂的是缺少解决整个流程如何工作的有效信息，和怎么样把所有的整合到一起。

一旦你了解了所有的步骤，它很简单。总结：

** 最初的手工流程 **

1. 写一个测试然后创建一个单一的HTML页面去运行它。
2. 在本地的一个或者两个浏览器中运行这个测试，确保它能工作。

** 增加自动化流程 **

1. 创建一个开源的Sauce Labs账号，获得一个用户名和访问权限。
2. 更新你的测试页面源码，以便Sauce Labs能通过JavaScript全局变量读取测试结果。
3. 用ngrok给你的本地测试页面创建一个安全隧道，这样就能在互联网公开的访问了。
4. 调用StartJS Unit Tests接口方法列出你想测试的浏览器/平台。
5. 定时调用GetJS Unit Test Status方法知道工作完成。
6. 报告结果。

## 使测试变得更容易

我知道这篇文章开头我谈了很多关于你不需要一个框架来做自动化，跨浏览器的JavaScript单元测试，我现在仍然坚信这个。然而，尽管每一步都很简单，你可能不想在每次都为项目编写代码。

我想给我的很多老项目增加自动化测试，所以对我来说打包这些逻辑到我的模块中是很有意义的。

我推荐你尝试实现一个你自己的框架，这样你就可以完全理解它是如何工作的，但如果你没有时间并且还想快速建立一个测试，我建议你使用我创建的库[Easy Sauce](https://github.com/philipwalton/easy-sauce)。

### Easy Sauce

[Easy Sauce](https://github.com/philipwalton/easy-sauce)是一个Node包和一个命令行工具，现在我为我想做跨浏览器测试的每一个JavaScript项目都使用这个包。

`easy-sauce` 命令可以设置你的HTML测试文件的路径（默认是`/test/`）、开启本地服务的端口（默认是`1337`端口）和一系列的浏览器/平台进行测试。`easy-sauce`将会在Sauce Lab’s selenium cloud运行你的测试，将日志打印在控制台并通过合适的状态码告知你测试是否通过。

npm包使它变得更方便，`easy-sauce`将会默认在`package.json`文件中查找配置选项，所以你不必分别的存储它们。好处是用户更加明确的知道你的包支持浏览器/平台。

对于`easy sauce`完整的用法介绍，请查看Github[文档](https://github.com/philipwalton/easy-sauce)。

最后，我想强调的是我专门建立这个项目来解决我的需要。虽然我认为这个项目对于很多开发人员都十分有用，但我没有计划把它变成一个功能齐全的测试解决方案。

## 结语

在这篇文章的开始，我写下了一系列的需求。在Easy Sauce的帮助下，我正努力的在任何项目中满足这些需求。

如果你还没有为你的项目做自动化、跨浏览器的JavaScript单元测试，我鼓励你给Easy Sauce一个尝试的机会。即使你不想用Easy Sauce，你至少应该了解你自己的需求或更好地了解现有的工具。

Happy testing!

> 如果你能看到这里，很感谢你的耐心阅读。
这是我翻译的第一篇技术文档，自身水平有限，所以翻译总有不当与疏漏，如有发现还请您耐心评论指出。
