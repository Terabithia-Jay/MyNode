

## 简介

- MVVM：Model - View - View Model
- vue负责vm的工作（视图模型），通过vue可以将视图和模型相关联。
  - 当模型发生变化时，视图会自动更新
  - 也可以通过视图去操作模型

## 

## 1.通过script标签使用Vue

*适用于老项目改造成*vue

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">
    <!-- 如果直接将模板定义到网页中就必须符合html规范（大小写都会转为小写），但是如果有自定义标签（有大写）就不能用这个方			法，只能用template -->
    <!-- <button @click='count++'>点我</button>--点了{{count}}下 -->
    <!-- 如果同时存在div里写内容和在组件里写template，会优先显示template，div里的则会被清除覆盖 -->
</div>
<script>
	// 组件 --> 组件生成组件实例 --> 虚拟DOM --> DOM（在页面中呈现）
    // 1、创建一个根组件Root，Vue3中组件就是一个普通的js对象，组件用来创建组件实例，组件是组件实例的模板
	const Root = {
            data() {
                return {
                    // data方法返回的对象，其属性message会自动添加到组件实例中
                    message: "Vue真好",
                    count: 0
                }
            },
            // 在模板template中可以直接访问组件实例中的属性message，用插值语法{{ 属性 }}
            // 定义模板的三种方式：
        			// 1、在组件中通过template属性指定
        			// 2、直接在网页的根元素中指定(上面div内)   不用
        			// 3、render()	不用
            template: `
                <h1>哈哈哈 {{message}} </h1>
                // 绑定点击事件 @click
                <button @click='count++'>点我</button>--点了{{count}}下
            `
        	render()
     }
    
    // 2、创建app实例
    const app = Vue.createApp(Root)
    
    // 3、将实例在页面中挂载
    app.mount("#app")
</script>
```

## 2.通过vite使用Vue

*脚手架的搭建流程*

- 安装 vite： `yarn add -D vite` 和 vue： `yarn add vue`

- 配置快捷键

```json
"scripts": {
    "dev": "vite --open",	// 启动
    "build": "vite build",	// 打包
    "preview": "vite preview"	// 预览
  }
```

- 创建src和里面的 index.js(主文件入口) 和 App.js(根组件)和 components文件夹里放MyButton.js(App.js的子组件)，创建index.html

```js
// index.js：
	// 引入vue
		// 这里引入的vue默认不支持通过template属性来设置模板（页面不显示）
			// 换这个就可以：import { createApp } from "vue/dist/vue.esm-bundler.js"
	import { createApp } from "vue"
	// 引入根组件
	import App from "./App"
	// 创建应用挂载到页面
	createApp(App).mount("#app")


// APP.js：
	// 引入子组件
	import MyButton from "./MyButton"
	// 创建一个子组件
	export default {
    	data() {
        	return {
            	message: "我爱vue"
        	}
    	},
    	// 在组件中注册(components)子组件
    	components:{
            // "my-button": MyButton	这是为了能在html直接写<my-button>使用的，但不建议直接写在html里
        	// MyButton:MyButton
        	MyButton
    	},
    	template: `
        	<h1>{{message}}</h1>
        	<MyButton></MyButton>
        	<MyButton></MyButton>
        	<MyButton></MyButton>
    	` // 使用子组件用自定义标签MyButton，各自独立的按钮
	}


// MyButton.js:
	// App.js的子组件
	export default {
    	data() {
        	return {
            	count: 0
        	}
    	},
    	template: `
        	<button @click="count++">点我一下<button><span>{{count}}</span>
    	`
	}


// index.html：
	<script type="module" src="./src/index.js"></script>
	<div id="app">
        <!-- <h1>{{msg}}</h1> -->	不建议写在html里
        <!-- <my-button></my-button> -->	不建议写在html里
    </div>
