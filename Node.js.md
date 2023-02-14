## Promise

```js
function sum(a, b){
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(a + b)
        },1000)
    })
}
sum(123, 456).then((result) => {
    console.log(result);
},(reason) => {
    console.log("出错了");
})
sum(123, 456)
    .then(result => result + 7) //上一个result的结果加上一个数
    .then(result => result + 8)
    .then(result => console.log(result))    // 123+456+7+8
```

- promise隐藏属性：

  1. PromiseResult：存储的数据
  2. PromiseState：记录Promise的状态（只能修改一次，修改后不会再变）
     1. pending：进行中（默认状态）
     2. fulfilled   完成（通过resolve存储数据时）
     3. rejected   拒绝（出错了或者通过reject存储数据时）

  - 当状态是fulfilled的时候then的result会执行resolve

  - 当状态是rejected的时候then的reason的会执行reject

    ```js
    promise.catch(reason => {
        // catch 专门处理拒绝（只解决前面then的错误，如果自己有错误则不解决，会throw error交给后面的catch解决）
        console.log("出错了");
    })
    
    promise.finally(()=>{})    // 无论是成功或失败都会执行这个函数，但是接收不到参数
    ```

  - promise里如果不返回return东西再接着then拿到的result就是undefined，如果有return就是下一次then的result

    ```js
    promise
        .then(result => {
        console.log(result);
        return "123"
    })
        .then(result => {
        console.log(result);    // 123
    })
    // 如果上一步的执行结果不是当前这步想要的结果，则会跳过这步
    ```

```js
Promise.resolve(10).then(result => console.log(result))     // 简便写法
Promise.reject("出错了").catch(reason => console.log(reason))
    // 相当于：
        new Promise((resolve, reject) => {
            resolve(10)
        })
```

```js
Promise.all([sum(123, 456), sum(5, 6), sum(33, 44)]).then(result => console.log(result))    // 可以返回多个promise对象的结果（结果自己也是promise,就可以then），如果里面有一个错误reject的结果就都不返回结果了
Promise.allSettled()  // 可以返回多个promise对象的结果，无论成功或失败
Promise.race([sum(123, 456), sum(5, 6), sum(33, 44)])  // 返回里面执行的最快的那个的结果，不考虑成功或失败
Promise.any()   // 跟race差不多，返回里面执行的最快的那个的结果，只有成功的结果
```

```js
queueMicrotask(() => {})    // 向微任务队列添加一个任务
```

- 调用栈 => 微任务队列（promise） => 宏任务队列（定时器）
  - 如果promise中写一个立即执行的定时器，则是微任务里的宏任务，会比纯微任务执行的还慢 
  - 如果promise中再写一个promise,则会比纯微任务还慢(一个then属于一个微任务，执行完就执行别的微任务，里面的微任务就排在别的微任务的后面了)

### async和await

```js
async function fn(){
    return 10
}
fn2.then(result => console.log(result))
```

- 当通过await调用异步函数时，会暂停await下面函数的执行，下面的代码就是需要await的结果出来才写的代码，await不会影响（阻塞）async声明的函数外面的代码执行
- await只能写在async声明的异步函数里，或es模块的顶级作用域中(script标签中type="module"这样这个标签就变成顶级作用域了，或文件后缀为mjs，里面就可以写await了)

```js
function sum(a, b){
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(a + b)
        },1000)
    })
}
async function fn2(){
    try{
        let result = await sum(123, 456)
        result = await sum(result, 9)
        result = await sum(result, 10)
        console.log(result);
    }catch(e){
        console.log("出错了");
    }
}
fn2()
```

- 如果async声明的函数中没有写await，就相当于里面的promise的resolve()没有返回值，没有返回值就同样直接进入调用栈执行

  ```js
  async function fn4(){
      console.log(1);
      console.log(2);
      console.log(3);
  }
  // 相当于：
  function fn4(){
      return new Promise (resolve => {
          console.log(1);
          console.log(2);
          console.log(3);
          resolve()   // 没传值
      })
  }
  // await当前行代码和上面的代码属于调用栈，await下面的代码才属于异步的微任务队列，一个await就相当于把后面的代码放进一个then里
  ```

  

## ES6 模块

