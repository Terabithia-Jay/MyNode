## 安装

1. 安装：`yarn add typescript`

2. 查看安装是否成功：`npx tsc`

3. 编译成js：`npx tsc .\xxx.ts`

   - 创建tsconfig.json配置文件：`npx tsc -init`

   - 编译所有文件：`npx tsc`      -->     yarn build           内置服务器：yarn dev

   - 自动监视ts文件并编译为js：`npx tsc -w`

     

## 限制变量的类型

```ts
// 声明一个变量a，并指定类型为number
    let a: number
    a = 10

// 声明完变量直接进行赋值
    let c: boolean = true
    
// 如果变量的声明和赋值是同时进行的，TS可以自动对变量进行类型检测，可以省略: boolean
    let c = true
    c = true

// 可以在参数进行类型的指定，参数多少个只能传多少个，而js可以传超过形参的数
    function sum(a: number, b: number): number | string {	// 在函数后面加: number表示函数的返回值的类型指定为number
        return a + b
    }
    sum(123, 456)

// 直接使用字面量进行类型声明
    let a: "male" | "female"	// 限制a的值只能是male或female
    a = "male"
    a = "female"
    let a: "boolean" | "string"

// any任意类型，相当于对该变量关闭了TS的类型检测(如果把any变量赋值给别的指定类型的值，也会改变被赋值的那个，unknown不会)
	let b: any
// unknown 表示未知类型的值不能直接赋值给其他变量
    let e: unknown
    // 解决办法：
    	if(typeof e === "string"){
            s = e
        }
	// 或使用类型断言：(告诉ts这个e就是string类型)
		s = e as string
		s = <string>e

// void 表示空，以函数为例就表示没有返回值的函数（不能写返回值）
	function fn(): void{
        // 不能写返回值
    }
// never 表示用于不会返回结果
	function fn(): never{
        throw new Error("报错了！")
    }

// object
	// 定义 对象 结构类型声明
	let b: { name: string, age?: number }		// age?: number 加问号表示是可选的，下面有age也行，没有也行
    b = { name: '孙悟空', age: 18 }

	let c: { name: string, [propName: string]: any }	
    	// 表示c只限制一个name的值为string，[propName: string]表示任意属性名，: any表示任意属性值
    c = {name: '猪八戒', age: 18, gender: '男'}

	// 定义 函数 结构的类型声明。语法：(形参：类型, 形参：类型...) => 返回值
		let d: (a: number, b: number)=>number
         d = function (n1, n2): number{
             return 10
         }
	
	// 定义 数组 结构的类型声明
		let e: string[]		// 字符串数组
         e = ["a", "b", "c"]
		
		let g: Array<number>	// 数字数组
         g = [1, 2, 3]
		// 元组，固定长度的数组，声明多少个就必须只写多少个
			let h: [string, number]
             h = ["hello", 123]

	// enum 枚举，适用于有多个固定的值选择
		// 定义一个枚举的类
			enum Gender{
                Male,
                Female
             }
			let i: { name: string, gender: Gender }
             i = {
                 name: '孙悟空',
                 gender: Gender.Male
             }
			console.log(i.gender === Gender.Male)

let j: { name: string } & { age: number }	// &表示同时满足，且
j = { name: '孙悟空', age: 18 }

// 自定义类型别名
	type myType = 1 | 2 | 3 | 4 | 5
	let k: myType
    k = 1
```



## 配置文件 tsconfig.json