```

- 启动 `yarn dev`

## 3.通过单文件组件使用vue

*因为使用上面的方法在template字符串里写，很麻烦，单文件组件不需要template属性*

- 目录下创建App.vue文件

- .vue结尾的文件格式本身不能被浏览器识别，必须被构建工具vite打包后才可以使用

  ​	一、安装插件 `yarn add -D @vitejs/plugin-vue`

  ​	二、配置文件：创建 vite.config.js 文件

  ```js
  // 引入插件
  import vue from "@vitejs/plugin-vue"
  // 配置插件
  export default {
      plugins:[vue()]
  }
  ```

  ​	三、编写代码

  ```js
  // App.vue:
  	<script>
      	// 引入MyButton子组件
      	import MyButton from "./components/MyButton.vue"
      	// 编写组件的代码
      	export default {
          	data() {
              	return {
                  	msg: "我爱vue"
              	}
          	},
          	components:{
              	MyButton
          	}
      	}
  	</script>
  	<!-- 编写模板 -->
  	<template>
      	<h1>{{ msg }}</h1>
      	<MyButton></MyButton>
  	</template>
  
  
  // MyButton.vue
  	<script>
      	// App.vue的子组件
      	export default {
          	data() {
              	return {
                  	count: 0,
              	}
          	},
      	}
  	</script>
  	<template>
      	<h2>子组件</h2>
      	<button @click="count++">点我一下</button><span>{{count}}</span>
  	</template>
  
  
  // index.js
  	// 引入vue
  	import { createApp } from "vue"
  	// 引入根组件
  	import App from "./App.vue"
  	// 创建应用挂载到页面
  	const vm = createApp(App).mount("#app")
  ```



## 4.自动创建vue项目

1. 安装：`yarn create vue`
2. 安装依赖 ：`yarn`
3. 启动： `yarn dev`



## 选项式API	

- data( ) { } 组件的实例vm：（数据代理 -- data(){} -- $data）

  ```js
  const app = createApp(App)
  const vm = app.mount('#app')
  ```

  - 组件的实例是一个Proxy对象（代理对象），这个对象也可以从 App.vue 文件中的默认导出中的 data(){} 函数的 this 获取

  - data(){} 中return返回的是一个对象，vue会对其进行响应式数据代理，只有在data的return里面才会代理

  - vm.$data 是实际的代理对象，vm.$data.msg 等于vm.msg，只有往 $data 加数据才会变为数据代理，响应式

  - 在data里 this.$data.xxx = "xxx" 可以动态响应数据（不要这么做），this.xxx 不行，不是数据代理

    

  - 深层响应式：在data中return的里面，会递归每一层的数据响应式代理

    - 如果数据较多不需要递归深层响应代理：（一层）

      1. 引入shallowReactive

      ```js
      improt { shallowReactive } from "vue"
      ```

      2. 在data里 return shallowReactive 把数据包裹起来

      ```js
      <script>
      	export default {
          	data() {
              	return shallReactive (){
                  	msg: "哈哈",
                  	stu: {
                      	name: 孙悟空,
                      	age: 18,
                      	gender: "男",
                      	friends: {
                          	name: "猪八戒"
                      	}
                  	},
                      arr: ["123", "456", "789"]
              	}
          	}
      	}
      </script>
      <template>
      	<h1>{{ msg }}</h1>
          <h1>{{ stu.name }}</h1>
          <h1>{{ arr[0] }}</h1>
      </template>
      
      // vm.msg	vm.stu.name
      ```
      

  ### methods: {  }

  - data( ) { } 用来指定实例对象中的响应式属性
  - methods: { } 用来指定实例对象中的方法，它是一个对象，可以在里面定义多个方法（函数），会挂载到组件实例上，可以通过组件实例来调用这些方法，methods中的函数的 this 会被自动绑定为组件实例（proxy，也就是vm，也会数据代理）

    ```js
    <script>
    	export default {
        	data() {
            	return {
                    msg: "哈哈"
                }
        	},
        	methods: {
            	sum(a, b) {
                	return a + b
            	},
                changeMsg() {
                    this.msg = "呵呵呵"
                }
        	}
    	}
    </script>
    <template>
        <h1>{{ msg }}</h1>
        <h1>{{ sum(2, 5) }}</h1>
    	<button @click="changeMsg">点我一下</button>
    </template>
    
    // vm.sum(123, 456)
    ```

  

  ### computed: {  }

  - 用来指定计算属性，也会挂载到组件实例上，数据代理

  - computed 能做的 methods 也能做，但是有区别：
    1. computed 只在其依赖的数据（比如computed里面计算的age属性，外部没有修改age的值，而修改了别的值，这样computed不会被调用）发生变化时才会重新执行（渲染），computed 会对数据进行缓存，methods 没有
    2. 而 methods 每次组件重新渲染都会调用
    3. 在计算属性 computed 的 getter 中，尽量只做读取相关的逻辑：只读数据不改数据。（methods 可以改）

       ```js
       // 选项式API
       <script>
       	export default {
           	data() {
               	return {
                   	stu: {
                           name: "孙悟空",
                           age: 18,
                           gender: "男"
                       }
               	}
           	},
           	methods: {
               	sum(a, b) {
                   	return a + b
               	},
                   changeMsg() {
                       this.msg = "呵呵呵"
                   },
                   getInfo() {
                       return this.stu.age >= 18 ? "成年人" : "未成年人"
                   }
           	},
               computed: {
                   info() {	
                       return this.stu.age >= 18 ? "成年人" : "未成年人"
                   }
               }
       	}
       </script>
       <template>
           <h1>{{ stu.name }} -- {{ stu.age }} -- {{ stu.gender }}</h1>
       	<h2>评语：{{ info }}<h2>		// computed里调info不需要写(),而如果在methods里写方法调用就必须写()
           <h1>{{ sum(2, 5) }}</h1>
       	<button @click="changeMsg">点我一下</button>	// 这里click调用changeMsg是methods方法可不写()
       </template>
       ```

       




​	



## 组合式API：setup

### reactive( )  

```js
// 老式写法：
<script>
    import { reactive } from 'vue'
    export default {
	   // 钩子函数，把之前data，methods，computed都写在这里面
       setup() {
            // 在组合式api中直接声明的变量就是一个普通的变量，不是响应式变量，修改这些属性，不会在视图中产生效果
            let msg = "哈哈"
            let count = 0
            // 需要引入import { reactive } from 'vue'来创建一个响应式的对象，reactive里面只能是对象{ }，不能reactive(1)
            const stu = reactive({
                name: "孙悟空",
                age: 18,
                gender: "男"
            })
            function changeAge() {
                stu.age = 33	// 这里就不用像之前要写this.stu.age了
            }
            // 在setup中可以通过 return 来指定哪些内容要暴露给外部
            return {
                msg,
                count,
                stu,
                changeAge
            }
        }
    }
