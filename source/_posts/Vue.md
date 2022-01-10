---
title: Vue
date: 2022-01-10 14:38:05
tags:
      - 前端
      - Vue
categories:
      - 前端
description: 一些简单的Vue基础,不包含细节处理,同样内容缺失
top_img: /img/Vue.jpeg
cover: /img/Vue.jpeg
keywords:
      - Vue
      - 前端框架
---

# Vue 	



## 下载安装

https://cn.vuejs.org/v2/guide/installation.html



## 初使用



首先引进一个样例

html部分

```html
<div id = "app">{{message}}</div>
```



js部分

```html
<script>
	var vm = new Vue({
		el:"#app",
		data:{message:"aaa"}
	
	})
</script>
```



### 使用对象作为参数

使用对象直接作为数据进行绑定会进行双向绑定

```
// 我们的数据对象
var data = { a: 1 }

// 该对象被加入到一个 Vue 实例中
var vm = new Vue({
  data: data
})

// 获得这个实例上的 property
// 返回源数据中对应的字段
vm.a == data.a // => true

// 设置 property 也会影响到原始数据
vm.a = 2
data.a // => 2

// ……反之亦然
data.a = 3
vm.a // => 3
```



### object.freeze()



Object.freeze()这会阻止修改现有的 property，也意味着响应系统无法再*追踪*变化。



```html
    <div id="app">
        <div>
            {{message}}
        </div>
        
        <!--这里单击后上面的message不会变-->
        <button v-on:click="message='bbbb'">change</button>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>

        var vm=new Vue({

            el:"#app",
            data:{
                message:"aaaa"
            }

        })

        Object.freeze(vm);

    </script>
```



### js内部调用vue方法



```html
    <div id="app">
        <div>
            {{message}}
        </div>
        <button v-on:click="message='bbbb'">change</button>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>

        var vm=new Vue({

            el:"#app",
            data:{
                message:"aaaa"
            },
            computed:{
                aaa:function(){return 1;}

            }
        })

        // 直接使用会调取第三层的变量->aaaa
        console.log(vm.message);

        // 结果为->1
        console.log(vm.aaa);
        
        // 使用$可以调用vue中直接贴近的一层的key
        console.log(vm.$data);

        Object.freeze(vm);

    </script>
```



### 动态参数



```html
<a v-bind:[attributeName]="url"> ... </a>
```

这需要在vue中的data区域有一个变量名为attributeName,如果attributeName的值为class则和下面的一样

```html
<a v-bind:class="url"> ... </a>
```

动态参数表达式有一些语法约束，因为某些字符，如空格和引号，放在 HTML attribute 名里是无效的。例如：

```html
<!-- 这会触发一个编译警告 -->
<a v-bind:['foo' + bar]="value"> ... </a>
```



### v-bind和v-on的缩写

v-bind缩写

```html
<!-- 完整语法 -->
<a v-bind:href="url">...</a>

<!-- 缩写 -->
<a :href="url">...</a>

<!-- 动态参数的缩写 (2.6.0+) -->
<a :[key]="url"> ... </a>
```

v-on

```html
<!-- 完整语法 -->
<a v-on:click="doSomething">...</a>

<!-- 缩写 -->
<a @click="doSomething">...</a>

<!-- 动态参数的缩写 (2.6.0+) -->
<a @[event]="doSomething"> ... </a>
```



## 计算属性



### 举个例子

```html
  <div id="app">
        {{ccc}}
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        var vm=new Vue({
            el:"#app",
            data:{
                aaa:111,
                bbb:222
            },
            computed:{
                ccc:function(){
                    return this.aaa+this.bbb;
                }
            }

        })
    </script>
```

其中在vue中的computed就是计算属性,在HTML代码中直接使用ccc变量就会自动调用函数进行计算,并将计算后的值进行缓存如果参数变量没有变化(比如aaa和bbb)以后调用就直接从缓存中取值不会再次进行计算



### 计算属性的get和set



```html
    <div id="app">
        {{ccc}}
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        var vm=new Vue({
            el:"#app",
            data:{
                aaa:111,
                bbb:222
            },
            computed:{
                ccc:{
                    get:function(){
                    return this.aaa+this.bbb;
                },
                    set:function(value){
                        this.aaa=value;
                    }

                }
            }

        })

        console.log(vm.ccc);
    </script>
```

直接在第四层变量里面添加get和set方法