- 暴露

  ```js
  export let a = 1
  export let b = [1, 2, 3, 4]
  export let c = function fn() {
      console.log(111);
  }
  
  export default function sum(a, b) {     //设置默认暴露，一个模块只能有一个
      return a + b
  }
  // 默认暴露的import的名字可以随便取import hello from "./m4.mjs"
  // export default 后面暴露的必须是一个值，不能export default let a=10,
  // 可以这样：
  	let a = 10
  	export default a
  // 同时要默认暴露和其他暴露：
  import sum,{a,b,c} from "./m4.mjs"
  
  console.log(sum, a);
  ```

- 引入

  ```js
  import { a as hello, b as haha, c } from "./练习02.mjs";    // 解构赋值拿数据
  	// 从./练习02.mjs中引入a，然后改名hello复制给hello 
  import * as m2 from "./练习02.mjs";
  	// 变量a,b,c不能改名，mjs里是什么这里就写什么
  console.log(hello);
  console.log(m2.a);
  
  // 导入的a是常量，不能更改，对象c.name可以改
  // es模块化都是严格模式
  ```

  - 如果想在nodeJS中使用ES6模块：
    1. 把文件的后缀改成.mjs就可以使用es6模块
    2. 修改package.json将模块化规范设置位ES模块（"type":"module"）



- node 模块   common Js模块  node默认为commonJS模块

- 先找js文件，没有就找json，没有就找node（特殊）

- 所有的common Js模块都会包装到一个函数中

  - 暴露 （m1.js里写）一个一个暴露

    ```js
    exports.a = "123"
    exports.b = { name: "张三" }
    exports.c = function fn(){
        console.log(111);
    }
    ```

  - 通过module.exports同时暴露多个值，必须写module

    ```js
    module.exports = {
        a: "哈哈",
        b: [1, 2, 3],
        c: () => {
            console.log(111);
        }
    }
    ```

  - 引入

    ```js
    const m1 = require("./m1.js")   // 引入本地要写路径
    console.log(m1.a);
    ```

    

```js
const path = require("node:path")   
// 引入核心模块（第三方模块），直接写核心模块的名字即可path是node自带的模块   
	const path = require("path") 
```

- node 的全局 globalThis 相当于window



## process （核心模块）

- 表示node的进程，是全局变量，可以直接使用

- 方法：

  ```js
  process.exit()    //结束进程，终止node
      // console.log(111);
      // process.exit()   这下面的就不执行了
      // console.log(222);
      // console.log(333);
  process.nextTick(callback([,...args]))
      process.nextTick(()=>{
          console.log(1);
      }) 
      // 将callback函数插入到tick队列中，比微任务还快执行
  
      // 调用栈 => tick队列 => 微任务队列 => 宏任务队列
  ```

  

## path（路径）

- 可以用来获取各种路径，要使用先引入const path = require("node:path")

- 方法：

  ```js
  path.resolve([...paths])     // 用来生成一个工作目录的绝对路径（注意：在这里调用和在调试控制台F5调用输出的路径不一样,因为工作目录不一样）
      // 如果将一个相对路径作为参数，则resolve会自动将其转换位绝对路径
      path.resolve("./m1.js")  // 不这么用
  
  // 这么用：(但是麻烦，也不这么用)
          // 第一个参数：绝对路径
          // 第二个参数：相对路径
      // 这样会自动计算出最终的路径
      path.resolve("C:\\User\\lilichao\\desktio\\03_包管理器","./m1.js")
          // 会显示成这样：C:\User\lilichao\desktio\03_包管理器\m1.js
  
  // 最终这么用：
      const result = path.resolve(__dirname,"./m1.js")
  ```

  

## Fs（file system）

- 用来帮助node来操作磁盘中的文件，也就是I/O（input和output 读写）

