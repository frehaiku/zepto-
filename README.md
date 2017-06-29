# zepto源码解读 -- zepto v1.2.0

`zepto`源码中，中文的注释是我的理解，若有错误的地方，请发issue指出

[Core模块的中文注释请戳这里（未完待续）](zepto.js)

## 基本结构

```javascript
// IIFE直接返回所要的值
var Zepto = (function () {
    // $的真正构造函数
    // 构建一个类数组对象，数字索引为dom元素，length为dom元素的长度
    function Z(dom, selector) {
        var i, len = dom ? dom.length : 0
        for (i = 0; i < len; i++) this[i] = dom[i]
        this.length = len
        this.selector = selector || ''
    }
    
    zepto.Z = function (dom, selector) {
        return new Z(dom, selector)
    }
    
    zepto.init = function(selector, context) {
        var dom
        // 分几种情况对DOM赋值
        // 空，返回空的Z对象
        // 字符串时，1.为html fragment时，创建这个标签，2.context参数存在时，调用find函数，3.选择器时，调用qsa函数
        // 为函数时，在$(document).ready执行
        // 都不是时。1.为数组，去null和undefined后直接返回数组，2.DOM对象时，直接存进数组的一个元素中
        return zepto.Z(dom, selector)
    }
    
    // 逻辑间接指向init函数
    $ = function (selector, context) {
        return zepto.init(selector, context)
    }
    
    zepto.Z.prototype = Z.prototype = $.fn
    $.zepto = zepto
    return $
})()

// 创建Zepto和$全局对象
window.Zepto = Zepto
window.$ === undefined && (window.$ = Zepto)
```

## 读源码的方法

很多人读源码包括我，有时候在稍微复杂的代码中看不懂其中的实现，因为有些代码作者封装成一个个函数，每个函数中又调用其他函数，而在调用的函数中又调用其他函数，这一层层的调用会弄得我们很晕。比如`Core`中`find()`方法的实现：

```javascript
// find方法
// ...
  if (typeof selector == 'object')
      result = $(selector).filter(function(){
        var node = this
        return emptyArray.some.call($this, function(parent){
          return $.contains(parent, node)
        })
      })
         
// filter
// ...
    return zepto.matches(element, selector)
```

里面包含了好几个函数的实现，每个函数又有各种判断，这无疑增大了我们理解代码的难度。我的方法是在**源码看不懂的地方加上`debugger`语句，在`/test`的测试文件中运行指定的文件，通过断点观察每个变量的含义，从而理解其源码运行的步骤**