</script>
<template>
    <h1>{{ msg }}</h1>
    <button @click="changeAge">点我</button>
    <h2>{{ stu.name }}</h2>
</template>
```

```js
// 新式写法：组合式API<script setup>，不用写export default，也不用写return暴露出去，模板就可以直接用
<script setup>
    import { reactive } from 'vue'
    const msg = "哈哈哈"
    const stu = reactive({
        name: "孙悟空",
        age: 18,
        gender: "男"
    })
    function fn(){
        alert("呵呵呵")
    }
</script>
<template>
    <h1>{{ msg }}</h1>
    <button @click="fn">点我</button>
    <h1>{{ stu.name }}</h1>
</template>
```

### ref(  )

- 因为 reactive() 里面只能传对象{ }，不能处理原始值（比如1，2，3）
- ref在生成响应式代理时，是将值包装为一个对象

```js
<script setup>
    import { reactive, ref, computed } from 'vue'
    // const stu = reactive({
    //         name: "孙悟空"
    // })
    const count = ref(1)	// 生成一个1的响应式代理 --> { value: 1 }，要访问 count.value
    const person = ref({name: "猪八戒", age: "18"})	// {value: {name: "猪八戒", age: "18"}}	person.value.name

    <h1>{{ count }}</h1>	// 在模板中访问不用.value，会自动解包自己加上.value，但要求ref对象必须是顶层对象

    // 如果在<script>标签中ref()也想要自动解包不写.value，可以在ref前面加上$ $ref(1)，然后后在vite配置文件vite.config中的	      plugins: [vue()]里面设置：reactivityTransform: true
    export default defineConfig({
      plugins: [vue(
        reactivityTransform: true
      )],
      resolve: {
        alias: {
          '@': fileURLToPath(new URL('./src', import.meta.url))
        }
      }
    })

	// ref另一种写法
	const obj2 = {
        name: ref("孙悟空"),	// {value: "孙悟空"} --> obj2.name.value
        age: ref(18)	// {value: 18} --> obj2.age.value
    }

    // 上面是写的data和methods，在setup组合式api中要用computed计算属性需要先引入computed
    const newMsg = computed(() => {
        return msg.value + "呵呵呵eee"
    })
