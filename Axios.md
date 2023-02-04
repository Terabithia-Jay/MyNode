## REST

- Rest:一种服务器的设计风格，服务器只返回数据(json)

- 请求方法：

  1. GET：跟服务器拿数据
  2. POST：在服务器新建或添加数据
  3. PUT：在服务器新建、添加或修改数据
  4. PATCH：修改数据
  5. DELETE：删除数据
  6. OPTION：由浏览器自动发送，检查请求的一些权限

  

- postman 可以帮助向服务器发送各种请求，模拟发送请求（不用在html新弄一个表单发送）

  

  ## AJAX

  - ajax：通过js向服务器发送请求来加载数据

  ​    A：异步

  ​    J：Javascript

  ​    A：and

  ​    X：xml

  - 早期的xml：

    ```html
    <student><name>孙悟空</name></student>
    ```

  - 现在用json


  ​    

  - 发送AJAX的方案：
    1. XMLHTTPRequest（xhr）  最早的，麻烦
    2. Fetch   xhr的升级版
    3. Axios   xhr的封装，现在大多用这个

### xhr

​	1、创建一个新xhr对象

```js
const xhr = new XMLHttpRequest()
```

​	2、设置响应体的类型，自动转换json对象为js对象

```js
xhr.responseType = "json"
```

​	3、设置请求信息

```js
xhr.open("GET", "http://localhost:3000/students/")  // GET请求，请求路径
```

​	4、发送请求

```js
xhr.send()
```

​	5、服务器index.js最前面设置一个中间件，设置一个允许跨域的头

```js
res.setHeader("Access-Control-Allow-Origin", "*") 
```

​	6、读取相应数据

```js
// 因为AJAX是异步的，当send出去后就直接response是拿不到数据的，因为数据还没返回，所以要为response添加一个xhr对象绑定一个load事件
    xhr.onload = function(){
	// 如果路径错误/students/123，console.log(xhr.response)则会输出服务器发回的404的页面，所以要做判断
        if(xhr.status === 200){
			// 拿到数据json要转换：
			// 1、手动转换：
	    	const result = JSON.parse(xhr.response)
             console.log(result.status, result.data);
             // 2、自动转换：在创建xhr的时候下面创建一个类型转换：xhr.responseType = "json"，这样当我们拿到json的时候会自动转换为js对象
             console.log(xhr.response);
             // 服务器是这么写的发送数据
             // res.send({
             //     status: "ok",
             //     data: updateStu  学生数据数组
             // })
        }
    }
```



### CROS（跨域资源共享）

- 跨域请求：如果两个网站的完整的域名不同
- 跨域会检查三个东西：1、协议 2、域名 3、端口号  ，只要有一个不同，就算跨域

- 客户端一个文件夹my-client，服务器一个文件夹my-server

  - /my-client里的html（http://127.0.0.1:5500/my-client/index.html）里发送AJAX请求到my-server（http://localhost:3000/students/）	这个就是跨域

- 当通过AJAX发送跨域请求时，浏览器为了服务器的安全，会阻止js读取到服务器的数据

  - 解决方案：在服务器设置一个允许跨域的头：

    ```js
    Access-Control-Allow-Origin
    Access-Control-Allow-Methods    // 允许请求的方式
    Access-Control-Allow-Headers    // 允许传递的请求头
    // 在index.js最前面设置一个中间件：
    app.use((req, res, next) => {
        res.setHeader("Access-Control-Allow-Origin", "*")  // *代表所有人都可以访问
        res.setHeader("Access-Control-Allow-Origin", "http://127.0.0.1:5500")  // 代表只能这个路径html的才可以发送请求，写死只能设置一个，动态可以写多个
        res.setHeader("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, PATCH")
        res.setHeader("Access-Control-Allow-Headers", "Content-type")
        next()
    })  
    ```

  - 然后在index.js最前面设置一个中间件：

    ```js
    app.use((req, res, next) => {
        res.setHeader("Access-Control-Allow-Origin", "*")  // *代表所有人都可以访问
        res.setHeader("Access-Control-Allow-Origin", "http://127.0.0.1:5500")  // 代表只能这个路径html的才可以发送请求，写死只能设置一个，动态可以写多个
        res.setHeader("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, PATCH")
        res.setHeader("Access-Control-Allow-Headers", "Content-type")
        next()
    })  
    ```

    

## Fetch

1. 服务器index.js最前面设置一个中间件，设置一个允许跨域的头

   ```js
   res.setHeader("Access-Control-Allow-Origin", "*") 
   ```