- 使用前先引入：

  ```js
  const fs = require("node:fs") 
  const fs = require("fs")
  ```

  - 方法：

    1. fs.readFile( )：读取磁盘中文件的数据，异步的,不会阻塞后面代码的执行

       ```js
       const buf =  fs.readFileSync(path.resolve(__dirname,"./hello.txt"))     // 同步的，会阻塞代码的执行
           console.log(buf.tostring())  // 通过 fs 模块读取磁盘的数据时，读取到的是一个buffer对象(缓冲区)的形式返回(乱码看不懂)，要用tostring解析才能看懂
           // readFileSync 是同步的读取文件的方法，会阻塞代码的执行，没读取出来下面的代码不执行
           // 直接写相对路径会报错，因为找的是目录下的找不到，所以灵活用路径要用path
               const buf =  fs.readFileSync(path.resolve(__dirname,"./hello.txt"))
                   // 不建议用readFileSync，会阻塞后续代码的执行
           console.log(buf.toString());
       fs.readFile()   // 读取磁盘中文件的数据，异步的,不会阻塞后面代码的执行
           fs.readFile(path.resolve(__dirname,"./hello.txt"),(err,buffer)=>{
               if(err){
                   console.log("出错了");
                   // 或者：throw err
               }else{
                   console.log(buffer.toString());
               }
           })
               // 第二个参数是回调函数
               // 正常也不这么用，因为有回调地狱的问题，用promise版本的fs
       ```

    2. fs.appendFile( )：创建新文件，或将数据添加到已有文件中

       ```js
       fs.appendFile()  
       fs.appendFile(
           path.resolve(__dirname,"./hello.txt"),
           "超哥讲的真不错"
           // 将"超哥讲的真不错"写入到./hello.txt文件里
       ).then(r => {
           console.log("添加成功");
       })
       // 如果没有./hello.txt这个文件，就自动创建一个新的文件，这个特性就可以用来复制文件
           // 复制一个文件：promise版本的
               fs.readFile("C:\\USERS\\图片\\an.jpg")
                   .then(buffer => {
                       return fs.appendFile(path.resolve(__dirname,"./haha.jpg"),
                       buffer
                       // 这个表示把上一步读取到的数据buffer添加到./haha.jpg下并改名为haha.jpg
                       )
                       // 如果不写return直接把箭头函数后面的大括号删了也可以
                   })
                   .then(() => {
                       console.log("操作结束");
                   })
       ```

    3. fs.mkdir( )：创建目录

       ```js
       fs.mkdir() 
           FileSystem.mkdir(path.resolove(__dirname,"./hello/abc"),{recursive:true}).then()
           // 在目录下hello文件中创建abc，目录下没有hello文件创建不了，现在得传第二个参数是一个对象recursive:true
       ```

    4. fs.rmdir( )：删除目录，用法同上

    5. fs.rm( )：删除文件

    6. fs.rename( )：重命名（等于是剪切文件）

       ```js
       fs.rename(    
           path.resolove(__dirname,"./hello.jpg"),     // 旧名
           path.resolove(__dirname,".an.jpg"))     // 新名
           .then(r => {
           console.log("重命名成功");
       }) 
       ```

    7. fs.copyFile( )：复制文件

    

  - promise版本的fs：

    - 引入：

      ```js
      const fs = require("node:fs/promises")
      const path = require("node:path") 
      fs.readFile(path.resolve(__dirname,"./hello.txt"))
          .then(buffer => {
          console.log(buffer.toString());
      })
          .catch(err => {
          console.log("出错了");
          throw err
      })
      ```

    - 也可以这么写，用async和await（上面promise的语法糖）立即执行函数：

      ```js
      ;(async()=>{
          try{
              const buffer = await fs.readFile(path.resolve(__dirname,"./hello.txt"))
              console.log(buffer.toString())
          }catch(err){
              console.log("出错了");
              throw err
          }
      })()
      ```

      

- 浏览器向服务器发送请求（request）
- 服务器向浏览器返回响应（response）



## express（服务器）

- 使用步骤：
  1. 创建并初始化项目： `yarn init -y`
  2. 安装express：  `yarn add express`
  3. 创建index.js 并编写代码（入口文件）

1. 引入express

   ```js
   const express = require("express")
   ```

2. 获取服务器的实例（对象）

   ```js
   const app = express()
   ```

3. 启动(监听)服务器  (3000是端口号) （监听代码写在最后面就可以）

   ```js
   app.listen(3000, () => {
       console.log("服务器启动了");
   })
   ```

   - 服务器启动后就可以通过端口来访问了
     - 协议名://ip地址:端口号/路径
     - http://localhost:3000
     - http://127.0.0.1:3000

