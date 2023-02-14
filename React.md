## 1.通过script标签使用React

- 下载：

  - `https://unpkg.com/react@18.0.0/umd/react.development.js`

  - `https://unpkg.com/react-dom@18.0.0/umd/react-dom.development.js`

- 引入：

  ```html
  <head>
      <meta charset="UTF-8">
      <title>Hello World</title>
      <!--引入react的核心库-->
      <script src="script/react.development.js"></script>
      <!--引入react的DOM库-->
      <script src="script/react-dom.development.js"></script>
  </head>
  <body>
  	<div id="root"></div>
      <script>
          // 原生js通过DOM向页面中添加一个div：
              // 创建一个div
                  const div = document.createElement('div'); // DOM元素
              // 向div中设置内容
                  div.innerText = '我是一个div';
              // 获取root
                  const root = document.getElementById('root');
              // 将div添加到页面中
                  root.appendChild(div);
  
  
          // React向页面中添加一个div：
              /*
                 一、React.createElement()	创建一个React元素
                     - 参数：
                         1. 元素名、组件名（如果是元素必须全是小写，大写的是组件名）
                         2. 元素中的属性
                              - id，class用className
                              - 如果是绑定事件必须得是驼峰命名，传入的必须是函数：onClick: () => { alert(123) } 
                         3. 元素的子元素（内容）
                 二、ReactDOM.createRoot()	创建React根元素
                 三、root.render()		    渲染
              */
              // 1、创建一个React元素，这里用的react核心库
                 const div = React.createElement('div', {
                      id: "hello",
                      className: "hello",
                      type: "button",
                      onClick: () => { alert(123) }
                  }, '我是React创建的div', button); // React元素
  
              // 获取根元素（容器root）对应的React元素
              // 2、创建React根元素，需要一个DOM元素作为参数，这里有使用dom，把dom元素转为react元素，用的react的dom库
                  const root = ReactDOM.createRoot(document.getElementById('root'));
  
              // 3、将div渲染添加到根元素中（render）
                  root.render(div);
      </script>
  </body>
  ```

- 注意：
  - dom元素不能与React元素一起混用（类似于原生js和jquery，要混用必须得转成一样的）
  - React元素一旦创建createElement就不能修改，只能通过新创建的元素然后渲染进行替换
    - 修改替换元素后，必须重新渲染（render），渲染时，React会自动比较两次渲染的元素（react的虚拟dom和js的真实dom），只在真实dom中更新发生变化的部分，先比较父元素，如果父元素不同，所有元素全部替换，如果父元素一样，则逐个对比子元素，按顺序从上到下对比（diffing算法）



## JSX（JS扩展）

- 声明式编程，结果导向的编程

- JSX就是 React.createElement() 的语法糖

- 通过JSX创建React元素，需要翻译（用babel把JSX的代码转换为React代码）

  1. 下载：`https://unpkg.com/babel-standalone@6/babel.min.js`

  2. 引入

     ```js
     <script src="script/babel.min.js"></script>
     ```

  3. 设置js代码被babel处理：<!--<script type="text/babel"></script>-->

     ```js
     <body>
         <div id="root"></div>
     
         <!--设置js代码被babel处理-->
         <script type="text/babel">
         	// 在这里面就可以直接写html代码即可，不需要再React.createElement()
             // 标签小写开头，组件大写。有且只能有一个根标签，最大的div包裹里面
             // 属性可以直接在标签中设置
     		const div = <div className="box" onClick={()=>{alert("haha")}} style={{backgroundColor:'red'}}>	
                 我是一个div
                 <button>我是按钮</button>
     
                 const name = '孙悟空'
                 <div>
                     {name}	// 使用{}获取上面的变量name
                 </div>
             </div>;
     
             const root = ReactDOM.createRoot(document.getElementById('root'));
     
             root.render(div);
         </script>
     </body>
     ```

     - 注意：
       - 标签小写开头，组件大写
       - 有且只能有一个根标签，最大的div包裹里面
       - 标签必须正确结束（自结束标签必须写/）
       - 可以使用 { } 嵌入表达式（有值的语句）
         - 如果表达式是空值、布尔值、undefined，将不会显示
       - 属性可以直接在标签中设置
         - class需要使用className代替
         - 事件用驼峰命名法
         - style中必须使用对象 {  } 设置
           - style={{backgroundColor:'red'}}

