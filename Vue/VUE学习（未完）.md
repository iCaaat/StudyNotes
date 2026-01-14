# Vue2.0

## 1. 使用vue

### 1.1 基础语法

```html
<div id='app'>
    准备容器  {{ msg }}
</div>

<script src='vue.js'>引入vue.js</script>

<script>
    // 创建vue实例
	const app = new Vue({
		el:
        	'#app',   // vue选择器
		data:
        	msg:'插值表达式数据'
    })
</script>
```

### 1.2 vue常用指令

|     vue指令     |                  作用                   |                  语法                   |     简写      |
| :-------------: | :-------------------------------------: | :-------------------------------------: | :-----------: |
|  ```v-html```   |           设置元素的innerHTML           |          ```v-html="表达式"```          |               |
|  ```v-show```   |            控制元素显示隐藏             | ```v-show="表达式"``` true显示false隐藏 |               |
|   ```v-if```    |            控制元素条件渲染             |           ```v-if="表达式"```           |               |
| ```v-else-if``` |           配合```v-if```使用            |        ```v-else-if="表达式"```         |               |
|  ```v-else```   |   配合```v-if```或```v-else-if```使用   |              ```v-else```               |               |
|   ```v-on```    |      注册事件 = 添加监听+处理逻辑       |   ```v-on:事件名="内联语句/函数名"```   |  ```@事件```  |
|  ```v-bind```   |     动态设置html的标签属性（如src）     |      ```v-bind:属性名="表达式"```       | ```:表达式``` |
|   ```v-for```   |     基于数据循环，多次渲染整个元素      | ```v-for="(item,index) in 数组/对象"``` |               |
|   ```:key```    | ```v-for```中的唯一标识，正确识别列表项 |          ```:key="唯一属性"```          |               |
|  ```v-model```  |          表单元素数据双向绑定           |          ```v-model='变量'```           |               |

### 1.3 指令修饰符

通过<span style="color:red">" . "</span>进行修饰，如：
|         修饰         |     作用     |
| :------------------: | :----------: |
|  ```@keyup.enter```  | 键盘回车监听 |
|  ```v-model.trim```  | 去除首尾空格 |
| ```v-model.number``` |    转数字    |
|  ```事件名.stop```   |   阻止冒泡   |
| ```事件名.prevent``` | 阻止默认行为 |

### 1.4 v-bind对样式控制的增强

1. 操作class，语法：`:class="对象/数组"`
   1. 对象：`:class="{ 类名: 布尔值, 类名2: 布尔值}"`，适用场景：一个类名，来回切
   2. 数组：`:class="[类名1, 类名2, 类名3]"`使用场景，批量添加或删除类
2. 操作style`:style="{样式}"`，使用场景，控制单个样式

### 1.5 v-model应用与其他表单元素

|                          表单元素                          | 绑定值  |
| :--------------------------------------------------------: | :-----: |
|                    输入框 `input:text`                     |  value  |
|                     文本域 `textarea`                      |  value  |
|                  复选框 `input:checkbox`                   | checked |
| 单选框 `input:radio`，给单选框加上`name`属性，同一组会互斥 | checked |
|                     下拉菜单 `select`                      |    \    |
|                            ...                             |   ...   |

### 1.6 vue组件方法

#### 1.6.1 computed计算属性



#### 1.6.2 watch监视器

作用：监视数据变化，执行一些业务逻辑或异步操作

1. **简单写法**（简单数据类型，直接监视）

   该方法在数据变化时执行，若不用oldValue，可不写，若是内层数据，比如`对象.值`，数据名可改为`'对象名.值'`或驼峰写法

   简单写法是一个**方法**

   ```vue
   watch: {
   	// 该方法在数据变化时执行
   	数据名 (newValue, oldValue) {     // 若不用oldValue，可不写
   		// 方法体
   	}
   }
   ```

2. **完整写法**（添加额外配置项）

   完整写法是一个**对象**

   - `deep:true`：对复杂类型深度监视
   - `immediate:true`：初始化立刻执行一次handler方法（而不是在数据修改时执行）

   ```vue
   watch: {
   	obj: {
   		deep: true,		//深度监视
   		handler (newValue) {
   			// 方法体
   		}
   	}
   }
   ```


### 1.7 vue的生命周期

#### 1.7.1 生命周期

