## 安装 
`yarn add -D vite`
- 相较于webpack，vite采用了不同的运行方式：
  - 开发时不对代码打包，而是直接采用ESM的方式来运行项目

  - 在项目部署时，再对项目进行打包

1. 在index.html中引入：

  ```js
  <script type="module" src="./src/index.js"></script>		必须加 type="module"
  ```

2. 启动： `yarn vite`		-->      yarn dev

3. 打包： `yarn vite build`        -->      yarn build

4. 预览： `yarn vite preview`     -->      yarn preview

   (打包完从dist文件夹里的index.html是打不开的，必须在服务器才能打开，要么就启动预览) 

5. 设置快捷键：

  ```json
  "scripts": {
      "dev": "vite",
      "build": "vite build",
      "preview": "vite preview"
    }
  ```



## 使用命令自动构建项目
- 创建完进入目录路径先安装依赖输入：yarn
- 创建vite：`yarn create vite`

- 不用像webpack一样下载配置loaders和快捷键，可以直接在index.js中引css和其他

  ```js
  import "./styles/index.css"
  ```

  ```js
  <script type="module" src="./src/index.js"></script>		必须加 type="module"
  ```



  - 插件：（跟webpack一样在目录下创建 vite.config.js 配置文件，里面暴露是用es模块化，webpack是commonjs模块化）

    - 兼容旧代码的插件，webpack是用babel

    - vite用 legacy： `yarn add -D @vitejs/plugin-legacy` 和 `yarn add -D terser`

    - 引入和配置：

      ```js
      // 引入配置文件编写代码快捷提示，不用这个也行
      import { defineConfig } from "vite"
      // 引入兼容旧浏览器插件
      import legacy from "@vitejs/plugin-legacy"
      
      export default defineConfig({
          plugins: [
              legacy({
                  targets: ["defaults", "ie 11"]
              })
          ]
      })
      ```

    - `yarn build` 打包完后dist的assets文件夹里会出现 index-legacy-a1a5c480.js 和 index-38a9d57a.js