```json
{
    "include": [	// 指定哪些ts文件需要被编译
        "./src/**/*"	// 表示src目录下的所有文件都会被编译，**表示任意目录，*表示任意文件
    ],
    "exclude": [	// 排除哪些文件被编译，表示src下的hello文件夹不被编译
        "./src/hello/**/*"
    ],
    "compilerOptions": {	// 编译器的选项
        "target": "es2016",		// 用来指定ts被编译为的es的版本，esNext最新版本
        "module": "es2015", 	// 指定模块化的规范
        "lib": [],			// 指定项目要使用的库（通常不用动）
        "outDir": "./dist",		// 指定编译后文件所在的目录
        "outFile": "./dist/app.js",		// 将编译后的代码合并为一个文件（不用）
        "allowJs": true, 	// 是否对js文件进行编译，默认false
        "checkJs": true, 	// 是否检查js代码是否符合语法规范，默认fasle
        "removeComments": true, 	// 编译的时候是否移除注释，默认false
        "noEmit": true, 	// 不生成编译后的文件，有编译但是不生成js文件，默认false
        "noEmitOnError": true, 	// 当有错误时不生成编译后的文件，默认false
        "alwaysStrict": true, 	// 设置编译后的文件是否使用严格模式，默认false
        "noImplicitAny": true,	// 不允许隐式的any类型，默认false
        "noImplicitThis": true,	 // 不允许不明确类型的this，默认false
        "strictNullChecks": true,	// 严格检查空值（如果获取的值是不存在的则提示错误）
        "strict": true,	  // 所有严格检查的总开关，默认false（建议开）
    }
}
```

```js
let box1 = document.get ElementById('box1')
box1?.addEventListener('click', function (){	// es6语法可选链，表示如果box1存在才绑定单击事件
    alert('hello')
})
```



## 使用webpack打包ts代码

1. 初始化项目：`yarn init -y`

2. 安装包：

   ​	`yarn add -D webpack-cli webpack typescript ts-loader html-webpack-plugin webpack-dev-server clean-webpack-plugin`

3. 目录下创建 webpack.config.js 文件

   ```js
   // 引入path
   const path = require('path')
   
   // 引入自动生成html文件插件
   const HTMLWebpackPlugin = require('html-webpack-plugin')
   
   // 引入clean插件（打包的时候先清空dist文件夹）
   const {CleanWebpackPlugin} = require("clean-webpack-plugin")
   
   // webpack中的所有的配置信息都应该写在module.exports中
   module.exports = {
       // 指定入口文件
       entry: "./src/index.ts",
   
       // 指定打包文件所在的目录
       output: {
           // 指定打包文件的目录
           path: path.resolve(__dirname, 'dist'),
           // 打包后文件的名字
           filename: "bundle.js"
       },
       mode: "development",
   
       // 指定webpack打包时要使用的模块
       module: {
           // 指定要加载的规则
           rules: [
               {
                   // test指定的是规则生效的文件
                   test: /\.ts&/,   // 匹配所有以ts结尾的文件
                   // 要使用的loader
                   use: "ts-loader",
                   // 要排除编译的文件
                   exclude: /node-modules/
               }
           ]
       },
   
       // 配置 HTMLWebpackPlugin 和 CleanWebpackPlugin 插件
       plugins: [
           new CleanWebpackPlugin(),
           new HTMLWebpackPlugin()
           // new HTMLWebpackPlugin({
           //     // 配置dist下生成的html文件会从这个路径的html模板，等于是复制
           //     template: "./src/index.html"
           // })
       ],
       
   	// 设置引用模块，表示以.js或.ts格式的文件都可以作为模块使用（原因：ts文件暴露出去，其他的ts文件不能用）
       resolve: {
           extensions: ['.ts', '.js']
       }
   }
   ```

4. 目录创建 tsconfig.json 文件

   ```json
   {
     "compilerOptions": {
         "module": "ES2015",
         "target": "ES2015",
         "strict": true
     }
   }
   ```

5. package.json 中加："build": "webpack" 快捷键、"dev": "webpack server --open"内置服务器快捷键

   ```json
   {
     "name": "hello",
     "version": "1.0.0",
     "description": "",
     "main": "index.ts",
     "scripts": {
       "test": "echo \"Error: no test specified\" && exit 1",
       "build": "webpack",
       "dev": "webpack server --open"
     },
     "keywords": [],
     "author": "",
     "license": "ISC",
     "devDependencies": {
       "html-webpack-plugin": "^5.5.0",
       "ts-loader": "^9.4.2",
       "typescript": "^4.9.5",
       "webpack": "^5.75.0",
       "webpack-cli": "^5.0.1",
       "webpack-dev-server": "^4.11.1"
     }
   }
   ```

6. 目录下创建 src 文件夹，里面创建 index.ts 文件

7. 启动编译打包：`yarn build`

