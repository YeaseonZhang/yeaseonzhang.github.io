---
title: YOU MIGHT NOT NEED JQUERY
date: 2018-01-15 17:25:15
tags: JQuery
---

在早期，**JQuery**在浏览器的兼容上为我们解决了痛点，但是随着浏览器/`JavaScript`的革新，我们对于浏览器的兼容要求越来越宽松，现在我们基本上兼容**IE8/IE9**就ok了。

很多**Jquery**实现的功能，我们使用原生js实现起来也不是很麻烦。

<!-- more -->

> 本文转载[YOU MIGHT NOT NEED JQUERY](http://youmightnotneedjquery.com/)，主要为了方便自己查阅

## Ajax

### JSON

> JQuery

```js
$.getJSON('/my/url', function (data) {

})
```

> IE9+

```js
var request = new XMLHttpRequest();
request.open('GET', '/my/url', true);

request.onload = function () {
    if (request.status >= 200 && request.status < 400) {
        // success
        var data = JSON.parse(request.responseText);
    } else {
        // error
    }
};

request.onerror = function () {
    // There was a connection error of some sort
};

request.send()
```

### Post

> JQuery

```javascript
$.ajax({
    type: 'POST',
    url: '/my/url',
    data: data
});
```

> IE8+

```javascript
var request = new XMLHttpRequest();
request.open('POST', '/my/url', true);
request.setRequestHeader('Content-type', 'application/x-www-form-urlencoded; charset=UTF-8');
request.send(data);
```

### Request

> JQuest

```javascript
$.ajax({
    type: 'GET',
    url: '/my/url',
    success: function (res) {

    },
    error: function () {

    }
})
```

> IE9+

```javascript
var request = new XMLHttpReQuest();
request.open('GET', '/my/url', true);

request.onload = function () {
    if (request.status >= 200 && request.status < 400 ) {
        // success
        var res = resquest.responseText;
    } else {
        // error
    }
};

request.onerror = function () {

};

request.send();
```

## Effects

### Fade In

> JQuery

```javascript
$(el).fadeIn();
```

> IE9+

```javascript
function fadeIn (el) {
    el.style.opacity = 0;

    var last = + new Date();
    var tick = function () {
        el.style.opacity = + el.style.opacity + (new Date() - last) / 400;
        last = + new Date();

        if (+ el.style.opacity < 1) {
            (window.requestAnimationFrame && requestAnimationFrame(tick)) || setTimeout(tick, 16);
        }
    };

    tick();
}

fadeIn(el);
```

### Hide

> JQuery

```javascript
$(el).hide();
```

> IE8+

```javascript
el.style.display = 'none';
```

### Show

> JQuery

```javascript
$(el).show();
```

> IE8+

```javascript
el.style.display = '';
```

## Elements

### Add Class

> JQuery

```javascript
$(el).addClass(className)；
```

> IE8+

```javascript
if (el.classList) {
    el.classList.add(className);
} else {
    el.className += ' ' + className;
}
```

### After

> JQuery

```javascript
$(el).after(htmlString);
```

> IE8+

```javascript
el.insertAdjacentHTML('afterend', htmlString);
```

### Append

> JQuery

```javascript
$(parent).append(el);
```

> IE8+

```javascript
parent.appendChild(el);
```

### Before

> JQuery

```javascript
$(el).before(htmlString);
```

> IE8+

```javascript
el.insertAdjacentHTML('beforebegin', htmlString);
```

### Children

> JQuery

```javascript
$(el).children();
```

> IE9+

```javascript
el.children;
```

### Clone

> JQuery

```javascript
$(el).clone();
```

> IE8+

```javascript
el.cloneNode(true);
```

### Contains

> JQuery

```javascript
$.contains(el, child);
```

> IE8+

```javascript
el !== child && el.contains(child);
```

### Contains Selector

> JQuery

```javascript
$(el).find(selector).length;
```

> IE8+

```javascript
el.querySelector(selector) !== null;
```

### Each

> JQuery

```javascript
$(selector).each(function (i, el) {

});
```

> IE9+

```javascript
var elements = document.querySelectorAll(selector);
Array.prototype.forEach.call(elements, function(el, i) {

});
```

### Empty

> JQuery

```javascript
$(el).empty();
```

> IE9+

```javascript
el.innerHTML = '';
```

### Filter

> JQuery

```javascript
$(selector).filter(filterFn);
```

> IE9+

```javascript
Array.prototype.filter.call(document.querySeletorAll(selector), filterFn);
```

### Find Children

> JQuery

```javascript
$(el).find(selector);
```

> IE8+

```javascript
el.querySelectorAll(selector);
```

### Find Elements

> JQuery

```javascript
$('.my #awesome selextor');
```

> IE8+

```javascript
document.querySelectorAll('.my #awesome selector');
```

### Get Attributes

> JQuery

```javascript
$(el).attr('tabindex');
```

> IE8+

```javascript
el.getAttribute('tabindex');
```

### Get Html

> JQuery

```javascript
$(el).html();
```

> IE8+

```javascript
el.innerHTML;
```

### Get Outer Html

> JQuery

```javascript
$('<div>').append($(el).clone()).html();
```

> IE8+

```javascript
el.outerHTML;
```

### Get Style

> JQuery

```javascript
$(el).css(ruleName);
```

> IE9+

```javascript
getComputedStyle(el)[ruleName];
```

### Get Text

> JQuery

```javascript
$(el).text();
```

> IE9+

```javascript
el.textContent;
```

### Has Class

> JQuery

```javascript
$(el).hasClass（className);
```

> IE8+

```javascript
if (el.classList) {
    el.classList.contains(className);
} else {
    new RegExp('(^| )' + className + '( |$)', 'gi').test(el.className);
}
```

### Matches

> JQuery

```javascript
$(el).is($(otherEl));
```

> IE8+

```javascript
el === otherEl
```

### Matches Selector

> JQuery

```javascript
$(el).is('.my-class');
```

> IE9+

```javascript
var matches = function (el, selector) {
    return (el.matches || el.matchesSelector || el.mozMatchesSelector || el.webkitMatchesSelector || el.oMatchesSelector).call(el, selector);
};

matches(el, '.my-class');
```

### Next

> JQuery

```javascript
$(el).next();
```

> IE9+

```javascript
el.nextElementSibling
```

### Offset

> JQuery

```javascript
$(el).offset();
```

> IE8+

```javascript
var rect = el.getBoundingClientRect();

{
    top: rect.top + document.body.scrollTop,
    left: rect.left + document.body.scrollLeft
}
```

### Offset Parent

> JQuery

```javascript
$(el).offsetParent();
```

> IE8+

```javascript
el.offsetParent || el
```

### Outer Height

> JQuery

```javascript
$(el).outerHeight();
```

> IE8+

```javascript
el.offsetHeight;
```

### Outer Height With Margin

> JQuery

```javascript
$(el).outerHeight(true);
```

> IE9+

```javascript
function outerHeight(el) {
    var height = el.offsetHeight;
    var style = getComputedStyle(el);

    height += parseInt(style.marginTop) + parseInt(style.marginBottom);
    return height;
}

outerHeight(el);
```

### Parent

> JQuery

```javascript
$(el).parent();
```

> IE8+

```javascript
el.parentNode;
```

### Position

> JQuery

```javascript
$(el).position();
```

> IE8+

```javascript
{
    left: el.offsetLeft,
    top: el.offsetTop
}
```

### Position Relative To Viewport

> JQuery

```javascript
var offset = el.offset();
{
    top: offset.top - document.body.scrollTop,
    left: offset.left - document.body.scrollLeft
}
```

> IE8+

```javascript
el.getBoundingClientRect();
```

### Prepend

> JQuery

```javascript
$(parent).prepend(el);
```

> IE8+

```javascript
parent.insertBefore(el, parent.firstChild);
```

### Prev

> JQuery

```javascript
$(el).prev();
```

> IE9+

```javascript
el.previousElementSibling
```

### Remove

> JQuery

```javascript
$(el).remove();
```

> IE8+

```javascript
el.parentNode.removeChild(el);
```

### Remove Class

> JQuery

```javascript
$(el).removeClass(className);
```

> IE8+

```javascript
if (el.classList) {
    el.classList.remove(className);
} else {
    el.className = el.className.replace(new RegExp('(^|\\b)' + className.split(' ').join('|') + '(\\b|$)', 'gi', ' ');
}
```

### Replace From Html

> JQuery

```javascript
$(el).replaceWith(string);
```

> IE8+

```javascript
el.outerHTML = string;
```

### Set Attributes

> JQuery

```javascript
$(el).attr('tabindex', 3);
```

> IE8+

```javascript
el.setAttribute('tabindex', 3);
```

### Set Html

> JQuery

```javascript
$(el).html(string);
```

> IE8+

```javascript
el.innerHTML = string;
```

### Set Style

> JQuery

```javascript
$(el).css('border-width', '20px');
```

> IE8+

```javascript
el.style.borderWidth = '20px';
```

### Set Text

> JQuery

```javascript
$(el).text(string);
```

> IE9+

```javascript
el.textContent = string;
```

### Siblings

> JQuery

```javascript
$(el).siblings();
```

> IE9+

```javascript
Array.prototype.filter.call(el.parentNode.children, function() {
    return child !== el;
});
```

### Toggle Class

```javascript
$(el).toggleClass(className);
```

> IE9+

```javascript
if (el.classList) {
    el.classList.toggle(className);
} else {
    var classes = el.className.split(' ');
    var existingIndex = classes.indexOf(className);

    if (existingIndex >= 0) {
        classes.splice(existingIndex, 1);
    } else {
        classes.push(className);
    }
    el.className = classes.join(' ');
}
```

## Events

### Off

> JQuery

```javascript
$(el).off(eventName, eventHandler);
```

> IE9+

```javascript
el.remoreEventListener(eventName, eventHandler);
```

### On

> JQuery

```javascript
$(el).on(eventName, eventHandler);
```

> IE9+

```javascript
el.addEventListener(eventName, eventHandler);
```

### Ready

> JQuery

```javascript
$(document).ready(function () {

})
```

> IE9+

```javascript
function ready(fn) {
    if (document.attachEvent ? document.readyState === 'complete' : document.readyState !== 'loading') {
        fn();
    } else {
        document.addEventListener('DOMContentLoaded', fn);
    }
}
```

### Tigger Custom

> JQuery

```javascript
$(el).trigger('my-event', {some: 'data'});
```

> IE9+

```javascript
if (window.CustomEvent) {
    var event = new CustomEvent('my-event', {detail: {some: 'data'}});
} else {
    var event = document.createEvent('CustomEvent');
    event.initCustomEvent('my-event', true, true, {some: 'data'});
}

el.dispathchEvent(event);
```

### Trigger Native

> JQuery

```javascript
$(el).trigger('change');
```

> IE9+

```javascript
var event = document.createEvent('HTMLEvents');
event.initEvent('change', true, false);
el.dispatchEvent(event);
```

## UTILS

### Bind

> JQuery

```javascript
$.proxy(fn, context);
```

> IE9+

```javascript
fn.bind(context);
```

### Array Each

> JQuery

```javascript
$.each(array, function (i, item) {

});
```

> IE9+

```javascript
array.forEach(function(item, i) {

});
```

### Deep Extend

> JQuery

```javascript
$.extend(true, {}, objA, objB);
```

> IE8+

```javascript
var deepExtend = function (out) {
    out = out || {};
    for (var i = 1; i < arguments.length; i++) {
        var obj = arguments[i];

        if (!obj)
            continue;
        for (var key in obj) {
            if (obj.hasOwnProperty(key)) {
                if (typeof obj[key] === 'object') {
                    out[key] = deepExtend(out[key], obj[key]);
                } else {
                    out[key] = obj[key];
                }
            }
        }
    }

    return out;
}
```

### Extend

> JQuery

```javascript
$.extend({}, objA, objB);
```

> IE8+

```javascript
var extend = function (out) {
    out = out || {};

    for (var i  = 1; i < arguments.length; i++) {
        if (!arguments[i]) {
            continue;
        }
        for (var key in arguments[i]) {
            if (arguments[i].hasOwnProperty(key)) {
                out[key] = arguments[i][key];
            }
        }
    }

    return out;
}

extend({}, objA, objB);
```

### Index Of

> JQuery

```javascript
$.inArray(item, array);
```

> IE9+

```javascript
array.indexOf(item);
```

### Is Array

> JQuery

```javascript
$.isArray(arr);
```

> IE9+

```javascript
Array.isArray(arr);
```

### Map

> JQuery

```javascript
$.map(array, function (value, index){

});
```

> IE9+

```javascript
array.map(function (value, index) {

});
```

### Now

> JQuery

```javascript
$.now();
```

> IE9+

```javascript
Date.now();
```

### Parse Html

> JQuery

```javascript
$.parseHTML(htmlString);
```

> IE9+

```javascript
var parseHTML = function (str) {
    var tmp = document.implementation.createHTMLDocument();
    tmp.body.innerHTML = str;
    return tmp.body.children;
};

parseHTML(htmlString);
```

### Parse JSON

> JQuery

```javascript
$.parseJSON(string);
```

> IE8+

```javascript
JSON.parse(string);
```

### Trim

> JQuery

```javascript
$.trim(string);
```

> IE9+

```javascript
string.trim();
```

### Type

> JQuery

```javascript
$.type(obj);
```

> IE8+
```javascript
Object.prototype.toString.call(obj).replace(/^\[object (.+)\]$/, '$1').toLowerCase();
```
