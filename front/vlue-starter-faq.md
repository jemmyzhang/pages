Notes

AMD,CommonJS和UMD？
由于JS生态发展迅速，使得多个第三方组件混合在一起使用，并不一定能够配合使用。
因此出现了两种具有竞争关系的前端模块规范（AMD和CommonJS），规定开发者用约定好的模式来写代码，避免污染整个开发的生态系统。
AMD（Asynchronous Module Definition）：异步模块加载规范，一般在浏览器端，典型的应用是Require.js。  
CMD（Common Module Definition）：通用模块加载规范，一般也在浏览器端。异步加载库Sea.js就是基于CMD规范。  
```javascript
define(function (require, exports, module) {
    // load dependence
    var $ = require('jquery');
    
    //    methods
    function myFunc(){};

    //    exposed public methods
    return myFunc;
})
```

CommonJS：一个单独的文件就是一个模块。每一个模块都是一个单独的作用域；在一个文件中定义变量（以及函数或者类），都是私有的，对其他文件不可见。CommonJS规范一般应用于服务端（Node.js平台）。CommonJS加载模块采用的都是同步方式。借助Browserify这样的工具可以在浏览器端使用采用CommonJS规范的JS文件。
UMD规范：由于AMD和CommonJS规范是两种不一致的规范，所以很多人希望用一种统一的规范来支持这两种规范。UMD规范（Universal Module Definition，通用模块规范）就诞生了。

应用UML规范的JS文件其实是一个立即执行函数。函数有俩参数，一个是当前运行的环境，另一个是模块的定义体。在执行UMD规范时，会优先判断是当前环境是否支持AMD环境，然后再检验是否支持CommonJS环境，否则认为当前环境为浏览器环境（ window ）。当然具体的判断顺序其实是可以调换的。
```javascript
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD
        define(['jquery'], factory);
    } else if (typeof exports === 'object') {
        // Node, CommonJS-like
        module.exports = factory(require('jquery'));
    } else {
        // Browser globals (root is window)
        root.returnExports = factory(root.jQuery);
    }
}(this, function ($) {
    //    methods
    function myFunc(){};

    //    exposed public method
    return myFunc;
}));
```

https://www.tuicool.com/articles/nueqi27