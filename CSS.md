### display

- display属性用来规定该元素应当被当做`inline element`，`block element`，`inline-block element`来显示
  - `inline element`：行内元素，元素前后不会有换行符
    - 不能设置元素的高度`height`和宽度`width`（设了也不会报错但不会生效）
    
    - 对`margin`的设置只有左右方向上的有效
    
    - 对`padding`的设置四个方向有有效
    
    - 将inline元素居中
    
      ```css
      /*在父元素中定义下面的样式*/
      text-align:center;
      ```
    
      
    
  - `block element`：块级元素，元素的前后会有换行符
    - 设置元素的宽高有效
    
    - `margin`和`padding`的设置四个方向都有效
    
    - 对block元素内容的居中
    
      - 左右居中
    
        ```css
        margin-left: auto;
        margin-right: auto;
        ```
    
      - 上下左右居中
    
        ```css
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        ```
    
        
    
  - `inline-block element`：行内块元素，元素前后没有换行符
    - 设置元素的宽高有效

### overflow

- 定义当元素内容过大时的行为
- 常用属性值
  - `visible`：没有任何裁剪（溢出当前的元素）
  - `hidden`：将超出的部分截掉
  - `scroll`：不管内容有没有超出当前元素的范围都生成横向滚动条

### text-overflow

- 定义当元素的内容发生overflow并且溢出部分被隐藏时的显示方式

- 使用`text-overflow`并不会使文本发生overflow，要使文本发生overflow需要设置其他css属性

  ```css
  overflow: hidden;
  white-space: nowrap;
  ```


### position

- 定义元素的定位类型
- `static`
  - 默认
  - 元素会按正常的文档流进行排序，不会受到top，bottom，left，right的影响
- `relative`
  - 相对定位
  - 相对于正常情况`static`下的位置（top，bottom，left，right）
  - 其自身在常规流中的位置仍然保留
  - 不指定top，bottom，left，right时，和`static`的效果是一样的
- `absolute`
  - 绝对定位
  - 相对于离自身最近的非`static`祖先元素的位置进行偏移定位。
  - 其自身在常规流中的位置不被保留
- `fixed`
  - 固定定位
  - 相对于浏览器窗口定位
  - 其自身在常规流中的位置不被保留
- `sticky`
  - 相对于距离该元素最近的有滚动条的祖先定位
  - 当元素与祖先的距离 >= 规定值时，处于相对定位状态
  - 当元素与祖先的距离 >= 规定值时，处于绝对定位状态