生命周期：一个Vue实例从创建到销毁的整个过程

1. 创建阶段：new Vue()
   - 操作：响应式数据，发送初始化渲染请求
2. 挂载阶段：渲染模版
   - 操作：操作DOM
3. 更新阶段：数据修改，更新视图，循环更新
4. 销毁阶段：销毁实例

#### 1.7.2 钩子函数

每个阶段两个钩子函数，创建始/创建末

|    阶段     |     阶段始      | 说明 |   阶段末    |        说明        |
| :---------: | :-------------: | :--: | :---------: | :----------------: |
| 1. 创建阶段 | `beforeCreate`  |      |  `created`  | 发送初始化渲染请求 |
| 2. 挂载阶段 |  `beforeMount`  |      |  `mounted`  |      操作dom       |
| 3. 更新阶段 | `beforeUpdate`  |      |  `updated`  |                    |
| 4. 销毁阶段 | `beforeDestroy` |      | `destroyed` |                    |

## 2. 工程化vue2项目

### 2.1 创建与使用vue项目

|                      命令                       |     说明     |       备注       |
| :---------------------------------------------: | :----------: | :--------------: |
| `yarn global add @vue/cli`或`npm i @vue/cli -g` |   全局安装   |                  |
|                 `vue --version`                 | 查看vue版本  |                  |
|            `vue create project-name`            | 创建项目架子 |    不能有中文    |
|          `yarn serve`或`npm run serve`          |   启动项目   | package.json配置 |

### 2.2 脚手架目录文件介绍