4. 要想正访问服务器要设置路由，路由执行时，会接收到3个参数request,response,next

   ```js
   app.get("/", (request, response, next)=>{
       // 第一个参数 / 代表当前的根目录也就是：http://localhost:3000
       console.log("有人访问我了");
       response.sendStatus(404)
       // sendStatus()向客户端发送响应状态码（页面不转了，结束了）
       response.status(404)
       // 设置响应状态码，但是不发送（页面还是一直转，没结束）
       response.send("<h1>你的请求没问题，但就是不给你看</h1>")
       // 设置并发送响应体
   })
   app.post()
   ```

   4.中间件，和路由差不多，是在客户端发送给路由的中间（要写在路由代码的前面）

   ```js
   // 和路由的区别：
       // 1、会匹配所有的请求，/ 这里写的什么模糊匹配/abc/bcd的路径都可以访问到，如果是路由就不行
           // app.use("/hello", (req, res))    只要是/hello的路径都会经过这个中间件
       // 2、第一个参数"/"可以省略不写，默认就是根目录，如果不写就表示所有的请求都必须经过这个中间件
   app.use("/", (req, res) => {
       console.log("收到请求");
       res.send("这是通过中间件返回的响应")
   })
   // 第三个参数next() 放行给下一个路由或者中间件处理
       app.use("/", (req, res, next) => {
           console.log("111");
           // res.send("这是通过中间件返回的响应")     这里不做处理
           next()
       })
       app.use("/", (req, res) => {
           console.log("222");
           res.send("222")
       })
   ```

   要返回一个静态页面：

   ```js
   // 设置一个中间件，中间件的代码要在路由app.get()前面写，作用拦截
   // 设置static中间件后，浏览器访问时，会自动去public目录寻找是否有匹配的静态资源
   app.use(express.static("public"))   // 路径问题
   app.use(express.static(path.resolve(__dirname, "./public")))    //public是文件夹，里面有index.html
   	// 因为服务器是不让人看到的，但是项目在服务器的目录下，有些东西（静态页面）要让人看就用static()，这里的public文件夹就是让别人能访问到
   ```



- 练习：简易的登录

  ```html
  <form action="/login" method="get">
      <div>用户名 <input name="username" type="text"></div>
      <div>密码 <input name="password" type="pasword"></div>
      <div>
      <input type="submit" value="登录">
      </div>
  </form>
  ```

  ```js
  app.get("/login", (req, res) => {   // 这里的/login等于就是http://localhost:3000/login 
      console.log("请求收到了");
      if(req.query.username === "sunwukong" && req.query.password === "123123"){
      	res.send("<h1>登录成功</h1>")
      }else{
      	res.send("<h1>账号密码错误</h1>")
      }
  })
  ```

  - 表单优先使用post请求

    ```js
    // 得在前面先引入解析请求体，如果不引入req.body不能解析出来东西const urlencoded = require("urlencoded")
        app.use(express.urlencoded()) 
    app.post("/login", (req, res) => {
            // 用req.body可以拿到post请求体中的内容，但是express默认不解析请求体的内容，所以要利用中间件引入解析请求体 app.use(express.urlencoded())
        const usename = req.body.username
        const pasword = req.body.password
            // req.body本身是一个对象名值对: {username: 'zhubajie',password: '123456',repwd: '123456',nickname: '帅哥'}
        // 上面的拿username和password用解构赋值方法如下：
            const {username, password, repwd, nickname} = req.body
        // 验证方法一
            for(const user of USERS){   //  USERS 是储存账号数据的数组
                if(user.username === username){
                    if(user.pasword === password){
                        res.send(`<h1>登录成功 ${user.nickname} </h1>`)
                        return
                    }
                }
            }
            res.send("账号密码错误")
    
        // 验证方法二
            const loginUser = USERS.find(item => {
                return item.username === username && user.pasword === password
            })
            // 如果loginUser存在证明有匹配的账号密码，没有则返回underfined
            loginUser? res.send(`<h1>登录成功 ${user.nickname} </h1>`) : res.send("<h1>账号密码错误</h1>")
    
    
    
    
        // push对象可以直接写在push里面
            USER.push({
                username,
                password,
                nickname
            })
    })
    ```

    ```js
    // 可以在所有路由的后面配置错误路由或者中间件（404的时候）
        // 中间件不写路径就代表会匹配所有的路径
        app.use((req, res) => {
            res.status(404)
            res.send("<h1>您访问的地址不存在</h1>")
        })
    ```

    