### 渲染

  ```js
  <head>
      <meta charset="UTF-8">
      <title>渲染列表</title>
      <script src="script/react.development.js"></script>
      <script src="script/react-dom.development.js"></script>
      <script src="script/babel.min.js"></script>
  </head>
  <body>
      <div id="root"></div>
  
      <script type="text/babel">
          const name = '孙悟空';
          const lang = 'cn';
          // {} 只能用来放js表达式，而不能放语句（if for）
          // 在语句中可以去操作JSX
          let div;
          if (lang === 'en') {
              div = <div>hello {name}</div>;
          } else if (lang === 'cn') {
              div = <div>你好 {name}</div>;
          }
  
  
          const data = ['孙悟空', '猪八戒', '沙和尚'];
          // 将数组渲染为一个列表在网页中显示，jsx中会自动将数组中的元素遍历在页面中显示
          // const list = <ul>{ data }</ul>;    -->    <ul>孙悟空猪八戒沙和尚</ul>
  
          // 要将数组渲染成一个表格：
          /* 
              要把数组变成这样：[<li>孙悟空</li>, <li>猪八戒</li>, <li>沙和尚</li>]
              方法1、 
                  创建一个空数组
                      const arr = [];
                  遍历data：
                      for(let i=0; i<data.length; i++){
                          arr.push(<li>{data[i]}</li>);
                      }
                  const list = <ul>{arr}</ul>
          */
  
          // 方法2、
          const list = <ul>{data.map(item => <li>{item}</li>)}</ul>;      // item是参数，这里li里面的item一定要加{}
  
  
          const root = ReactDOM.createRoot(document.getElementById('root'));
          root.render(list);
      </script>
  </body>
  ```

### key
  
  - 渲染对比是从上往下进行的，如果在前面插入新的元素，但此时对比的位置发生的改变，修改的元素发生了错位
  - 解决办法：给一个key属性（跟vue一样）
  - 注意：
    1. 开发中一般会采用数据的id作为key（必须唯一的）
    2. 尽量不要使用元素的index作为key（除非顺序不发生变化才可以使用）
  
  ```js
  // 可以在jsx中直接写document.getElementById('btn').onclick = function (){}
  document.getElementById('btn').onclick = function (){
      const data = ['唐僧', '孙悟空', '猪八戒', '沙和尚'];
      // 创建列表
      const list = <ul>
            // {data.map(item => <li>{item}</li>)}
  
            // 添加key属性
            {data.map(item => <li key={id}>{item}</li>)}
            {data.map((item, index) => <li key={index}>{item}</li>)}
      </ul>;
      // 渲染元素
      root.render(list);
  }
  ```
  
  



## 2.手动创建React项目

1. 目录下创建public和src文件夹

   1. public下创建index.html文件

      - index.html是首页的模板，webpack在编译打包文件时，会以index.html为模板在build文件夹下生成index.html

      ```html
      <!DOCTYPE html>
      <html lang="en">
      <head>
          <meta charset="UTF-8">
          <meta http-equiv="X-UA-Compatible" content="IE=edge">
          <meta name="viewport" content="width=device-width, initial-scale=1.0">
          <title>Document</title>
      </head>
      <body>
          <div id="root"></div>	// 创建根
      </body>
      </html>
      ```

   2. src下创建index.js文件

      ```js
      // 引入ReactDOM
      	import ReactDOM from 'react-dom/client';	// 引入文件后面要加/client
      
      // 创建一个JSX
          const App = <div>
              <h1>这是一个React项目</h1>
              <p>我终于有了第一个React项目了!!!</p>
          </div>;
      
      // 获取一个根容器
      	const root = ReactDOM.createRoot(document.getElementById('root'));
      
      // 将App渲染进根容器
      	root.render(App);
      ```

2. 初始化项目并安装依赖

   `yarn init -y`

   `yarn add react react-dom react-scripts`

3. package.json文件设置快捷键

   ```js
   "scripts": {
       "start": "react-scripts start",
       "build": "react-scripts build"
     },
   ```

4. 打开内置服务器预览页面

   `yarn start`

5. 构建打包

   `yarn build`

6. 把build文件夹下的index.html中的路径改为相对路径

   ```js
   src="./static/js/main.593b4e52.js"
   ```

   