8. 打包并打开网页：`yarn dev`

   

   - 兼容老版本浏览器和代码：babel 插件：

     1. 安装：`yarn add -D @babel/core @babel/preset-env babel-loader core-js`

     2. 配置webpack.config.js：use: [ ]里、output:{ } 里 environment

        ```js
        // 引入path
        const path = require('path')
        
        // 引入自动生成html文件插件
        const HTMLWebpackPlugin = require('html-webpack-plugin')
        
        // 引入clean插件，打包时自动清空dist文件夹
        const { CleanWebpackPlugin } = require("clean-webpack-plugin")
        
        // webpack中的所有的配置信息都应该写在module.exports中
        module.exports = {
            // 指定入口文件
            entry: "./src/index.ts",
        
            // 指定打包文件所在的目录
            output: {
                // 指定打包文件的目录
                path: path.resolve(__dirname, 'dist'),
                // 打包后文件的名字
                filename: "bundle.js",
                // 告诉webpack不使用箭头函数
                environment:{
                    arrowFunction: false
                }
            },
            mode: "development",
        
            // 指定webpack打包时要使用的模块
            module: {
                // 指定要加载的规则
                rules: [
                    {
                        // test指定的是规则生效的文件
                        test: /\.ts&/,   // 匹配所有以ts结尾的文件
                        // 要使用的loader
                        use: [
                            // 配置babel
                            {
                                // 指定加载器
                                loader: "babel-loader",
                                // 设置babel
                                options: {
                                    // 设置预定义的环境
                                    presets: [
                                        [
                                            // 指定环境插件
                                            "@babel/preset-env",
                                            // 配置信息
                                            {
                                                // 要兼容的目标浏览器
                                                targets: {
                                                    // 兼容的版本
                                                    "chrome": "88",
                                                    "ie":"11"
                                                },
                                                // 指定corejs的版本
                                                "corejs": "3",
                                                // 使用corejs的方式，usage表示按需加载
                                                "useBuiltIns": "usage"
                                            }
                                        ]
                                    ]
                                }
                            },
                            "ts-loader"
                            // 简化版：
                            // "babel-loader",
                            // "ts-loader"
                        ],
                        // 要排除编译的文件
                        exclude: /node-modules/
                    }
                ]
            },
        
            // 配置 HTMLWebpackPlugin 插件
            plugins: [
                new CleanWebpackPlugin(),
                new HTMLWebpackPlugin()
                // new HTMLWebpackPlugin({
                //     // 配置dist下生成的html文件会从这个路径的html模板，等于是复制
                //     template: "./src/index.html"
                // })
            ],
        
            // 设置引用模块，表示以.js或.ts格式的文件都可以作为模块使用（原因：ts文件暴露出去，其他的ts文件不能用）
            resolve: {
                extensions: ['.ts', '.js']
            }
        }
        ```
     
     3. 编译打包：`yarn build`
     
        

## 面向对象

- 使用class关键字来定义一个类

- 对象中包含两个部分：1、属性   2、方法

```ts
class Person {
    // 定义属性：
    	name = "孙悟空"	// 直接定义即可，因为有类型检测，下面是完整写法
        // 1、直接定义的属性是实例属性，需要通过对象的实例去访问
        name:string = "孙悟空"
        age: namber = 18
        // 2、在属性前使用static关键字可以定义类属性（静态属性）
        static age: namber = 18

        readonly name:string = "孙悟空"	// readonly只读不能修改
        static readonly name:string = "孙悟空"	// 静态的只读属性

    // 定义方法：
        sayHello(){
            console.log('hello')
        }
}
const per = new Person()

// 通过实例属性去访问
console.log(per.name, per.age);
// 类属性就要通过类去访问，无需创建new实例对象就可以直接访问
console.log(Person.age);

per.name = "tom"

per.sayHello()
```



### constructor 构造函数
- 每次调用实例对象就会调用constructor