</script>
```





### 模板语法

- 在模板中，可以直接访问到组件中声明的变量    `<h1>{{ msg }}<h1>`

- 部分全局对象也可以用：Date、Math、RegExp...`<h1>{{ new Date( ) }}<h1>`

- 也可以通过app对象来向vue中添加一些全局变量：app.config.globalProperties

```js
// main.js：
	import { createApp } from "vue"
	import App from "./App"
	const app = createApp(App)
    app.config.globalProperties.hell0 = "你好"
	app.mount("#app")
// App.vue：
	<h1>{{ hello }}<h1>
```

### 插值语法

- 插值语法{{  }}中只能使用表达式（有返回值的语句）

- 插值就是在修改元素的textContent，如果内容中有标签，标签会被转义显示，不会作为标签生效（避免xss攻击）

  - 如果不要这种情况：

    - 指令：用来设置标签如果显示内容，使用 v- 开头

      - v-text 将表达式的值作为元素的textComtent插入，跟{{ }}一样

      - v-html 将表达式的值作为元素的innerHTML插入（有xss攻击的风险）

        ```js
        const html = "<h1>呵呵呵</h1>"
        <div>{{ html }}<div>		-->  	页面：<h1>呵呵呵</h1>
        <div v-text="html"></div>		-->		页面：<h1>呵呵呵</h1>
        <div v-html="html"></div>		-->		页面：呵呵呵
        ```

      - v-bind 动态为标签设置属性，可以简写为冒号：

        ```js
        <script>
            import { ref } from 'vue'
            const imgPath = ref("/imgs/messi.png")
            const attrs = {
                id: "box1",
                class: "hello"
            }
            function changeImg() {
                imgPath.value = "/imgages/neimar.png"
            }
        	const attrName = "title"
            const attrValue = "这是一个title"
        	const isDisabled = false
        </script>
        <template>
            <button @click="changeImg">切换图片</button>
            <img :[attrName]="attrValue" v-bind:src="imgPath">		// 简写：<img :src="imgPath">
                // :[attrName]="attrValue"	可以动态的设置各种属性
            <div :="attrs"></div>		-->		<div id="box1" class="hello"></div>	// 设置多个标签属性
            <input :disabled="isDisabled" :style="{}" :class=""/>
        </template>
        ```





### style-scoped

- 可以直接通过style标签来编写样式，在style标签写的样式是全局样式，也会影响到所有组件同类名的样式

  ```js
  <script setup>
      import Mybox from './components/Mybox.vue'
  </script>
  <template>
  	<Mybox></Mybox>		// 引入子组件直接写标签会自动引入上面的import
  </template>
  <style scoped>
          
  </style>
  ```

- `<style scoped>` 这样样式将成为局部样式，只对当前组件生效（只适用于单根组件）

  - 使用scoped时，vue会自动为组件中的所有元素生成一个随机的属性（比如：data-v-7a7a37b1）

  - 生成后，所有的选择器都会在最后添加一个[data-v-7a7a37b1]

    .box1		-->		.box1[data-v-7a7a37b1]		属性选择器，在.box1中找data-v-7a7a37b1的标签

  - 随机生成的属性除了会添加到当前组件内的所有元素，还会添加到当前组件引入的子组件的根元素（最大的div）上，这样设计是为了可以通过父组件来为子组件设置一些样式

    ```js
    // 单根组件：子组件的template标签里只有一个最大的标签div
    <template>
        <div>
        	<h1>hello</h1>
    		<div>我是box1</div>
        </div>
    </template>
    // 多根组件：
    <template>
        <h1>hello</h1>
    	<div>我是box1</div>
    </template>
    
    
    <style scoped>
        // 深度选择器 :deep()
    		// 比如父组件有h2，子组件也有h2，在父组件设置h2不会影响子组件的h2，但是又想在父组件设置
    	.app :deep(h2){		// 表示所有app下的h2都设置
        	color: yellow
    	}
    
    	// 全局选择器 :global()，跟下面的一样，用这个
    	:global(div) {
            border: 1px solid red
        }
    </style>
    // 可以写多个style，这个不写scoped，表示全局的样式
    <style>
        div {
            border: 1px solid red		// 表示全局所有的div都加上边框
        }
    </style>
    ```

### style-module

- 自动对模块中的类名进行hash哈希化来确保类名的唯一性
- 通过 $style.类名 来使用

```js
<div :class:"$style.box1">哈哈哈</div>