## EJS（模板引擎）

1. 安装  `yarn add ejs`

2. 配置express的模板引擎为ejs

   ```js
   app.set("view engine", "ejs")
       // 配置模板的路径（真正开发只放一个项目没有路径问题，如果有多个项目则会有路径问题就需要下面这句代码配置路径）
           app.set("views", path.resolve(__dirname, "views"))
   ```

3. 在左边目录下创建名为views的文件夹，里面创建 students.ejs 以ejs结尾的文件，这里面相当于html文件，html怎么写这里面就怎么写

   ```js
   app.get("/students", (req, res) => {
       // 模板引擎需要被espress渲染后才能使用：render()
           res.render("students", {name: "孙悟空", age: "18"})  //render ： 把一个ejs的html页面渲染上去
               res.render("students", { stus:STUDENT_ARR }) // 注：传数组必须stus:STUDENT_ARR
           // render 第一个参数：文件名students.ejs中的students；第二个参数：传一个对象(值)给要渲染的页面
               // 注：render的第一个参数是要渲染文件的名字，只要写名字就会自动去views中寻找改名字的ejs的html页面文件
           // 然后在students.ejs文件中创建标签 <h1> <%=name %> </h1> 这样就可以打印了
               // res.render("students", {hello: "<h1>哈哈</h1>"})     如果这么写标签在里面：<h1>哈哈</h1>实体显示在页面
                   // <%= %>   转义后输出，就是写的什么就输出什么（页面显示hello：<h1>哈哈</h1>）
                   // <%- %>   只输出内容，没有标签（页面显示hello：哈哈），不好，会xss攻击
                   // <% %>    这里面可以写js代码
                   // <%# %>   ejs中html文件的注释
                       // 可以这么写：里面可以写html代码
                           // <% if(name === "孙悟空"){ %>
                           //     <h2>大师兄来了</h2>
                           // <% }else{ %>
                           //     <h2>二师兄来了</h2>
                           // <% } %>
   
   
   
       // 练习：直接在添加路由中渲染ejs会面临表单重复提交的问题（添加信息res.render重新渲染页面，每次刷新就重新提交表单向/add_students地址发送请求，自动添加了信息）
           res.redirect("/students")  // 重定向，作用是告诉浏览器你向另外一个地址再发起一次请求   
           // 本来点击添加信息按钮是向新写的post请求的/add_studends地址发送请求，上面地址栏会跳转到/add_studends
               // res.redirect()  的作用是添加完后返回/students地址下，这样就不会有重复提交表单的问题
   
           // 但是数据的数组是写死的在index.js文件下，服务器一重启新添加的数据就没了
               // 新建一个data文件夹，里面students.json文件，把数据放里面（记得name属性类的要加引号）
                   // 然后index.js引入const student_arr = require("./data/students.json")
                   student_arr.push(newUser)
                           // 注：在外面先引入fs的promises，const fs = require("fs/promises")
                   // 然后将新的数据写到json文件中
                       fs.writeFile(path.resolve(__dirname,"./data/students.json"), JSON.stringify(student_arr))
                       .then(()=>{
                           // 写入成功后再进行重定向
                               res.redirect("/students") 
                       }).catch(()=>{
   
                       })
   
           // 删除功能：
               // 1、创建一个/delete的get请求的路由，通过const id = req.query.id获取查询字符串传过来的id
               // 2、然后再students.ejs的td中<a href="/delete?id=<%=stu.id %>">删除</a>
               // 3、筛选出不符合这个id的students数组，再将这个数组放入json文件中
   })
   ```

   

## router

- 相当于是一个中间件，可以在这绑定各种路由和中间件（所有的功能的路由都写到index.js里面，太多，不好管理，所以需要router分成一个一个的模块）
- 引入const router = express.Router()
- 以前app.get()，现在router.get()
- 然后再app.use(router)   启动

