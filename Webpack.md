## 安装

- 使用步骤：

  1. 初始化项目：`yarn init -y`

  2. 安装依赖：`yarn add -D webpack webpack-cli`

  3. 在项目中创建 src 目录文件夹，创建并编写代码（index.js）

  4. 执行 `npx webpack` 来对代码进行打包（打包后会在目录下生成一个dist文件夹）
  ```js
  // 配置快捷键package.json
  "scripts": {
      "build": "webpack",     // 打包：yarn build
      "dev": "webpack server --open"      // 通过内置服务器打开index.html：yarn dev
  },
  ```
  
     *打包的时候会自动去找src里的index.js（入口文件，其他的js文件都引入这里），然后打包*
  
- 配置文件（创建 webpack.config.js 文件）

  ```js
  const path = require("path")
  module.exports = {
      mode: "production", 	// 设置打包的模式，production生产模式，development开发模式
      entry: "./src/index.js",	// 配置打包的入口文件，可以改自己定义的比如hello.js，默认的，不用配置
      output: {	
      	path: path.resolve(__dirname, "hello"),		// 指定打包的目录文件夹名 dist，不用配置
      	filename: "main.js",	// 配置代码打包后的文件名，不用配置
      	clean: true,		// 每次打包是否清空dist目录
      }, 
  }
  ```
  
  
  
  
  
  推迟执行脚本defer：页面解析完才会执行脚本
  
  ```js
  <script defer src="./main.js"></script>
  ```



## Loaders（插件）

​		*webpack默认只能处理js文件，css文件又不能放在dist里（Loaders也可以处理其他的文件格式）*

​		*css文件用 style 文件夹命名，图片文件夹用 assets 文件夹命名*

1. 安装： `yarn add -D style-loader`、`yarn add css-loader -D`

2. 在 index.js 文件中引入css和img

   ```js
   import "./style/index.css"
   import dog from "./assets/dog.jpg"
   	document.body.insertAdjacentHTML("beforeend", `<img src="${dog}"/>`)
   ```

3. 配置：（ 在目录下创建webpack.config.js文件 ）

   ```javascript
   module.exports = {
       module: {
           rules: [
               {
                   test: /\.css$/i,    // 匹配以.css结尾的文件
                   use: ['style-loader', 'css-loader']     // 从后往前依次执行
                       // 必须style-loader写在css-loader前面，css-loader先执行，再执行style-loader
                       // style-loader 负责让样式生效，css-loader负责让css代码转为js代码从而引入index.js中
               },
               {
                   // 图片自带就有，不用下载loaders，直接配置type就行
                   test: /\.(jpg|png|gif)$/i,
                   type: "asset/resource"
               }
           ],
       },
   };
   ```

   

## plugin（插件）

- 用来为webpack扩展功能

- **html-webpack-plugin** 这个插件可以在打包代码后，自动在打包目录dist生成html页面

  - 使用步骤：

    1. 安装依赖： `yarn add html-webpack-plugin`

    2. 引入，在webpack.config.js文件引入

       ```js
       const HTMLPlugin = require("html-webpack-plugin")
       ```

    3. 配置插件

       ```js
       module.exports = {
           plugins: [
               new HTMLPlugin({
                   // title: "Hello Webpack",
                   template: "./src/index.html"	// 以这个html文件为模板创建一样的
               })
           ]
       }
       ```


- 开发服务器，临时服务器上编译代码（webpack-dev-server）

  - 安装：

    - `yarn add -D webpack-dev-server`
  - 启动：
    - `yarn webpack serve --open`

- 配置源码的映射

  ```js
  module.exports = {
  	devtool:"inline-source-map"
  }
  ```

  