<style module>
   .box1 {		// 标签会显示：<div class="_box1_bi5jk_2">
       background-color: #bfa
   }
</style>


// 类和内联样式
const arr = ["box1", "box2", "box3"]
const arr2 = [{box1: true}, {box2: true}, {box3: false}]	// 传布尔值true表示有这个属性，false表示没有
const style = {
    color: "red",
    backgroundColor: "#bfa"
}
<div :class="arr"></div>	--->	<div class="box1 box2 box3"></div>
<div :class="arr2"></div>	--->	<div class="box1 box2"></div>
<div :style="style"></div>
```





## props

- 父组件给子组件传递数据

- 子组件的数据通常不会在子组件直接定义，这样会导致数据和视图发生耦合，子组件的数据通常会在创建组件实例时确定（写在父组件）

  ```js
  const player = reactive({
    name: "梅西",
    img: "/images/messi.png",
    rate: 1,
    hot: 433760
  })
  ```

- 父组件可以通过 props 来将数据传递给子组件

  1. 先在子组件中定义props

     ```js
     const props = defineProps(["item", "maxLength"])		// 驼峰命名
     // const item = props.item
     ```

  2. 通过父组件的子组件实例标签传递数据

     ```js
     <TabItem :item="player"  maxLength="10" max-length="10"></TabItem>		// 也可以这么传：maxLength="10"
     ```

  3. 使用数据

     ```html
     <img :src="item.img" :alt="item.name" />		
     <span>{{ item.rate }}</span>
     <h1>{{ props.maxLength }}</h1>
     ```

- 父组件传给子组件的props在子组件是只读的，不能在子组件中进行修改的操作，reactive或者ref的数据要确保只有一个地方能改，就是创建他们的地方的组件（单向数据流）。最好不要通过子组件去修改父组件的数据，如果真要改，通过自定义事件

- 可以对props进行配置：

  ```js
  const props = defineProps({		// 上面的是传数组（简单配置），这个传对象（完整配置）
      count: Numver,	// 对传过来的数据进行类型限制，还是可以接收到数据，只是在控制台会有错误提示，开发时可以用
      obj: Object,
      maxLength: {	// 也可以对传过来的数据进行配置
          type: String,	// 跟上面的限制类型一样
          required: true,	// 默认为false，true表示必须得传这个值才行
          default: "哈哈",	// 如果不传数据过来，则默认值设置为
          validator(value){	// 检查传过来的数据是否合法，value是传过来的值
              return value !== "嘻嘻"	// 可以制定正则
          }
      }
  })
  ```

  





## v-show

- 可以根据表达式的值来决定元素是否显示（通过display，东西还在，只是隐藏，不会涉及到组件的重新渲染）。切换性能好，初始化性能差，初始化时会将所有的进行初始化，即使暂时不显示


```html
const isShow = ref(true)

<div v-show="isShow"></div>
<button @click="isShow = !isShow">切换</button>
```

## v-if

- 跟v-show一样，但是会将元素直接删除，会重新渲染。切换性能差，初始化性能好，用的时候才会初始化。可以配合`<template>`标签使用，可以和 `v-else` 使用

```html
<div v-if="isShow">
    <h1>我是v-if</h1>
</div>
<div v-else>
    <h1>我是v-if中的else</h1>
