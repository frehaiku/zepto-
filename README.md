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