```ts
class Dog{
    // 先定义name和age的类型
    name: string
    age: number
    // 动态的属性name和age而不是写死的，在创建多个类的时候才可以多次使用，写在constructor里面
    // constructor 构造函数，会在对象实例创建时调用，每次创建new Dog()的时候都会调用constructor
    // constructor初始化属性名：
    constructor(name: string, age: number){
        // console.log(this)	这里的this表示当前的实例dog、dog1、dog2...,通过this向新建的对象中添加属性
        this.name = name
        this.age = age
    }
    bark(){
        alert('汪汪汪')
        // console.log(this.name)	这里的this就是谁调用就是谁
    }
}

// 创建多个Dog类
const dog = new Dog('旺财', 1)
const dog1 = new Dog('小黑', 2)
const dog2 = new Dog('小白', 3)

console.log(dog)
console.log(dog1)
console.log(dog2)
console.log(dog2.bark())
```



### extends（继承）

- 继承：extends，重写：overrides

```js
// 定义一个动物类，父类
class Animal {
    name: string
    age: number
    constructor(name: string, age: number) {
        this.name = name
        this.age = age
    }
    sayHello() {
        console.log('动物在叫!');
    }
}

// Dog类继承Animal类，子类，继承后子类会拥有父类所有的属性和方法
class Dog extends Animal {
    // 可以在子类中添加父类没有的属性和方法
    run() {
        console.log(`${this.name}在跑！`);
    }

    // 如果在继承的子类中添加了跟父类一样的方法，则子类的方法会覆盖父类的方法，这种叫做：重写overrides
    sayHello() {
        console.log('汪汪汪！');
    }
}

// Cat类继承Animal类，子类
class Cat extends Animal {
    sayHello() {
        console.log('喵喵喵！');
    }
}

const dog = new Dog('旺财', 5)
const cat = new Cat('秘密', 3)
console.log(dog);
dog.sayHello();
console.log(cat);
cat.sayHello();

dog.run()
```

### super（父类）

```ts
class Animal {
    name: string

    constructor(name: string) {
        this.name = name
    }

    sayHello() {
        console.log('动物在叫!');
    }
}

class Dog extends Animal {
    // 如果要在子类新添加一个属性，也必须配constructor
    age: number
    // 在这如果直接这么写的话不行，因为在子类写跟父类一样的方法会覆盖父类
        // constructor(age: number){
        //     this.age = age
        // }
    // 如果在子类写了构造函数constructor就必须调用父类的构造函数super() 
    constructor(name: string, age: number) {    // 父类中有name，所以要声明name的类型
        super(name)     // 调用父类的构造函数，父类的构造函数中有name，所以要传name
        this.age = age
    }

    sayHello() {
        // 在类的方法中，super就表示当前类的父类，调用dog.sayHello()相当于是调用了父类的sayHello()方法
        super.sayHello()
    }
}

const dog = new Dog('旺财', 3)
dog.sayHello()
console.log(dog.name, dog.age);
```

### abstract（抽象类）

```ts
// 以abstract开头的类是抽象类，跟其他的类区别不大，只是不允许用来创建实例对象(不能const an = new Animal())，抽象类就是只专门用来被继承的类
abstract class Animal {
    name: string

    constructor(name: string) {
        this.name = name
    }

    // 抽象类可以定义抽象方法(因为在父类定义的这个方法是公共的方法，等于是写死的。在父类这个方法就不定义东西，让子类自己去定义)，在方法前加abstract，抽象方法只能定义在抽象类中，子类必须对抽象方法进行重写
    abstract sayHello(): void   // : void 不设定返回值
}

class Dog extends Animal {
    sayHello() {
        console.log('汪汪汪！');
    }
}

class Cat extends Animal {
    sayHello() {
        console.log('喵喵喵！');
    }
}

const dog = new Dog('旺财')
dog.sayHello()
```

### interface（接口）

- 用来对类的结构进行限制

