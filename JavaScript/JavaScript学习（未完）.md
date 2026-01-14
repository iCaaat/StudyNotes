# JavaScript

## 1. JavaScript-APIs

### 1.1 DOM操作

#### 1.1.1 DOM常用标签

|                 标签                  |                 说明                 |
| :-----------------------------------: | :----------------------------------: |
|       ```document.innerText```        |         操作内容，不识别标签         |
|       ```document.innerHTML```        | 操作内容，识别标签（得不到表单内容） |
| `document.querySelector('CSS选择器')` |               获取元素               |

#### 1.1.2 DOM操作元素常用属性

语法：获取元素，`对象名.属性名="值"`

#### 1.1.3 DOM操作元素样式属性

1. 通过`.style`修改。语法：获取元素，`对象名.style.样式属性='值'`
2. 操作css样式类名`.className`修改。css中编写style类（`.类名`），语法：获取元素，`对象.className='样式类名 样式类名'`，会覆盖原类名
3. 通过`.classList`修改，语法：获取元素，`对象名.classList.操作('样式类名')`，注意类名不加点`.add()`添加样式，`.remove()`删除，`.toggle()`有就删，没有就加上（切换）

#### 1.1.4 DOM操作表单元素属性

获取元素，语法：`DOM对象.属性名='值'`，如：`表单.value='用户名'` ，`表单.type='类型'`

#### 1.1.5 自定义属性

1. `data-属性名`自定义属性
2. 使用自定义属性：获取对象，`对象.dataset`自定义属性数组，`对象.dataset.自定义属性名`获取单个属性。

#### 1.1.6 间歇函数-定时器

1. 开启定时器，语法：`let 变量名=setInterval(函数, 间隔时间)`，每隔一段时间调用这个函数，单位**毫秒**，函数名不需要加括号。
2. 定时器返回一个id数字
3. 关闭定时器，语法：`clearInterval(变量名)`

#### 1.1.7 事件监听

1. 获取对象，语法：`元素对象.addEventListener('事件类型', 要执行的函数)`
2. 版本：
   * `事件源.on事件=function() {}`------会覆盖
   * `事件源.addEventListener('事件类型', 执行函数)`
3. 事件类型
   1. 鼠标事件：
      * `click`鼠标点击
      * `mouseenter`鼠标经过
      * `mouseleave`鼠标离开
   2. 焦点事件（表单获得光标）：
      * `focus`获得焦点
      * `blur`失去焦点
   3. 键盘事件：
      * `keydown`键盘按下
      * `keyup`键盘抬起
   4. 文本事件（表单输入出发）：
      * `input`用户输入
4. 调用事件，`对象.事件函数()`，如`next.click()`

### 1.2 BOM操作

#### 1.2.1 组成

1. **Window**：浏览器窗口对象，直接使用（`window.方法`）
   - 属性：
     - history
     - location
     - navigator
   - 方法：
     - `alert()`
     - `confirm()`
     - `setInterval()`
     - `setTimeout()`
2. Navigator：浏览器对象
3. Screen：屏幕对象
4. History：历史记录对象
5. **Location**：地址栏对象
   - 获取：
     - `window.location.属性`
     - `location.属性`
   - 属性：
     - href：设置或返回完整的URL

## 2. JS对象

### 2.1 JSON基础语法

#### 2.1.1 JSON定义

JSON是一串**字符串**

sj中：

```js
var 变量名 = '{ "key1": value1, "key2": value2, "key3": value3}'
```

#### 2.1.2 JSON与JS对象转换

1. JSON字符串转换为JS对象：`JSON.parse(JSON字符串);`

   ```js
   var jsObject = JSON.parse(jsonStr);
   ```

2. JS对象转换为JSON字符串：`JSON.stringify(js对象);`

   ```js
   var jsonStr = JSON.stringify(jsObject);
   ```

   