1. 创建一个routes文件夹，里面专门放一些路由js文件(模块化，里面专门写user的路由或goods等等其他专门的路由文件)

   ```js
   const express = require("express")    // router是属于express模块的，所以要先引入express
   const router = express.Router()     // 创建router对象
   module.exports = router     // 将router暴露到模块外
   ```

2. 然后再index.js中引入

   ```js
   const userRouter = require("./routes/user")     // 引入
       app.use(userRouter)     // 利用中间件使路由生效
       // 但是routes里文件一多有可能请求路径有重复的 （都是router.get("/list")）
           // 所以我们要在index.js使路由生效的代码加专属的路径前缀：（本来是/user，会变成/user/list）
               app.use("/user", userRouter)   
               app.use("/goods", goodsRouter)
               // 注：这里启动路由的/user跟routes文件夹里的student.js里写的router.get("/list",)不一样，student.js里的路径可以随便取，但是启动router的路由必须跟ejs的html表单的action属性一样
               // 这样访问user的路径就变成：/user/list     访问goods的路径：/goods/list    然后ejs的html代码的表单的action也要改成这个路径
   ```

   

## cookie

- 是HTTP协议用来解决无状态问题的技术（识别客户端的用户）

- cookie 的本质是一个头

  - 服务器以响应头的形式将cookie发送给客户端

  - 客户端收到后会将其存储，并在下次向服务器发送请求时将其返回

  - 这样服务器就可以根据cookie来识别客户端了

    ```js
    res.cookie("username", "admin") // 存cookie：1属性名，2属性值（专属的名字比如账号）
    req.cookies.username    // 读取cookie，注意要加s
    ```

  - 想路由里读取cookie,要先安装中间件来使得express可以解析cookie：

    1. 安装：`yarn add cookie-parser`

    2. 引入：

       ```js
       const cookieParser = require("cookie-parser")
       ```

    3. 设置成中间件：

       ```js
       app.use(coolieParser())
       ```

  - cookie 默认的有时效为一次会话（session）,关闭浏览器就没了

  - res.cookie第三个参数是一个配置对象，可以传一个对象{}，可以设置cookie的时效

    - 对象里写 maxAge: 1000 * 60  单位毫秒，表示这个cookie只有1秒的时效

  - cookie时效想去掉：cookie一旦发送给浏览器就不能再修改，但是可以重新发送来替换原来的cookie

- cookie每次请求都会发送，导致不能发送太大的信息数据，也不安全（存在客户端，容易被篡改盗用），所以用下面的 session



## session

- 用来存储用户的数据(通过cookie的形式将session发给客户端，用session不需要cookie功能，发送session可以再网页的cookie查看)  默认有效期是一次会话

- session是服务器存储的一个对象（存在内存中），把用户的数据存在服务器中，然后自动生成一个id或name或{}发送给客户端

- 每个session都有对应的id，id会通过cookie的形式发送给客户端

- 客户端每次访问时只需将存储有id的携带session的cookie发回即可获取它在服务器中存储的对应的数据

  1. 安装：`npm i express-session`

  2. 引入：

     ```js
     const session = require("express-session")
     ```

  3. 设置位中间件

     ```js
     // 中间件第一个参数如果不写路径，无论访问这个目录或域名的哪个页面都会进入这个中间件
     app.use(session({   // 发送session
         secret: "hello"      // 必写的属性，加密用，随便写，等于密钥
     }))
     req.session.loginUser = username    // 存session的username
     req.session.loginUser     // 读session
     ```

  

  - 注：先写一个app.get("/set")发送（向session中添加存储，注意得用req.session）req.session.loginUser = "sunwukong"，然后再写一个app.get("/get")拿session数据const loginUser = req.session.loginUser

    - 访问也得先访问 /set 再 /get

      

