---
title: CSS布局
date: 2017-07-05 11:05:06
tags:
---


由于最近工作比较饱和，也没有太多的心思去写博客。今天更新一篇，我们来谈谈圣杯布局和双飞翼布局。

<!-- more -->

### 圣杯布局

> HTML

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Holy Grail Layout</title>
  <link rel="stylesheet" href="./base.css">
</head>
<body>
    <div class="container">
      <div class="main"></div>
      <div class="sub"></div>
      <div class="extra"></div>
    </div>
</body>
</html>
```

> CSS

```
body {
  min-width: 600px;
}

.container {
  padding-left: 210px;
  padding-right: 190px;
}

.main {
  float: left;
  width: 100%;
  height: 300px;
  background-color: rgba(255, 0, 0, .5);
}

.sub {
  position: relative;
  left: -210px;
  float: left;
  width: 200px;
  height: 300px;
  margin-left: -100%;
  background-color: rgba(0, 255, 0, .5);
}

.extra {
  position: relative;
  right: -190px;
  float: left;
  width: 180px;
  height: 300px;
  margin-left: -180px;
  background-color: rgba(0, 0, 255, 0.5);
}
```

### 双飞翼布局

> HTML

```
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Double Wing Layout</title>
  <link rel="stylesheet" href="./base.css">
</head>

<body>
  <div class="main-wrapper">
    <div class="main"></div>
  </div>
  <div class="sub"></div>
  <div class="extra"></div>
</body>

</html>
```

> CSS

```
.main-wrapper {
  float: left;
  width: 100%;
}

.main {
  height: 300px;
  margin-left: 210px;
  margin-right: 190px;
  background-color: rgba(255, 0, 0, .5);
}

.sub {
  float: left;
  width: 200px;
  height: 300px;
  margin-left: -100%;
  background-color: rgba(0, 255, 0, .5);
}

.extra {
  float: left;
  width: 180px;
  height: 300px;
  margin-left: -180px;
  background-color: rgba(0, 0, 255, .5);
}
```