</div>
<div v-else-if>
    <h1>我是v-if中的else-if</h1>
</div>

<template v-if='isShow'>	// 在网页中不显示template标签，只显示h1和h2标签
	<h1>我是h1</h1>
    <h2>我是h2</h2>
</template>
<button @click="isShow = !isShow">切换</button>
```

## component(动态组件标签)

- 也可以实现切换效果

- component最终以什么标签呈现由 is 属性决定

  ```html
  <template>
  	<component is='div'>我是component</component>		-->		<div>我是component</div>
  </template>
  ```

- component的is里面也可以直接写组件

  ```html
  <script>
  	import { ref } from 'vue'
      import A from './components/A.app'
      import B from './components/B.app'
      const isShow = ref(true)
  </script>
  <template>
      <button @click="isShow = !isShow">切换</button>
  	<component :is='isShow? A : B'></component>		也可以实现切换效果
  </template>
  ```

## 动态绑定css

```js
<script setup>
    import { computed } from 'vue';
    const props = defineProps(["src", "alt", "rate"])
    const color = computed(() => {
        if (props.rate == 1) {
            return "rgb(254, 45, 70)"
        } else if (props.rate == 2) {
            return "rgb(245, 102, 1)"
        } else {
            return "rgb(247, 169, 1)"
        }
    })
</script>

background-color: v-bind(color);
```

## v-for

- 使用v-for时，比如在li前面添加unshift一个li，如果里面有input框，input框里的内容会发生顺序错乱，因为v-for添加元素是根据从第一个到最后一个进行对比，有不同才添加上去，原来一样的dom就不会动，不会渲染，但是用unshift在前面添加则会错乱
  - 可以为元素指定一个唯一的key，元素在比较时就会按照相同的key去比较而不是顺序（相同的key对比，而不是按顺序从上往下比）


```html
<script setup>
    import { ref } from 'vue';
    const arr = ref(["孙悟空","猪八戒","沙和尚","唐僧",])
</script>

<template>
    <ul>
    	<li v-for="name in arr">{{ name }}</li>
		<li v-for="(name, index) in arr">{{ index }} - {{ name }}</li>
		<li v-for="{ id, name, gender } in arr">{{ name }} - {{ age }} - {{ gender }}</li>	// 解构对象赋值
         <li v-for="{ id, name, gender } in arr" :key="id">{{ name }} - {{ age }} - {{ gender }}</li>
    </ul>
</template>
```





## 插槽（slot）

- 用props传数据只能一层一层传，如果数据太多传着很麻烦

```html
// App.vue：
<script setup>
    import MyButton from "./components/MyButton.vue"
    import A from "./components/A.vue"
    const name = "孙悟空"
</script>

<template>
	<MyButton> 这是App自定义的文字 </MyButton>		// 组件插槽入口
	<MyButton> <A :name="name"></A> </MyButton>
</template>

// MyButton.vue:
<template>
	<button>
        <slot></slot>		// 出口
    </button>
</template>
```

### 具名插槽（简写#）

- 传给指定的插槽

```html
// App.vue：
<script setup>
    import MyWrapper from "./components/MyWrapper.vue"
</script>

<template>
	<MyWrapper>
    	<template v-slot:h1>一级标题</template>		// 在组件MyWrapper里必须先写template标签
        <template #h2>二级标题</template>   	// #简写
    </MyWrapper>
</template>

// MyWrapper.vue:
<template>
	<div>
    	<h1>
    		<slot name="h1"></slot>
             <slot>插槽的默认内容</slot>		// 不指定name的是默认插槽
    	</h1>
		<h2>
    		<slot name="h2"></slot>
    	</h2>
    </div>