现在再运行 `vm.ccc = 1` 时，setter 会被调用，`vm.aaa` 相应地被更新。最终结果被返回为223



## 关于vue对class和style的绑定



### class



#### 简单的例子

```html
<div class="static" v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>
```



```json
data: {
  isActive: true,
  hasError: false
}
```

结果为

```html
<div class="static active"></div>
```

这里可以看到由于hasError为false所以没有成为class中的一员,而且vue绑定的class也没有覆盖前面的class



#### 使用对象修改

上面的例子直接将类写在标签上我们也可以写在vue内部如

```html
<div v-bind:class="classObject"></div>
```

```json
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

这样效果和第一个例子一样



#### 使用计算属性修改

我们也可以使用计算属性来返回一个对象

```html
<div v-bind:class="classObject"></div>
```

```json
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```



#### 使用数组修改

还可以给vue添加数组用来动态的绑定类

```html
<div v-bind:class="[activeClass, errorClass]"></div>
```

```json
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```



结果为

```html
<div class="active text-danger"></div>
```

我们可以看出div的class都是由activeClass和errorClass来决定的



也可以使用三目条件和类进行复合使用

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```



### style

#### 简单的例子

```html
<div v-bind:style="styleObject"></div>
```

```json
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

当 `v-bind:style` 使用需要添加[浏览器引擎前缀](https://developer.mozilla.org/zh-CN/docs/Glossary/Vendor_Prefix)的 CSS property 时，如 `transform`，Vue.js 会自动侦测并添加相应的前缀。-----------------总之就是vue会帮我们自动添加前缀



### 对于列表渲染

如果使用以下方法能够检测到数组或者对象需要更新

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`
- 下面三个方式不会修改原先的数组需要替换掉原来的数组
- `filter()`
- `concat()`
- `slice()`