2. Fetch是原生js的一个方法，可以直接用

   ```js
   fetch("http://localhost:3000/students")     // 如果是只填资源路径就是get请求，如果第二个参数有一个配置对象，就可以是别的（post）请求
   .then(result => {
       if (result.status === 200) {
           // result.json() 这个读取到的是promise对象，要接着then读取里面的数据，return出去下面的then就可以读取到数据
           return result.json()
       } else {
           throw new Error("加载失败")
               // 只要抛出错误，同{}的后面的代码就不执行
       }
   })
   // 上一步return的promise对象下一步then就可以读取到上一步的数据
   .then(result => {
       // 渲染数据
       if (result.status === "ok") {
           // 在这渲染数据
       }
   })
   .catch(error => {
       console.log("出错了", error);
   })
   ```

   - 如果要发post或其他请求

     ```js
     fetch("http://localhost:3000/students", {
         // 指定post请求
         method: "post",
         // 通过body去发送数据时，必须通过请求头来指定数据的类型
         headers: {
             // 表示我们要发送的数据类型是json的数据类型
             "Content-type": "application/json"
         },
         // 发送请求体数据内容到服务器
         body: JSON.stringify({
             name: "白骨精",
             age: 16,
             gender: "女",
             address: "白骨洞"
         })
     })
     ```

     

     

### token

- 登录后直接将用户储存到 localStorage 主要存在两个问题：1、数据安全 2、服务器不知道你有没有登录

- 解决办法：

  - 服务器不储存用户的数据，将用户信息发送给客户端保存，客户端每次访问服务器时，直接将用户的信息发回，服务器就可以根据用户信息来识别用户的身份，但也有安全问题，所以要对数据进行加密，加密后再发给客户端保存

  - 客户端信息数据发给服务器，服务器把信息加密，然后把token发给客户端存在本地存储，客户端每次访问只需把token发回

  - 在node中可以直接使用 jsonwebtoken（jwt） 来对数据进行加密

    1. 安装：

       `npm i jsonwebtoken`

    2. 引入：

       ```js
       const jwt = require("jsonwebtoken")
       ```

    3. 客户端将账号密码发给服务器：

       ```js
       fetch("http://localhost:3000/login", {
           // 指定post请求
           method: "POST",
           // 通过body去发送数据时，必须通过请求头来指定数据的类型
           headers: {
               // 表示我们要发送的数据类型是json的数据类型
               "Content-type": "application/json"
           },
           // 发送请求体数据内容到服务器
           body: JSON.stringify({ username, password })
       })
       ```

    4. 服务器收到数据后设置token：jwt.sign

       ```js
       const token = jwt.sign(obj, "hello", {        
           //   第一个参数为数据对象（客户端发过来的账号和密码），
           //   第二个参数为秘钥，通常是随机的一串字符
           //   第三个参数为option配置对象
           expiresIn: "1h"     // 表示token的有效时间为1小时
           // expiresIn: "1d"     1天
           // expiresIn: "1"      1毫秒
           // expiresIn: 1        1秒
       })
       // 然后把token发给客户端：
           res.send({
               status: "ok",
               data: {
                   token,
                   // 也可以发一些不敏感的信息过去，比如nickname
                   nickname: "超级管理员"
               }
           })
       ```

    5. 客户端拿到服务器发过来的token时，在登录成功的代码把token存在本地存储，然后渲染数据

       ```js
       // 这里的then紧接着第3步登录请求的下面写
       .then((res) => res.json())  // 这里res.json()这么写等于是直接return res.json()
           .then((res) => {
           if (res.status !== "ok") {
               throw new Error("用户名或密码错误")     
           }
           localStorage.setItem("token", res.data.token)
       })
           .catch((err) => {
           console.log("出错了！", err)
       })
       ```

    6. 客户端每次发送请求时，将token从本地拿出来发给服务器

       ```js
       const token1 = localStorage.getItem("token")
       fetch("http://localhost:3000/students", {
           // 通过fetch的第二个参数配置请求头发送token
           headers: {
               // 需要这么配置请求头，服务器也要配置相应的Authorization跨域请求
               "Authorization": `Bearer ${token1}`
               // 在index.js 中students的路由中req.get("Authorization")获取发过去的token
           }
       })
       ```

    7. 客户端发送请求时发送token放在请求头里，服务器拿到客户端的token后解密：jwt.verify

       ```js
       try {
           // 获取请求头的发过来的token
           // 这里拿到的token是 Bearer + 空格 + token，所以要先截取数组，以空格为标准，变为2个索引，截取索引为1的
           const token = req.get("Authorization").split(" ")[1]
           // 对token进行解密
           const decodeToken = jwt.verify(token, "hello")
           // 解码成功，token有效
               console.log(decodeToken);
           // 解码成功后返回学生信息
           res.send({
               status: "ok",
               data: STU_ARR
           })
       } catch (e) {
           // 解码错误，token无效
           res.status(403).send({
               status: "error",
               data: "token无效"
           })
       }
       ```

    - 如果客户端发送请求，没有响应或者响应的很慢，客户端想终止请求的话，在html页面创建 AbortController()

      ```js
      const controller = new AbortController()
      fetch("http://localhost:3000/test", {  
          // 把controller信号配置到fetch里
          signal: controller.signal
      })
      then(res => console.log(res))
          .catch(err => console.log("出错了", err))
      // 点击按钮取消发送请求
      controller.abort()
      
      
      // 用async和await发送fetch请求，一定要写try-catch
      btn.onclick = async () =>{
          try{
              const res = await fetch("http://localhost:3000/test")
              const data = await res.json()
              console.log(data);
          }catch(e){
              console.log("出错了", e);
          }
      }
      ```

      