```ts
// 1、定义一个对象的类型声明，同变量名只能声明一个（接口也可以做这些）
type myType = {
    name: string,
    age: number
}
const obj: myType = {
    name: 'sss',
    age: 111
}
// type myType = {  不能声明多个myType
//     name: string,
//     age: number
// }


// 2、接口用来定义一个类结构，用来定义一个类中应该包含哪些属性和方法，也可以当成类型声明来使用，同变量名可以声明多个，声明的多个是合并起来的
interface myInterface {
    name: string
    age: number
}
interface myInterface {		// 可以声明多个myInterface
    gender: string
}
const obj2: myInterface = {
    name: 'sss',
    age: 111,
    gender: '男'
}

// 接口可以在定义类的时候去限制类的结构，接口中的所有的属性都不能有实际的值，只定义对象的结构(跟抽象类差不多意思，抽象类能定义普通方法和抽象方法，接口只能抽象方法)
interface myInter {
    name: string
    sayHello(): void
}
// 定义类时，可以使类去实现一个接口，就是使类满足接口定义的要求
class MyClass implements myInter {
    name: string
    constructor(name: string) {
        this.name = name
    }
    sayHello() {
        console.log('hello');
    }
}
```

### 属性的封装

- 不让直接访问和修改属性，getter和setter让属性更加的安全

```ts
class Person {
    // 现在属性是在对象中设置的，属性可以任意的被修改，这样非常的不安全
    // 可以在属性前添加修饰符
    /* 
        public _name   默认值，修饰的属性可以在任意位置访问或修改，不写就是默认值
        private _name   私有属性，只能在类的内部进行访问或修改，创建该实例去访问也不行，继承的子类也不行
        protected _name   受保护的属性，只能在当前类和当前类继承的子类中访问或修改，创建该子类的实例也不能访问，只能在该子类的内部访问
    */
    private _name: string
    private _age: number
    constructor(name: string, age: number) {
        this._name = name
        this._age = age
    }

    // 如果外部要访问该属性，定义一个可以让外部能访问到类内部的name,age属性的方法
    getName() {      // getter
        return this._name
    }
    getAge() {
        return this._age
    }
    // 定义一个可以修改属性的方法（如果只读不让修改可以不写setter方法）
    setName(value: string) {     // setter
        this._name = value
    }
    setAge(value: number) {
        value >= 0 ? this._age = value : console.log("错误");
    }


    // 上面的getter和setter是我们自己定义的，但ts中有自己的getter和setter方法，可以直接用
    get name() {
        return this._name
    }
    get age() {
        return this._age
    }
    set name(value: string) {
        this._name = value
    }
    set age(value: number) {
        value >= 0 ? this._age = value : console.log("错误");
    }
}
const per = new Person('孙悟空', 18)
// 自定义set修改
per.setName('猪八戒')
per.setAge(28)

// 自定义get修改
per.setName()
per.setAge()

// 自带的set修改
per.name = '唐僧'

// 自带的get获取
per.name

// 当我们 per.name 的时候会自动去调用 get name(){} 而不用像我们自定义的那样需要 per.setName()
console.log(per);
console.log(per.name);



// 声明类型也可以这么写：
class B {
    // 原本：
        // private _name: string
        // private _age: number
        // constructor() {
        //     this._name = name
        //     this._age = age
        // }
    // 现在简写，属性名在 constructor 里面即可，里面的 this 也不用写了
    constructor(public name: string, public age: number) {

    }
}
const b = new B('哈哈', 11)
```

### 泛型

- 在定义函数或类时，如果遇到类型不明确的就可以使用泛型（根据调用的情况的类型才知道该属性是属于什么类型）

```js
// function fn(a: number): number{		// 如果不确定a该属性是什么类型，用any会关闭类型检测，不好
//    return a
// }

// 当我们要类型检测，但又不确定该属性是什么类型，用泛型
function fn<T>(a: T): T{	// 函数执行调用的时候T才会确定是属于什么类型
    return a
}
fn(10)		// 自动检测传入的值的类型，从而指定T泛型的类型
fn<string>('hello')		// 手动指定T泛型为string

// 也可以指定多个泛型
function fn2<T, K>(a: T, b: K): T{
    return a
}
fn2<number, string>(123, "hello")


// 给泛型一个接口
interface Inter{
    length: number
}
function fn3<T extends Inter>(a: T): number{	// 表示这个泛型T继承自Inter接口(子类)
    return a.length
}
fn3('123')

// 给类指定一个泛型
class MyClass<T>{
    name: T
    constructor(name: T){
        this.name = name
    }
}
const mc = new MyClass<string>('孙悟空')
```