- 重启服务器用户对应session就会没了（内存里就没了），所以要对session进去持久化的操作：写到本地文件或数据库里（实际开发存数据库），如果内存有就去内存找，没有就去本地文件里找（本地文件名为sessions）

  - 引入一个中间件

    - 使用（存本地文件）：

      1. 安装： `yarn add session-file-store`

      2. 引入：

         ```js
         const FileStore = require("session-file-store")(session)
         ```

      3. 设置为中间件：

         ```js
         store: new FileStore({}) // 写在session的中间件就行
         ```

         ```js
         app.use(session({
             store: new FileStore({
                 path: path.resolve(__dirname, "./sessions"), // 指定保存的路径
                 secret: "haha",  // 加密本地sessions文件里的信息变为各种随机值
                 ttl: 3600,   // 设置sessions有效时间，默认3600秒
                 reapInterval: 3600  // 每隔一段时间清除session文件里的json文件，默认3600秒
             }),
             secret: "hello"      // 必写的属性，加密用，随便写，等于密钥
         }))
         ```

  - 退出登录：使session失效

    ```js
    req.session.destory(() => {
        res.redirect("/")
    })  
    ```

    

## 解决csrf攻击

1. 在路由中使用referer请求头来检查请求的来源，看路径是不是本地发起的

   ```js
   const referer = req.get("referer")
   if(!referer || !referer.startsWith("http://localhost:3000/")){
       res.status(403).send("你没有访问权限！")
       return
   }
   ```

2. 使用验证码（比如手机）

3. 尽量使用post请求，结合token令牌（uuid）

   - 创建表单时随机生成一个令牌，将令牌存到session里，通过ejs发送给用户

   - 用户提交表单时，必须将token发回才可以进行后续操作（页面写一个标签然后type:hidden隐藏保存token）

   - 每操作一次增删改查都对比发送的token和页面的token是否一致，如果不一致就不让操作，每操作一次就生成一个token

     - `yarn add uuid`

     ```js
     const uuid = require("uuid").v4     // 引入uuid版本v4
     const token = uuid()                // 生成uuid
     req.session.token = token           // 将token存到session中
     req.session.save(()=>{              // session保存完在渲染页面，再把token通过ejs的render传给/students/list页面
         res.render("students",{
             stus: STUDENT_ARR,
             username:req.session.loginUser,
             token
         })
     })
     ```

     - ejs表单里创建一个input，放token，然后type:hidden
     - 然后将客户端页面input发送的token和session发送的token比对，不对就不让做
     - 然后把token销毁，req.session.token = null



- 注：express加功能就是多添加一个中间件



## nodemon

- 自动监视代码保存重启服务器
- 安装：`yarn add nodemon -g`，-D在当前目录装（先在终端输入cd .\09_express\ 进去要安装的目录）
- 启动
  1. 终端输入：`nodemon`
  2. nodemon .\01_hello.js，运行指定的js



## 补充

### 包管理器：

- package.json 是node项目必须创建的文件，里面是对项目的描述

  - 自动创建 package.json 文件：
    - `yarn init`  意思是初始化项目，然后再终端输入文件名 my_project，然后一直回车即可
      - 里面的"main": "index.js"  这个是入口文件，最好用index来命名

- 下载包：`yarn add 包名`  （下载到当前项目中，会多一个node_modules文件夹，里面是下载的包）

- 删除包：`yarn uninstall 包名`

- yarn add 拿到别人的项目输入这个会自动安装所有的依赖，在package.json文件中的dependencies属性就是项目的所有包名

- 引入包： 

  ```js
  const _ = require("lodash")
  ```





### 响应状态码：

- 1xx 请求处理中
- 2xx 成功
- 3xx 请求重定向
- 4xx 请求错误



### param

- get请求发送参数的第二种方式：param (以冒号命名的部分)

- /hello/:id 表示访问/hello开头的路径都可以（/hello/dsff5ds45）
- 通过 req.params 来获取数据

### 其他

- const id = stuArr.at(-1).id + 1    自动数组id+1



- <a onclick="return confirm('确认删除吗？')"></a>     在标签中点击事件直接return一个事件可以



- student.gender === "男" && "checked"   如果gender的值是男，则设置位checked（&&的一个用法）



- <input type="hidden" name="id" value="<%=student.id %>"/>   type="hidden"隐藏input框，但是还是有数据id通过表单post请求体传个服务器



- const student = STUDENT_ARR.find(item => item.id == id)  
  - student.name = name    通过find()找到对应id的对象，直接修改里面的属性值就可以了，不用再重新放进数组里