由于 JavaScript 的限制，Vue **不能检测**数组和对象的变化。[深入响应式原理](https://cn.vuejs.org/v2/guide/reactivity.html#检测变化的注意事项)中有相关的讨论。



#### 对列表进行过滤

```html
    <div id="app">
        <div v-for="item in items">
            <li v-for="n in event(item)">
                {{n}}
            </li>
        </div>

    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var vm = new Vue({

            el:"#app",
            data:{
                items:[
                    [1,2,3,4,5],
                    [6,7,8,9,10]
                ]
            },
            methods:{
                event:function(numbers){
                    return numbers.filter(function(number){
                        return number%2==0;
                    })
                }
            }
        })
    </script>
```



### 事件处理

```html
    <div id="app">
        <button @click="great">
            aaa
        </button>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        var vm = new Vue({

            el: "#app",
            data: {},
            methods: {
                great: function (event) {
                    alert(event);
                }
            }
        })
    </script>
```

在这里great和great($event)等价

> 以$开头的变量为特殊变量,$event代表原生DOM事件



#### 事件修饰符

- `.stop` 阻止单击事件
- `.prevent` 不会重载页面
- `.capture` 优先处理绑定的事件
- `.self` 不从元素内部触发
- `.once` 只触发一次
- `.passive` 滚动事件行为

```html
<!-- 阻止单击事件继续传播 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>

<!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发 -->
<!-- 而不会等待 `onScroll` 完成  -->
<!-- 这其中包含 `event.preventDefault()` 的情况 -->
<div v-on:scroll.passive="onScroll">...</div>
```



```
使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用 v-on:click.prevent.self 会阻止所有的点击，而 v-on:click.self.prevent 只会阻止对元素自身的点击。
```



#### 按钮修饰符

只举个简单的例子键盘和鼠标修饰符自行查询

```html
<!-- 只有在 `key` 是 `Enter` 时调用 `vm.submit()` -->
<input v-on:keyup.enter="submit">
```

- `.left`
- `.right`
- `.middle`



### 表单绑定



#### 简单的双向绑定

使用v-model在表单上进行双向绑定时

```html
 
    <div id="app">

        <input type="text" v-model="msg">
        {{msg}}
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var vm = new Vue({
            el:"#app",
            data:{
                msg:"aaa"
            }
        })
    </script>
```

最简单的双向绑定

`v-model` 会忽略所有表单元素的 `value`、`checked`、`selected` attribute 的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的 `data` 选项中声明初始值。





> 下面这些没看懂啥意思

`v-model` 在内部为不同的输入元素使用不同的 property 并抛出不同的事件：

- text 和 textarea 元素使用 `value` property 和 `input` 事件；
- checkbox 和 radio 使用 `checked` property 和 `change` 事件；
- select 字段将 `value` 作为 prop 并将 `change` 作为事件。



#### 对于多选按钮(复选框)的绑定

```html
        <input type="checkbox" v-model="items" ><label for="">aaa</label>
        <input type="checkbox" v-model="items" ><label for="">bbb</label>
        <input type="checkbox" v-model="items" value="ccc"><label for="">ccc</label>
        <input type="checkbox" v-model="items" value="ddd"><label for="">ddd</label>
        {{items}}
         <script>
        var vm = new Vue({
            el:"#app",
            data:{
                msg:"aaa",
                items:[]
            }
        })
    </script>
```

> 由于上面的例子无论单击aaa或者bbb都会一起被单击而ccc和ddd都不受影响
>
> 多选使用列表
>
> 单选按钮使用字符串



#### 选择框绑定

```html
        <select name="aaa" id="aaa" v-model="selected">
            <option v-for="obj in objects" :value="obj.msg">{{obj.msg}}</option>
        </select>

        {{selected}}
 <script>
        var vm = new Vue({
            el:"#app",
            data:{
                selected:"aaa",
                msg:"aaa",
                items:[],
                objects:[
                {"msg":"aaa"},
                {"msg":"b"},
                {"msg":"c"},
                {"msg":"d"},
                {"msg":"e"},
                ]
            }
        })
    </script>
```

使用v-model来绑定选则的值,实现双向绑定如果使用v-bind为单项绑定==只能由selected中的值影响标签的值不能仍标签的值影响到selected的值==



#### 提醒

v-model只能用在特定组件上

可以使用value属性来决定==选项组==件选中时所决定的值



### 修饰符

### [`.lazy`](https://cn.vuejs.org/v2/guide/forms.html#lazy)

在默认情况下，`v-model` 在每次 `input` 事件触发后将输入框的值与数据进行同步 (除了[上述](https://cn.vuejs.org/v2/guide/forms.html#vmodel-ime-tip)输入法组合文字时)。你可以添加 `lazy` 修饰符，从而转为在 `change` 事件_之后_进行同步：

```
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg">
```

### [`.number`](https://cn.vuejs.org/v2/guide/forms.html#number)

如果想自动将用户的输入值转为数值类型，可以给 `v-model` 添加 `number` 修饰符：

```
<input v-model.number="age" type="number">
```

这通常很有用，因为即使在 `type="number"` 时，HTML 输入元素的值也总会返回字符串。如果这个值无法被 `parseFloat()` 解析，则会返回原始的值。

### [`.trim`](https://cn.vuejs.org/v2/guide/forms.html#trim)

如果要自动过滤用户输入的首尾空白字符，可以给 `v-model` 添加 `trim` 修饰符：

```
<input v-model.trim="msg">
```



## 组件

### 基础组件

#### 定义一个简单的组件

```html
<div id="app">
        <button-comp id="comp">

        </button-comp>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        Vue.component('button-comp',{
            data:function(){
               return {
                   count:0
               }
            },
            template:'<button v-on:click="count++">You clicked me {{ count }} times.</button>'
        })

        var vm = new Vue({
            el:"#comp"
        })
    </script>
```

首先在html里面定义一个新的标签为button-comp

然后在script内部进行实现template是用来描述内容的

对于data部分必须是一个函数所以每个自定义组件都有一个独立的对象他们之间的数据互不影响



自定义的组件使用props来创建变量和属性进行绑定

```html
        <title-comp id="kkk" title="aaa">

        </title-comp>
        
```



```json
      Vue.component("title-comp",{
            props:['title'],
            template:"<h3>{{title}}</h3>"
        })
```

所有组件最后都需要通过如下方式进行创建

```html
        var vm=new Vue({
            el:"#kkk"
        })
```





## 关于Vue-cil

### 安装



首先需要安装node.js

安装结束后检查版本来确定是否安装成功



下面是全局安装cnpm安装方式(国内镜像的npm)

>npm install cnpm -g



安装vue-cil

> cnpm install vue-cil -g



vue list查看可用模板项目



### 使用

首先创建一个项目(新建一个文件夹)

用cmd(管理员身份)切换到指定目录下(新建文件夹所在的位置)

然后运行

> vue init webpack 新建项目名



创建完成后会有以下提示

![image-20210517083649211](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20210517083649211.png)

- 第一行让你切换到项目目录

- 第二行让你安装依赖
- 第三行运行项目



### webpack的安装

> cnpm install webpack -g
>
> cnpm install webpack-cil -g



测试 

> webpack -v
>
> webpack-cil -v



打包项目时需要按照webpack.config.js(需要自己创建)进行打包

js格式为https://blog.csdn.net/zaichuanguanshui/article/details/53610694

```js
var webpack = require('webpack');
module.exports = {
    //确定入口
    entry: [
        'webpack/hot/only-dev-server',
        './js/app.js'
    ],
    //用于定义构建后的文件的输出。
    output: {
        path: './build',
        filename: 'bundle.js'
    },
    //关于模块的加载相关，
    module: {
        loaders: [
        { test: /\.js?$/, loaders: ['react-hot', 'babel'], exclude:     /node_modules/ },
        { test: /\.js$/, exclude: /node_modules/, loader: 'babel-loader'},
        { test: /\.css$/, loader: "style!css" },
        {test: /\.less/,loader: 'style-loader!css-loader!less-loader'}
        ]
    },
    //webpack在构建包的时候会按目录的进行文件的查找
    resolve:{
        extensions:['','.js','.json']
    },
    //当我们想在项目中require一些其他的类库或者API，而又不想让这些类库的源码被构建到运行时文件中，这在实际开发中很有必要。
    plugins: [
        new webpack.NoErrorsPlugin()
    ]
};
```



然后运行下面这条命令即可打包

>webpack



### vue-router

==在所需要的项目中进行安装==

cnpm install vue-router --save-dev



创建一个路由js并书写代码

```js
import Vue from 'vue'
import Router from 'vue-router'
import Content from '../components/Content'
import Main from '../components/Main'

//vue使用插件
Vue.use(Router);

export default new Router({
  routes:[
    {
      path:"/content",
      name:"content",
      component:Content,
    },{
      path:"/main",
      name:"main",
      component:Main,
    }

  ]
});

```

首先需要导入一个Vue,并对导入后的Router进行引用

> Vue.use(Router);

然后是编写一个默认导出的路由routes是所有的路由规则对应含一个类数组

每个类中path代表访问路由,name代表路由名称,component代表路由所对应的内容.



### 总体过程

首先新建一个文件夹比如MyVue

> vue init webpack MyVue
>
> cd MyVue
>
> cnpm install vue-router --save-dev
>
> cnpm i element-ui -S//安装ElementUI
>
> cnpm install axios
>
> cnpm install//安装所有依赖
>
> cnpm install sass-loader node-sass --save-dev
>
> npm run dev//运行

==这些都运行玩后再用idea打开,否则可能出现语句不提示功能==



#### 编写视图页

##### Login.Vue

```vue
<template>
  <el-form ref="form" :model="form" label-width="80px">
    <el-form-item label="活动名称">
      <el-input v-model="form.name"></el-input>
    </el-form-item>
    <el-form-item label="活动区域">
      <el-select v-model="form.region" placeholder="请选择活动区域">
        <el-option label="区域一" value="shanghai"></el-option>
        <el-option label="区域二" value="beijing"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="活动时间">
      <el-col :span="11">
        <el-date-picker type="date" placeholder="选择日期" v-model="form.date1" style="width: 100%;"></el-date-picker>
      </el-col>
      <el-col class="line" :span="2">-</el-col>
      <el-col :span="11">
        <el-time-picker placeholder="选择时间" v-model="form.date2" style="width: 100%;"></el-time-picker>
      </el-col>
    </el-form-item>
    <el-form-item label="即时配送">
      <el-switch v-model="form.delivery"></el-switch>
    </el-form-item>
    <el-form-item label="活动性质">
      <el-checkbox-group v-model="form.type">
        <el-checkbox label="美食/餐厅线上活动" name="type"></el-checkbox>
        <el-checkbox label="地推活动" name="type"></el-checkbox>
        <el-checkbox label="线下主题活动" name="type"></el-checkbox>
        <el-checkbox label="单纯品牌曝光" name="type"></el-checkbox>
      </el-checkbox-group>
    </el-form-item>
    <el-form-item label="特殊资源">
      <el-radio-group v-model="form.resource">
        <el-radio label="线上品牌商赞助"></el-radio>
        <el-radio label="线下场地免费"></el-radio>
      </el-radio-group>
    </el-form-item>
    <el-form-item label="活动形式">
      <el-input type="textarea" v-model="form.desc"></el-input>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="onSubmit">立即创建</el-button>
      <el-button>取消</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
  export default {
    data() {
      return {
        form: {
          name: '',
          region: '',
          date1: '',
          date2: '',
          delivery: false,
          type: [],
          resource: '',
          desc: ''
        }
      }
    },
    methods: {
      onSubmit() {
        console.log('submit!');
      }
    }
  }
</script>

<style scoped>

</style>

```



##### Main.vue

```vue
<template>
    <h1>首页
    </h1>
</template>

<script>
    export default {
        name: "Main"
    }
</script>

<style scoped>

</style>

```



#### 修改App.vue页面

```vue
<template>
  <div id="app">
<!--    <img src="./assets/logo.png">-->
<!--    <HelloWorld/>-->
    <router-link to="/login">login</router-link>
      
      //用于展示组件结果如router-link所指定的
    <router-view></router-view>
    首页
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld'

export default {
  name: 'App',
  components: {
    // HelloWorld
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

```



#### 编写主页面

```javascript
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'
import router from './router'
import elementUi from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';


Vue.config.productionTip = false;
Vue.use(router);
Vue.use(elementUi);

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  render: h => h(App)
})

```



#### 编写路由页面

```javascript
import Router from 'vue-router';
import Vue from 'vue';
import Login from '../views/login';
import Main from '../views/Main'

Vue.use(Router);

export default new Router({
  routes:[
    {
      path:"/index",
      component:Main
    },
    {
      path:"/login",
      component:Login
    }
  ]
})

```

## 钩子

### 钩子函数

- `bind`: 只调用一次，指令第一次绑定到元素时调用，用这个钩子函数可以定义一个在绑定时执行一次的初始化动作。
- `inserted`: 被绑定元素插入父节点时调用（父节点存在即可调用，不必存在于 document 中）。
- `update`: 被绑定元素所在的模板更新时调用，而不论绑定值是否变化。通过比较更新前后的绑定值，可以忽略不必要的模板更新
- `componentUpdated`: 被绑定元素所在模板完成一次更新周期时调用。
- `unbind`: 只调用一次， 指令与元素解绑时调用。



### 每个函数所带的参数



- **el**: 指令所绑定的元素，可以用来直接操作 DOM 。
- binding:一个对象，包含以下属性：
  - **name**: 指令名，不包括 `v-` 前缀。
  - **value**: 指令的绑定值， 例如： `v-my-directive="1 + 1"`, value 的值是 `2`。
  - **oldValue**: 指令绑定的前一个值，仅在 `update` 和 `componentUpdated` 钩子中可用。无论值是否改变都可用。
  - **expression**: 绑定值的表达式或变量名。 例如 `v-my-directive="1 + 1"` ， expression 的值是 `"1 + 1"`。
  - **arg**: 传给指令的参数。例如 `v-my-directive:foo`， arg 的值是 `"foo"`。
  - **modifiers**: 一个包含修饰符的对象。 例如： `v-my-directive.foo.bar`, 修饰符对象 modifiers 的值是 `{ foo: true, bar: true }`。
- **vnode**: Vue 编译生成的虚拟节点。
- **oldVnode**: 上一个虚拟节点，仅在 `update` 和 `componentUpdated` 钩子中可用。



> 这是对上面的例子

```html
<div id="app"  v-runoob:hello.a.b="message">
</div>
 
<script>
Vue.directive('runoob', {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify
    el.innerHTML =
      'name: '       + s(binding.name) + '<br>' +
      'value: '      + s(binding.value) + '<br>' +
      'expression: ' + s(binding.expression) + '<br>' +
      'argument: '   + s(binding.arg) + '<br>' +
      'modifiers: '  + s(binding.modifiers) + '<br>' +
      'vnode keys: ' + Object.keys(vnode).join(', ')
  }
})
new Vue({
  el: '#app',
  data: {
    message: '菜鸟教程!'
  }
})
</script>
```







## V指令



### v-bind

v-bind绑定当前标签的属性

```html
这样相当于用aaa变量绑定了div的value属性
<div v-bind:value="aaa"></div>

```

对于布尔 attribute (它们只要存在就意味着值为 `true`)，`v-bind` 工作起来略有不同，在这个例子中：

```
<button v-bind:disabled="isButtonDisabled">Button</button>
```

如果 `isButtonDisabled` 的值是 `null`、`undefined` 或 `false`，则 `disabled` attribute 甚至不会被包含在渲染出来的 `<button>` 元素中。



### v-once

v-once一次性绑定数据

```html
<span v-once>这个将不会改变: {{ msg }}</span>
```



### v-html

v-html用来将文本解析为代码

```html
<p>Using mustaches: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

结果:

```
mustaches:<span style="color:red">aaa</span>
Using v-html directive:aaa
```



### v-if

根据条件决定是否显示

```html
<div v-if="aaa"></div>
```



附加:https://cn.vuejs.org/v2/guide/conditional.html

由于vue尽可能的复用组件所以会有以下的情况

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```

这里添加一个切换两个template会导致这两个input的值一样举个例子

![image-20210508094253766](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20210508094253766.png)

这里输入aaaa单击按钮后

![image-20210508094318424](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20210508094318424.png)

可以看出切换了但是里面值没变如果要解决这个问题就需要给元素一个keyid



```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

这样以后每次切换都会重新渲染



>
>
>v-if和v-for建议不要再一个标签内用，v-for的优先级比较高
>
>







### v-show

和v-if用法一样但是不同的是无法使用template、v-else和v-else-if



if和show的区别：

`v-if` 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。

`v-if` 也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

相比之下，`v-show` 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。



### v-for



for遍历列表item表示元素,index代表下标(从前往后)

```html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

```json
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```



for遍历对象value代表每个键所对的值,name代表每个键,index代表下标

```html
<div v-for="(value, name, index) in object">
  {{ index }}. {{ name }}: {{ value }}
</div>
```



>在遍历数据时由于采用Object.keys()所以每次遍历结果不一定一样
>
>当 Vue 正在更新使用 `v-for` 渲染的元素列表时，它默认使用“就地更新”的策略。如果数据项的顺序被改变，Vue 将不会移动 DOM 元素来匹配数据项的顺序，而是就地更新每个元素，并且确保它们在每个索引位置正确渲染。
>
>为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 `key` attribute：
>
>不要使用对象或数组之类的非基本类型值作为 `v-for` 的 `key`。请用字符串或数值类型的值。





### v-model

> 【只能用在html表单控件上（还有自定义组件），其他组件无效】

- **限制**：

- - `<input>`
  - `<select>`
  - `<textarea>`
  - components



### 自定义指令

```javascript
// 注册一个全局自定义指令 v-focus
Vue.directive('focus', {
  // 当绑定元素插入到 DOM 中。
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})
```

使用Vue.directive进行指令注册第一个参数为指令名称("默认前面添加v-")

inserted是一个[钩子函数](##钩子)

```JavaScript
new Vue({
  el: '#app',
  directives: {
    // 注册一个局部的自定义指令 v-focus
    focus: {
      // 指令的定义
      inserted: function (el) {
        // 聚焦元素
        el.focus()
      }
    }
  }
})
```

或者使用上面的方法进行局部的注册指令





## 关于Quasar

#### 跨域

他和别的vue不同需要的所有配置要在quasar.config.js中修改

```json
devServer: {
  proxy: {
    '/api': {
      target: 'http://localhost:8001',//代理服务器
      pathRewrite: { '^/api': '' },//将以/api开头的所有替换为空
    },
  },
  https: false,
  port: 8080,
  open: true // opens browser window automatically
},
```



#### 图标的导入

***\*步骤一\****，目前Quasar支持：[Material Icons](https://material.io/icons/) 、[Font Awesome](http://fontawesome.io/icons/)、[Ionicons](http://ionicons.com/)、[MDI](https://materialdesignicons.com/) and [IcoMoon](https://icomoon.io/)。

**步骤二**，在quasar.conf.js里 ctrl+f，找到extras

![image-20210731125916822](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20210731125916822.png)

***\*步骤三\****

![image-20210731125835589](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20210731125835589.png)

http://www.quasarchs.com/vue-components/icon#%E8%87%AA%E5%AE%9A%E4%B9%89%E6%98%A0%E5%B0%84官网地址





## 报错解决

https://blog.csdn.net/chenmoupeng/article/details/107317247

![image-20210514105242812](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20210514105242812.png)

看到这个傻逼错误了吗

哪个网址就是解决它的如果不行就把它![image-20210514105330500](C:\Users\lll\AppData\Roaming\Typora\typora-user-images\image-20210514105330500.png)打开





## 路由

```
this.$router
```

使用router对象几乎可以完成所有有关url

https://router.vuejs.org/zh/官网文档

https://www.jianshu.com/p/fa0b5d919615其他讲解