![image-20241124210942053](https://gitee.com/jackqueen/pictures/raw/master/202411242109966.png)

1. index.html：提供一个渲染容器
2. App.vue：根组件
3. main.js：入口文件
   * 作用：导入App.vue，基于App.vue创建结构渲染index.html
   * ![image-20241124212520021](https://gitee.com/jackqueen/pictures/raw/master/202411242125705.png)
   * `render: h => h(App)`：将App.vue渲染导index.html容器中



### 2.3 组件化开发

![image-20241124213550902](https://gitee.com/jackqueen/pictures/raw/master/202411242135362.png)

1. 根组件：整个应用最上册的组件，包裹所有普通小组件，组件包括三部分：

   * **template**结构（只能有一个根节点）

   * **style**样式（可支持less，需安装包less和less-loader）

   * **script**行为

2. 普通组件的注册使用

   1. 局部注册：只能在注册的组件内使用

      * 创建 **.vue**文件（三个组成部分）

      * 在使用的组件内导入并注册：

        * 导入：`import 组件对象 from '.vue文件路径'`

        * ```vue
          export default {
          	components: {
          		'组件名':组件对象,
          	}
          }
          ```

   2.  全局注册：所有组件都能使用

      * 创建**.vue**文件（三个组成部分）
      * **main.js**中进行全局导入
        * 导入...
        * 调用Vue.component进行全局注册，语法：`Vue.component('组件名', 组件对象)`

3. 组件的使用：当成html标签使用`<组件名></组件名>`，注意：组件命名规范->**大驼峰命名法**



### 2.4 组件通信

#### 2.4.1 父子关系：props和$emit

1. **子使用父数据`props`（在子组件上自定义的一些属性）：**

   - 父组件中给子组件添加自定义属性`:自定义属性='数据'`

   - 子组件中通过（数组写法）`props: ['自定义属性', '自定义属性']`接收

   - 模版中使用

   - ![image-20241129175946689](https://gitee.com/jackqueen/pictures/raw/master/202411291759860.png)

   **参数校验**（控制台提示错误）

   - 类型校验（对象写法）
     
   - 非空校验
     
- 默认值


   - 自定义校验

     ```js
     // 基础写法
     props: {
     	自定义属性: 类型      // 类型校验
     }
     
     // 完整写法
     props: {
     	自定义属性名: {
     		type: 类型,      // 类型校验
     		required: true,    // 是否必填
     		default: 默认值,     //默认值
     		validator (value) {
     			// 自定义校验逻辑, value=传值数据
     			return 是否通过校验
     		}
     	}
     }
     ```


2. **父使用子数据`$emit`**

   - 通过$emit向父组件发送消息通知`this.$emit('自定义事件名', '数据')`

   - 父组件中对子组件监听`@自定义事件名='处理函数'`

   - 父组件方法列表中添加`处理函数(数据)`，其中形参为子组件传递的数据

   - ![image-20241129180944379](https://gitee.com/jackqueen/pictures/raw/master/202411291809420.png)

   **参数修改**

   - 自己的数据(`data()`)可改，外部参数(`props`)需要通知对应组件修改（单向数据流）
   - 若要修改，需要提供对应函数（子传父使用`$emit`方法）

   ![父子通信](https://gitee.com/jackqueen/pictures/raw/master/202411291843282.png)



#### 2.4.2 非父子关系：1. eventbus  2. provide & inject   

1. **event bus事件总线**

   1. 创建一个公共的事件总线（空vue实例） -> *utils/EventBus.js*

      *src/utils/EventBus.js*：

      ```js
      import Vue fro 'vue'
      
      const Bus = new Vue()
      
      export default Bus
      ```

   2. A组件（接收方，可有多个接收方），监听Bus实例的事件(`Bus。$on('sendMsg', (msg) => { this.msg = msg})`)

      script标签下：

      ```js
      import Bus from '../utils/EventBus'
      export default {
          created () {
              // 监听Bus事件
              Bus.$on('事件名', () => {
                  // 方法体
              })
          }
      }
      ```

   3. B组件（发送方），触发Bus实例的事件(`Bus.$emit('sendMsg', '这是一个消息')`)

      先在template下监听触发的事件

      ```html
      <button @click='clickSend'>
          发消息
      </button>
      ```

      之后script标签下：

      ```js
      import Bus from '../utils/EventBus'
      export default {
          methods: {
              clickSend() {
                  this.$emit('与接收方一致的事件名', '数据')
              }
          }
      }
      ```

   - ![eventbus](https://gitee.com/jackqueen/pictures/raw/master/202411301755136.png)

2. **provide & inject**跨层级通信

   1. 父组件provide提供数据，使用`data: { }`中的数据

      ```js
      export default {
          provide() {
              return {
                  // 普通类型[非响应式]
                  color: this.color,
                  // 复杂类型[响应式] -- 推荐
                  userInfo: this.userInfo,
              }
          }
      }
      ```

   2. 子/孙组件inject取值使用，可直接使用inject中的数据

      ```js
      export default {
          inject: ['color', 'userInfo'],
          created() {
              console.log(this.color, this.userInfo)
          }
      }
      ```



#### 通用解决方案：Vuex（适用复杂业务场景）



### 2.5 父组件与子组件数据的双向绑定

#### 2.5.1 v-model详解

`v-model='数据'`等价于`:value='数据' @input='数据 = $event.target.value'`，即v-model为value和input（事件）的合写，$event获取形参

#### 2.5.2 表单类组件封装

1. **父传子**：数据从父组件props传递过来，v-model**拆解**绑定数据

2. **子传父**：

   *父组件完整写法*：

   ```vue
   <子组件 :自定属性='父组件数据' @事件名='父组件数据 = $event'
   ```

   *子组件完整写法*：

   ```vue
   <template>
   	<select :value='自定属性' @change='处理函数'>
           
       </select>
   </template>
   
   <script>
   export default {
       props: {
           自定属性: 类型
       }
       methods: {
       	处理函数(e) {
               // console.log(e.target.value)
               this.$emit('事件名', e.target.value)
           }
   	}
   }
   </script>
   ```

#### 2.5.3 v-model简化代码

1. **子组件修改 自定属性/事件名**

   ```vue
   <script>
   export default {
       props: {
           value: String      // 自定属性修改为value
       }
       methods: {
       	处理函数(e) {
               // console.log(e.target.value)
               this.$emit('input', e.target.value)        // 事件名修改为input
           }
   	}
   }
   </script>
   ```

   

2. **父组件用v-model简化**

   ```vue
   <子组件 v-model='父组件数据'></子组件>
   ```

#### 2.5.4 .sync修饰符

作用：可实现子组件与父组件数据的双向绑定，简化代码

特点：props属性名，可以自定义，非固定为value

场景：封装弹框类的基础组件，visible属性，true显示，false隐藏

本质：就是`:属性名`和`@update:属性名`合写

*父组件*：

```vue
<BaseDialog :visible.sync="isShow" />
// 等价于
<BaseDialog :visible="isShow" @update:visible="isShow = $event"  />   // 事件监听使用
```

*子组件*：

```vue
<script>
export default {
    props: {
        visible: Boolean
    },
    this.$emit('update:visible', false)     // 事件修改为与update:visible
}
</script>
```

### 2.6. vue中的DOM操作

#### 2.6.1 ref和$refs

作用：利用`ref`和`$refs`可用于获取dom元素

特点：查找范围 -> 当前组件内

1. **获取dom**：

   - 目标标签-添加ref属性

     ```vue
     <div ref="chartRef">
     </div>
     ```

   - 恰当时机 通过`this.$refs.xxx`获取目标标签

     ```vue
     <script>
     export default {
         mounted() {
             cons
         }
     }
     </script>
     ```

2. **获取组件**：

   - 目标组件-添加ref属性

     ```vue
     <BaseForm ref='baseForm'></BaseForm>
     ```

   - 恰当时机，通过`this.$refs.xxx`，获取目标组件，就可**调用组件对象里面的方法**

     ```vue
     this.$refs.baseForm.组件方法()
     ```



#### 2.6.2 vue异步更新、$nextTick

$nextTick：等DOM更新后，才会触发执行此方法里面的方法

语法：`this.$nextTick(方法体)`，如

```vue
<script>
export default {
    methods: {
        this.$nextTick(() => {
    		this.$refs.inp.focus()
		})
    }
}
</script>
```



### 2.7. vue自定义指令

#### 2.7.1 语法与使用

自定义指令：自己定义的指令，可**封装一些dom操作**，扩展额外功能

1. **注册自定义指令**

   - 全局注册-语法：在*main.js*中定义

     ```js
     Vue.directive(
         '指令名', {
         	"inserted"(el) {        // inserted会在指令所在的元素，被插入到页面时触发
           	  	// el是指令所绑定的元素
            	 	// 可对el标签，扩展额外功能
             	el.focus()
        	 	}
     })
     ```

   - 局部注册-语法：在组件内部定义，智能在当前组件范围内使用

     ```js
     export default {
         directives: {
       	  '指令名': {
            	 inserted(el) {
            	     // 可以对el标签，扩展额外功能
            	     el.focus()
            	 }
       	  }	
     	}
     }
     ```

2. **使用**：`<div v-指令名>`

3. **指令的值**：

   - 绑定元素：

     ```js
     <div v-指令='数据' />      // 使用
     ```

   - 传值：

     ```js
     export default {
         data() {
             return {
                 数据：'值'
             }
         }
         directives: {
       	  '指令名': {
            	 inserted(el, binding) {
            	     // 可以对el标签，扩展额外功能
            	     // 方法逻辑
            	 }
     		update(el, binding) {
                 // 传值变化时执行
             }
       	  }	
     	}
     }
     ```


#### 2.7.2 v-loading的封装

本质：loading是一个蒙层，盖在盒子上

1. 设置loading类：

   ![image-20241201160037843](C:\Users\GodsCat\AppData\Roaming\Typora\typora-user-images\image-20241201160037843.png)

2. 注册*v-loading*指令（全局，局部）

   - 数据请求中：开启loading状态，添加蒙层，伪元素定位，inserted逻辑中添加loading类（css），
   - 数据请求完毕，关闭loading状态，添加蒙层，update逻辑中移除loading类

   ```js
   Vue.directive(
       'loading', {
           inserted (el, binding) {
               binding.value ? el.classList.add('loading') : el.classList.remove('loading')
           },
           update (el, binding) {
               binding.value ? el.classList.add('loading') : el.classList.remove('loading')
           }
       }
   )
   ```

3. 使用*v-loading*指令

   `<div v-loading="isLoading"></div>`

4. 设置初始值

   ```js
   data() {
       return {
           isLoading: true
       }
   }
   ```

5. 挂载后注销

   ```js
   mounted() {
       isLoading= false
   }
   ```



### 2.8 vue插槽

#### 2.8.1 基本语法

作用：自定义组件内的一些结构

**语法**：

1. 需要定制的结构部分，用`<slot></slot>`占位
2. 使用组件时，`<自定义组件>  自定义  </自定义组件>`标签内部传入结构替换slot

后备内容：`<slot> 后备内容 </slot>`，即默认内容



#### 2.8.2 具名插槽

要定制组件内多个结构，可使用具名插槽

语法：`<slot name='名字'></slot>`，即使用name属性区分不同插槽

组件标签内部使用`<template v-slot:名字></template>`分发，简化：使用`#名字`代替`v-slot:名字`，如

*组件内部定义*：

```vue
<slot name='名字1'> 后备内容 </slot>

<slot name='名字2'> 后备内容 </slot>
```

*父组件使用*：

```vue
<MyDialog>
	<template v-slot:名字1>
    	<h>				// 内部标签可自定义
            内容1
        </h>
    </template>
    <template #名字2>		// 可简写
    	<div>
            内容2
        </div>
    </template>
</MyDialog>
```



#### 2.8.3 作用于插槽

定义：定义slot插槽的同时，是可以传值的，给插槽上可以绑定数据，将来使用组件时可以使用

使用场景：封装表格组件

**使用**：

1. 给slot标签，添加属性传值

   ```vue
   <slot :id='item.id' msg='测试文本'></slot>
   ```

2. 所有添加的属性，都会被收集到一个对象中

   ```vue
   { id: 3, msg: '测试文本' }
   ```

3. 在template中，通过`#插槽名='接收对象名'`接收，默认插槽名为default，（可用`{ id }`解构）



## 3. vue2路由

### 3.1 vue中的路由

#### 3.1.1 VueRouter基本使用

路由：路径与组件的映射关系

**使用，5基础步骤**：

1. 下载：下载VueRouter模块(3.6.5)到当前工程，Vue2 -> VueRouter 3.x -> Vuex 3.x     Vue3 -> v4 -> v4

2. 引入：

   ```js
   import VueRouter from 'vue-router'
   ```

3. 安装注册：

   ```js
   Vue.use(VueRouter)
   ```

4. 创建路由对象

   ```js
   const router = new VueRouter()
   ```

5. 注入，将路由对象注入new Vue实例中，建立关联

   ```js
   new Vue({
       render: h => h(App),
       router
   }).$mount('#app')
   ```

**使用，2核心步骤**：

1. 创建需要的组件（views目录），配置路由规则

   ```js
   import 组件X from './views/组件X'     // 记得导入对应组件
   
   const router = new VueRouter({
       routes: [
           { path: '/path1', component: 组件1},		// 左路径，右组件
           { path: '/path2', component: 组件2},
           { path: '/path3', component: 组件3}
       ]
   })
   ```

   或者分开

   ```js
   const routes = [
       { path:'/path1', component:组件1 },
       { path:'/path2', component:组件2 },
       { path:'/path3', component:组件3 },
   ]
   ```

   

2. 配置导航，配置路由出口

   ```vue
   <div>
       <a href='#/path1'>路径1</a>
       <a href='#/path2'>路径2</a>
       <a href='#/path3'>路径3</a>
   </div>
   // 路径匹配的组件 显示的位置依据<router-view></router-view>的放置位置
   <div>
       <router-view></router-view>     // 显示的位置在下方
   </div>
   ```

#### 3.1.2 组件存放目录问题

.vue文件分为两类，页面组件和复用组件

页面组件 -> *views*文件夹（配合路由的）

复用组件 -> *components*文件夹

### 3.2 路由进阶

#### 3.2.1 路由模块封装

单独的router模块

在***src/router***文件夹中，新建***index.js***文件，将router相关逻辑代码放在该文件中

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
Vue.use(Vue.Router)

import 组件 from '../views/组件'    // 导入视图组件    可使用@/views/Find,@直接表示src目录下

const router =new VueRouter ({
    routes: [
        { path: '/路径1', component: 组件1 }
    ]
})

export default router	// 记得导出，这样main.js才可以使用
```

#### 3.2.2 router-link替代a标签

跳转链接可使用`<router-link to='/path1'></router-link>`



#### 3.2.3 路由重定向

#### 3.2.4 编程式导航 



## 4. vue2项目打包部署

### 4.1 打包

使用`npm run biuld`进行打包，将生成一个***dist***目录

### 4.2 部署

#### 4.2.1 部署平台：**Nginx**

部署方式：**本地部署**

注意事项：

1. 默认占用80端口，需在*conf/nginx.conf*的**server { listen    80;}**中修改端口号

#### 4.2.2 部署平台：**github**

> 见：所有学习笔记\vue2项目的打包与github部署.md

