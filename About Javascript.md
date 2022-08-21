## Javascript

### use strict

- javascript虽然一直在增加新的特性，但老的功能也没有改变

- ES5之后一些旧的特性发生了修改，但默认是不使用新特性

- 如果想要javascript脚本使用新特性，需要`"use strict"`指令来激活

  - 如果`"use strict"`写在js文件的最上面，那整个文件都是以“严格”模式进行工作

  - 如果`"use strict"`写在某个函数的上面，那么只有这个函数以严格模式运行

    ```javascript
    function(){
        'use strict'
        
        // code
    }
    ```

  - 严格模式不能在中途被取消，也不能在脚本的途中声明

- 如果使用`class`, `module`, 默认为严格模式，不需要在写`use strict`