## Axios

1. 安装：`yarn add axios`

2. html引入，目前没有学习包，就先用script引入

   ```js
   <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
   ```

3. 应用

   - 发post请求，主要用这个好用

     ```js
     axios({     // 配置对象里只有url是必须写，其他的都可以不写
         method: 'post',
         // baseURL:"http://localhost:3000",     指定服务器的根目录，如果这边这么写，下面的url就只需写studetns即可
         url: 'http://localhost:3000/students',  
         // 设置请求头发送token
         headers:{
             "Authorization": `Bearer ${token}`
         },
         // axios会根据data的类型自用设置请求头，不需要像fetch一样在headers中 "Content-type": "application/json"
         data: {
             name: '唐僧',
             age: 18,
             gender: "男",
             address: "女儿国"
         },
         // data: "name=唐僧&age=16"     这个是用urlencoded请求体发，获取req.body
         // params可以指定查询字符串的内容
         params:{
             id: 1,
             name: "swk"
         },
         // timeout 过期时间（毫秒）
         timeout: 1000,      // 表示如果1秒后响应没有返回的话则自动取消请求
         // signal   终止请求，需要在外面先 const controller = new AbortController()，用法跟上面的242行一样
         signal: controller.signal,
         // transformRequest可以用来处理请求数据（data），参数为数组，数组里可以多个函数，层层接收上一层函数的data和headers的值
         transformRequest:[function(data, headers){
             // 可以在函数里对data和headers进行修改
             data.name = "猪八戒"
             headers["Content-Type"] = "application/json"    // 这里的Type的T必须大写
             return data
         }, function(data, headers){
             console.log(data, headers);
             // 最后一个函数必须返回一个字符串
             return JSON.stringify(data)
         }]
     }).then(result => {
         // 这里的result是封装过的
         console.log(result.data);
         // axios默认只会在响应状态码为2XX时才会调用then，其他都调用catch
         // fetch在这里还要判断响应状态码，如果不是正确的则需要主动throw出错误，而axios不需要
     }).catch(err => {
         console.log("出错了", err);
     })
     ```

   - 指定默认配置，在axios({})外部，然后下面的每个axios({})里面就不用写了

     ```js
     // 配置默认路径
     	axios.defaults.baseURL = "http://localhost:3000"
     	// 然后axios({})里面的配置对象的url就只需写 url:"students" 即可
     // 配置默认请求头
     	axios.defaults.headers.common['Authorization'] = `Bearer ${token}`;    
     	// common表示适配所有的请求方式，比如get，post，也可以把common改成get或post
     ```

   - axios实例

     ```js
     // 比如上面设置了默认配置axios.defaults.baseURL = "http://localhost:3000"
         // 但是另一个axios并不是用的3000的端口，此时我们可以创建一个axios实例instance
             // axios实例中的默认配置也是原来的，但可以手动改，用法和axios一样，如果有多个端口就创建多个实例对应不同的请求
                 const instance = axios.create({
                     baseURL: "http://localhost:4000"
                     // 这样我们就可以在实例中指定别的端口
                 })
                 instance.get("students").then(()=>{}).catch(()=>{})
     ```

   - axios拦截器，对请求或响应进行拦截，在请求发送前和响应读取前处理数据

     - 请求拦截器 

       ```js
       axios.interceptors.request.use(     // 这里前面写axios，拦截器只对当前的实例有效，如果有新的实例，比如const 										myaxios=axios.create()，对myaxios的请求就无效了
           // 第一个参数的函数是在发送请求之前做些什么
               function (config) {
                   // 这里的config就是请求的配置对象里的内容：axios({method: 'post', url: 									  'http://localhost:3000/students'})
                       // 在发送前可以对配置对象里的数据进行修改再发送 config.data.name = "haha"
                   // 通常拦截器的用法是在这配置请求头：作用跟中间件一样
                       config.headers['Authorization'] = `Bearer ${localStorage.getItem("token")}`
                           // 这个的意思是每个请求发送前都会通过这个拦截器，都会配置上这个请求头
                   return config;
           // 第二个参数函数不用动
               }, function (error) {
                   // 对请求错误做些什么
                   return Promise.reject(error);
       });
       ```

     - 响应拦截器

       ```js
       axios.interceptors.response.use(function (response) {
           // 2xx 范围内的状态码都会触发该函数。
           // 对响应数据做点什么
           return response;
       }, function (error) {
           // 超出 2xx 范围的状态码都会触发该函数。
           // 对响应错误做点什么
           return Promise.reject(error);
       });
       ```

     - 取消拦截器  

       ```js
       // .eject 清除当前拦截器
       // .clear 清除所有拦截器
           const myInterceptor = axios.interceptors.request.use(function () {/*...*/});
           axios.interceptors.request.eject(myInterceptor);
       ```



- 发get请求简写：

  ```js
  axios('http://localhost:3000/students')
      .then()
      .catch()
  ```

- form表单点击登录发送请求会自动提交表单发生页面跳转，可以取消默认行为或者在button标签里添加一个type="button"，默认的是type="sumit"