</template>
```

- 兄弟间传数据（子传父，父传另一个子）

  ```html
  // App.vue：
      <script setup>
          import A from "./components/A.vue"
          import SlotDemo from "./components/SlotDemo.vue"
      </script>
  
      <template>
          <SlotDemo>
              <template #s2="slotProps">	// 在#s2后面等号取个收取来自SlotDemo.vue中<slot>传过来的数据的名字，										  或者直接解构#s2="{age, gender}"
                  <A :name='slotProps.stuName'></A>	// 通过slotProps.stuName传给A组件，通过s2传过来的只能													 在s2访问到数据
              </template>   	
          </SlotDemo>
      </template>
  
  // SlotDemo.vue：
      <script setup>
          // 要把这些数据传给A.vue，先通过<slot>传给父组件App.vue，再由App.vue的A组件标签传给A.vue
          const stu = {
              name: "孙悟空",
              age: 18,
              gender: "男"
          }
          const stuName = "孙悟空"
          const age = 18
          const gender = "男"
      </script>
  
  	<template>
          <div>
              // 这个插槽就是App.vue传过来的A.vue的组件，把数据写在这个插槽里传给App.vue的s2的插槽入口
              <slot name="s2" :gender="gender" :age="age" :stuName="stuName" :stu="stu"></slot>
          </div>
  	</template>
  
  // A.vue
      <script setup>
      	const props = defineProps(["name"])
      </script>
  
      <template>
          <h1>A组件 {{ props.name }}</h1>
      </template>
  ```

### 动态具名插槽

```html
<template #[dynamicSlotName]></template>	// 通过[]来放变量插槽名
```



## 事件（v-on简写@）

- 简写：`@`

- 绑定事件的两种方式：

  1. 内联事件处理器（自己调用函数）事件触发时，直接执行js语句，数由我们自己传递

     ```html
     // 自己调用要加()，可以自己传值，没有event，如果需要用到，可以传$event
     <button @click="clickHandler2($event, 1, 2, 'hello')">内联事件处理器</button>
     ```

  2. 方法事件处理器（vue帮我们调用函数）事件触发时，vue会对事件的函数进行调用，参数由vue帮我们传

     ```html
     // 不加()让vue帮我们调用，有event
     <button @click="clickHandler">方法事件处理器</button>
     ```

- vue怎么区分两种处理器：

  - 检查事件的值是否是合法的js标识符(变量名)或属性访问路径，如果是则表示是方法事件处理器，否则表示内联事件处理器

    foo	-->	方法

    foo.bar	-->	方法

    foo++	-->	内联

    foo()	-->	内联
    
    

## 事件修饰符

### .stop

- 阻止冒泡
- 原生js：stopPropagation()

### .capture

- 在捕获阶段触发事件

### .prevent

- 取消默认行为
- 比如点击超链接跳转、点击sumit提交表单
- 原生js：preventDefault()

### .self

- 只有事件由自身触发时才会有效

### .once

- 绑定一个一次性的事件

### .passive

- 用于提升滚动事件的性能

### 按键修饰符

- 比如：
  - .enter
  - .space
  - .tab
  - .down
  - .esc

```html
<script setup>
import { ref } from "vue"
const count = ref(0)

function clickHandler(event) {
    console.log(event)
}

function clickHandler2() {
    
}
    
// 原生js解决事件冒泡：
    function boxHandler(event, text) {
    // 可以通过事件对象来取消事件的传播
        event.stopPropagation()
        alert(text)
    }
    
// vue解决事件冒泡：在事件后面加.stop
</script>

<template>
    <h1>{{ count }}</h1>
    <button @click="count++">点我一下</button>
    <button @click="clickHandler">方法事件处理器</button>
    <button @click="clickHandler2($event, 1, 2, 'hello')">内联事件处理器</button>
    
    // 事件的冒泡，原生js传 $event
      <div class="box1" @click="boxHandler($event, 'box1')">box1
        <div class="box2" @click="boxHandler($event, 'box2')">box2
            <div class="box3" @click="boxHandler($event, 'box3')">box3</div>
        </div>
      </div>

    // vue冒泡，不用传event，在事件后面加.stop
    <div class="box1" @click="boxHandler2('box1')">
        box1
        <div class="box2" @click.stop="boxHandler2('box2')">
            box2
            <div class="box3" @click.stop="boxHandler2('box3')">box3</div>
        </div>
    </div>
    
    
    // 按键修饰符
    <input @keyup.enter="sumit">
    <input @keyup.page-down="onPageDown">
