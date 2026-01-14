# CSS

---

## 1.基本语法

### 1.1 选择器

* 通配选择器：

  ```css
  * {}
  ```

* 元素选择器

  ```css
  HTML {}
  h2 {}
  ```

* 类选择器

  ```css
  .class-name {}
  ```

* ID选择器

  ```css
  #ID-name {}
  ```

* 交集选择器

  * 如果有元素，元素必须开头，一般不用id作为交集

  ```css
  p.class-name#id-name {}
  ```

* 并集选择器

  ```css
  .class-name1,.class-name2 {}
  
  .class-name1,
  .class-name2 {}
  ```

* 后代选择器

  * 选择父元素的所有后代

  ```css
  .parent-class-name .son-class-name {}
  ```

* 子代选择器

  * 选择父元素的直接子代元素

  ```css
  .parent-class-name > .son-class-name > .son2-class-name {}
  ```

* 兄弟选择器

  * `+` 邻近的、后面兄弟
  * `~` 所有的、后面的兄弟元素

  ```css
  div+p {}
  div~p {}
  ```

* 属性选择器

  ```css
  ```

  

> 补充：
>
> 块元素独占一行，行内元素不独占一行