</template>
```



## 属性的透传

- 在组件标签上设置属性，会自动传递给子组件的根元素成为子组件的属性，如果子组件也是组件标签，则会一层一层往下传，这样就可以方便在父组件给子组件设置属性

- 透传会发生在没有被声明为props和emit的属性上（如果有被声明props的属性，则这个属性不会发生透传）

  ```js
  const props = defineProps(["name"])		// name这个属性不会发生透传
  ```

- 自动的透传只适用单根组件（子组件的template只有一个最大的标签）

  - 如果是多根组件，透传并不知道要传给谁，则需要手动指定添加到哪个元素上
    1. 在模板 template 标签中，可以通过$attrs来访问透传过来的属性
    2. 在脚本 script 标签中，可以通过useAttrs()来获取透传过来的属性（先引入再使用）

- 如果是单根组件，会全部发生自动透传，但只想指定部分属性透传。首先先禁止自动透传，然后再手动通过 $attrs 指定部分属性透传

  - 禁止自动透传：在子组件中向外导出一个配置对象

    ```js
    <script>
        export default {
            inheritAttrs: false
        }
    </script>
    ```

```js
// App.vue：
import C from "./components/C.vue"
<script setup>
    function showMsg() {
        alert("hello")
    }
</script>

<template>
    <C class="box2" style="color: red" @click="showMsg"></C>	// 透传过去的class不会覆盖子组件原有的class，会整合
</template>
<style scoped></style>


// C.vue
<script setup>
    // 引入useAttrs
    import { useAttrs } from "vue"

    // 在脚本script中，可以通过useAttrs()来获取透传过来的属性
    const attrs = useAttrs()
    console.log(attrs)
</script>

<script>
    export default {
        inheritAttrs: false
    }
</script>

<template>
    <h2 class="box3" style="font-size: 60px">我是组件C</h2>

	// 在模板template中，可以通过$attrs来访问透传过来的属性，多根组件中可以手动指定透传过来的属性要添加到哪些元素
	{{ $attrs }}

	// 使用：通过 $attrs.属性
	<h3 :class="$attrs.class" :style="$attrs.style">我也是组件C</h3>

	// 也可以这样：表示把透传过来的所有属性作为h3的属性
	<h3 :="$attrs">我是h3</h3>
</template>
```





## 网页的渲染

- 浏览器在渲染页面时，做了那些事：

  1. 加载页面的html和css（源码）
  2. html转换为DOM，css转换为CSSOM
  3. 将DOM和CSSOM构建成一课渲染树
  4. 对渲染树进行reflow（回流、重排）（计算元素的位置）
  5. 对网页进行绘制repaint（重绘）

- 渲染树（Render Tree）

  - 从根元素开始检查那些元素可见，以及他们的样式
  - 忽略那些不可见的元素（display:none 在网页中直接隐藏并删除；而 visibility: hidden 只是隐藏，还占着位置，还会重排）

- 重排、回流

  - 计算渲染树中元素的大小和位置
  - 当页面中的元素的大小或位置发生变化时，便会触发页面的重排（回流）
  - width、height、margin、font-size ......
  - 注意：每次修改这类样式都会触发一次重排！所以如果分词修改多个样式会触发重排多次，而重排是非常耗费系统资源的操作（昂贵），重排次数过多后，会导致网页的显示性能变差，在开发时我们应该尽量的减少重排的次数
  - 在现代的前端框架中，这些东西都已经被框架优化过了！所以使用vue、react这些框架这些框架开发时，几乎不需要考虑这些问题，唯独需要注意的时，尽量减少在框架中直接操作DOM

- 重绘

  - 绘制页面
  - 当页面发生变化时，浏览器就会对页面进行重新的绘制

```js
// 多次重排，耗费性能
  	box1.style.width = "300px"
    box1.style.height = "400px"
    box1.style.fontSize = "20px"
// 可以通过修改class来间接的影响样式，来减少重排的次数
    box1.classList.add("box3")
// 也可以通过以下方式减少重排
    box1.style.display = "none"
    box1.style.width = "300px"
    box1.style.height = "400px"
    box1.style.fontSize = "20px"
    div.style.display = "block"
```

