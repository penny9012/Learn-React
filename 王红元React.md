# React

## 初试React

我们先使用原生的 `JavaScript` 写编写一个功能

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <h2 class="title"></h2>
  <button class="btn">改变文本</button>

  <script>
    let message = "Hello World"

    const titleEl = document.getElementsByClassName('title')[0];
    titleEl.innerHTML = message

    const btnEl = document.getElementsByClassName('btn')[0]
    btnEl.addEventListener('click',() => {
      message = "Hello React"
      titleEl.innerHTML = message
    })
  </script>
</body>
</html>
```

使用 `React` 来开发上述的这个功能

### React开发依赖

- **react**：包含react所必须的核心代码
  - 这个包包含了react和react-native所拥有的核心代码
- **react-dom**：react渲染在不同平台所需要的核心代码
  - web端：react-dom 会将 `jsx` 最终渲染成真实的DOM，显示在浏览器种
  - native端：react-dom 会将 `jsx` 最终渲染成原生的控件（例如：Android的Button，iOS种的UIButton）
- **babel**：将 `jsx` 转换成 `React` 代码的工具
  - 语法转换，将ES6转成大多数浏览器都支持 ES 的语法
  - 我们直接编写 `jsx` 的语法，babel帮助我们转换成 `React.createElement`

### CDN

```js
<script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
```

源码：packages > react-dom >  src > client > ReactDOM.js

ReactDOM本质上是

```js
export {
  createPortal,
  batchedUpdates as unstable_batchedUpdates,
  flushSync,
  Internals as __SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED,
  ReactVersion as version,
  // Disabled behind disableLegacyReactDOMAPIs
  findDOMNode,
  hydrate,
  render,	//
  unmountComponentAtNode,
  // exposeConcurrentModeAPIs
  createRoot,
  createBlockingRoot,
  discreteUpdates as unstable_discreteUpdates,
  flushDiscreteUpdates as unstable_flushDiscreteUpdates,
  flushControlled as unstable_flushControlled,
  scheduleHydration as unstable_scheduleHydration,
  // Disabled behind disableUnstableRenderSubtreeIntoContainer
  renderSubtreeIntoContainer as unstable_renderSubtreeIntoContainer,
  // Disabled behind disableUnstableCreatePortal
  // Temporary alias since we already shipped React 16 RC with it.
  // TODO: remove in React 17.
  unstable_createPortal,
};

```

ReactDOM.render('渲染的内容','挂载的对象','回调函数') 这里要求我们传入三个参数，第三个参数一般不传

```js
export function render(
  element: React$Element<any>,
  container: Container,
  callback: ?Function,
) {
  invariant(
    isValidContainer(container),
    'Target container is not a DOM element.',
  );
  if (__DEV__) {
    const isModernRoot =
      isContainerMarkedAsRoot(container) &&
      container._reactRootContainer === undefined;
    if (isModernRoot) {
      console.error(
        'You are calling ReactDOM.render() on a container that was previously ' +
          'passed to ReactDOM.createRoot(). This is not supported. ' +
          'Did you mean to call root.render(element)?',
      );
    }
  }
  return legacyRenderSubtreeIntoContainer(
    null,
    element,
    container,
    false,
    callback,
  );
}
```

初试React

```js
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
    <div id="app">app</div>
    <!-- CDN 引入依赖 -->
    <!-- <script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
    <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script> -->

    <script src="../lib/react.development.js"></script>
    <script src="../lib/react-dom.development.js"></script>
    <script src="../lib/babel.min.js"></script>

    <!-- 注意：使用jsx，并且希望script中的jsx的代码被解析，必须在script标签中添加一个属性 -->
    <script type="text/babel">
      let message = "Hello World"
      ReactDOM.render(<h2>Hello World</h2>,document.getElementById("app"))
    </script>
</body>
</html>
```

接下来我们来用React来实现上述的功能

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
    <div id="app">app</div>
    <!-- CDN 引入依赖 -->

    <script src="../lib/react.development.js"></script>
    <script src="../lib/react-dom.development.js"></script>
    <script src="../lib/babel.min.js"></script>

    <!-- 注意：使用jsx，并且希望script中的jsx的代码被解析，必须在script标签中添加一个属性 -->
    <!-- jsx：多个标签最外层只能有一个标签 -->
    <script type="text/babel">
      let message = "Hello World"

      function btnClick() {
        message = "Hello React"
        render(0)
      }

      function render() {
        ReactDOM.render(
          <div>
            <h2>{message}</h2>  
            <button onClick={btnClick}>改变文本</button>
          </div>,
          document.getElementById("app")
        )
      }

      render()
      
    </script>
</body>
</html>
```

这样的写法非常的混乱，我们使用组件化来实现一次   封装组件就必须继承 `React.Component`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
    <div id="app">app</div>
    <!-- CDN 引入依赖 -->

    <script src="../lib/react.development.js"></script>
    <script src="../lib/react-dom.development.js"></script>
    <script src="../lib/babel.min.js"></script>

    <!-- 注意：使用jsx，并且希望script中的jsx的代码被解析，必须在script标签中添加一个属性 -->
    <!-- jsx：多个标签最外层只能有一个标签 -->
    <script type="text/babel">
      let message = "Hello World"

      // 封装App组件
      class App extends React.Component {

        constructor() {
          super();  // 初始化父类的实例
          // this.message = "Hello World"
          this.state = {
            message:"Hello World"
          }
        }

        render() {
          return (
            <div>
              <h2>{this.state.message}</h2>  
              <button onClick={this.btnClick.bind(this)}>改变文本</button>
            </div>
          )
        }

        btnClick() {
          // btnClick.apply(undefined)
          // this.message = "Hello React"
          // console.log(this.message);
          this.setState({
            message:"Hello React"
          })
        }
      }

      ReactDOM.render(<App/>,document.getElementById("app"))
    </script>
</body>
</html>
```

### for循环一个列表

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>

  <div id="app"></div>

  <script src="../lib/react.development.js"></script>
  <script src="../lib/react-dom.development.js"></script>
  <script src="../lib/babel.min.js"></script>

  <script type="text/babel">

    class App extends React.Component {
      constructor() {
        super();
        this.state = {
          message:"Hello World",
          movies:["信条","我和我的团长","1992","漫威"]
        }
      }
      
      render() {
        const arr = []
        for(let movie of this.state.movies) {
          arr.push(<li key={movie}>{movie}</li>)
        }

        return (
          <div>
            <h2>aasdsad</h2>  
            <ul>
              {arr}
            </ul>

            <h2>电影列表</h2>  
            <ul>
              {this.state.movies.map(item => {
                return <li key={item}>{item}</li>
              })}
            </ul>
        </div>
        )
      }
    }

    ReactDOM.render(<App/>,document.getElementById("app"))
  </script>
</body>
</html>
```

### 计数器

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>

  <div id="app"></div>

  <script src="../lib/react.development.js"></script>
  <script src="../lib/react-dom.development.js"></script>
  <script src="../lib/babel.min.js"></script>

  <!-- React代码编写 -->
  <script type="text/babel">
    class App extends React.Component{
      constructor(){
        super()

        this.state = {
          counter:0
        }
      }

      render() {
        return(
          <div>
            <h2>当前计数：{this.state.counter}</h2>
            <button onClick={this.increment.bind(this)}>+1</button>
            <button onClick={this.decrement.bind(this)}>-1</button>
          </div>
        )
      }

      increment() {
        // React.Component 里面有 setState实现的函数
        this.setState({
          counter: this.state.counter+1
        })
      }

      decrement() {
        this.setState({
          counter: this.state.counter-1
        })
      }
    }
    
    ReactDOM.render(<App/>, document.getElementById("app"))
  </script>
</body>
</html>
```



## 语法补充 

### class类的定义

#### 在ES5中如何定义类

```js
function Person (name,age) {
	this.name = name;
    this.age = age
}

var p = new Person('penny',16)

//  在类当中定义函数，我们一般都会写成原型上
Person.prototype.running = function() {
	console.log(this.name,this.age,'running...')
    // this指向的是 p , 在执行的时候会有一次隐式的绑定
}

console.log(p.name,p.age)	// penny,16
p.running() //  penny 16 runinng...
```

#### 在ES6当中如何定义类

```js
class Person {
	constructor(name,age){	// 构造方法 在 new的时候就会创建此方法 可以在这里接手参数
        this.name = name;
        this.age = age;
    }
    
    // 定义方法 也是放在原型上的
    running(){
        console.log(this.name,this.age,'running...')
    }
}

const p = new Person('penny',16);	// 创建类
console.log(p.name,p.age);
p.running();
```

### JavaScript中类的继承

#### 面向对象的三大特性

- 封装 \ 继承 \ 多态
  - 继承：较少重复的代码 多态是前提

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <script>
    class Person {
      constructor(name,age) {
        this.name = name;
        this.age = age
      }

      running() {
        console.log('running...');
      }
    }

    // class Student {
    //   constructor(name,age,sno){
    //     this.name = name;
    //     this.age = age;
    //     this.sno = sno
    //   }
    //   running() {
    //     console.log('running...');
    //   }
    // }
    // 使用继承进行改造

    class Student extends Person{
      constructor(name,age,sno){
        // 子类中是必须初始化父类对象
        super(name,age,sno)
        this.sno = sno;
      }
      // 继承的时候会自动继承方法
    }

    const stu = new Student('penny',18,110)
    console.log(stu.name,stu,age,stu,sno);	// penny 18 110 
    stu.running()	// running...
    

    class Teacher {
      constructor (name,age,subject){
        this.name = name;
        this.age = age;
        this.subject = subject;
      }
      running() {
        console.log('running...');
      }
    }
  </script>
</body>
</html>
```

### map函数

- ​    map('回调函数','给前面的回调函数绑定this')  
  - 第一个参数:数组里有几个元素就会回调几次、
  - 第二个参数:给前面的回调函数绑定this

```js
const arr = ['asd','zxc','123','qwe']  

const newarr = arr.map((item,index,array) => {
    /**
       * 回调函数有三个参数
       *  参数一：执行时的对应元素
       *  参数二：对应的下标值
       *  参数三：完整的数组对象
      */
    return item + '1111'
})

```

### reduce函数

```js
reduce((previousValue,item,index,arr) => {},previousValue(上一次返回return的值))
```

## 认识JSX

```jsx
const element = <h2>Hello JSX</h2>
```

JSX是一种JavaScript的语法扩展

JSX的顶层只能有一个根元素，我们很多时候会在外层包裹一个div

### 注释

```jsx
{/* 我是一段注释 */}
```

### JSX嵌入变量

- 变量为Number，String,Array类型时，可以直接显示
- 变量为null，undefined，Boolean类型时，内容为空
  - 想要呈现，需要转成字符串
  - toString()，空接字符串，String()

```jsx
  <script type="text/babel">
    class App extends React.Component{
      constructor(){
        super()
        this.state = {
          // 在{}中可以正常显示的内容
          name:"penny", // String
          age:18, // Number
          names:['a','b','c'], // Array

          // 在{}中不能显示 （忽略）
          test1:null,
          test2:undefined,
          test3:true,
          flag:true
        }
      }

      render() {
        return(
          <div>
            <h2>{this.state.name}</h2>
            <h2>{this.state.age}</h2>
            <h2>{this.state.names}</h2>

            <h2>{this.state.test1}</h2>
            <h2>{this.state.test1 + ""}</h2>
            <h2>{this.state.test2}</h2>
            <h2>{this.state.test3}</h2>
            <h2>{this.state.test3.toString()}</h2>

            {/*
              boolean 想呈现 可以调用 toString()
              undefined null ： String(null)
            */}

            {/* 为什么要设计成这样 因为我们在使用三目运算符的时候并不希望将null或者undefined呈现给用户 */}
            <h2>{this.state.flag?"yes":null}</h2>
          </div>
        )
      }
    }
    
    ReactDOM.render(<App />, document.getElementById("app"))
  </script>
```

### JSX  v-show

```react
class App extends React.Component {
    constructor() {
        super()

        this.state = {
            isLogin:true
        }
    }

    render() {
        const {isLogin}  = this.state
        const titleValue = isLogin?"block":"none"
        return (
          <div>
              <h2 style={{display:titleValue}}>v-show</h2>
              <button onClick={e => this.loginClick()}>
                  {isLogin?"退出":"登录"}
              </button>
          </div>
        )
    }

    loginClick(e){
        this.setState({
            isLogin:!this.state.isLogin
        })
    }
}
ReactDOM.render(<App/>,document.getElementById("app"))
```

### JSX 绑定事件

this的绑定有三种方法

1. 在 `constructor` 里直接定时可以减少代码的调用
2. 在定义函数的时候直接使用箭头函数让this指向自动往上级查找
3. 在标签里的内联样式直接写箭头函数改变它的 this 指向 

```react
class App extends React.Component {
	constructor(){
		super()

        this.state = {
            message:"hello"
        }
        // 这样写能够减少重复的代码调用
        this.btnClick = this.btnClick.bind(this)
	}

	render() {
		return (
            <div>
            {/* 方案一：bind绑定this（显示绑定） */}
            <button onClick={this.btnClick.bind(this)}>aaaaa</button>
            <button onClick={this.btnClick}>bbbbb</button>

            {/* 方案二：定义函数时，使用箭头函数 */}
            <button onClick={this.increment}>ccccc</button>

            {/* 方案三：直接传入一个箭头函数，在箭头函数中调用需要执行的函数 */}
            <button onClick={()=>{this.decrement()}}>ddddd</button>
            </div>
		)
	}

    btnClick() {
    	console.log(this.state.message)
    }

    // 方案二：
    increment = () => {
    	console.log(this.state.message)
    }

    decrement() {
    	console.log(this.state.message)
    }
}
ReactDOM.render(<App/>,document.getElementById("app"))
```

### JSX 绑定属性

```react
function getSizeImage(imgUrl,size){
    return imgUrl + `?param=${size}*${size}`
}
class App extends React.Component {
     constructor(){
       super()
          
       this.state = {
            title:"标题",
            img:'http://p1.music.126.net/9-B2g8mBkjhrDsMvC9kUOg==/109951165615707827.jpg',
            a:'?param=140y140',
            active:true
          }
        }

        render() {
          return (
            <div>
              <h2 title={this.state.title}>我是标题</h2>
              {/*<img src={this.state.img}/>*/}
              <img src={getSizeImage(this.state.img,140)}/>

              {/* class是关键字 如果要定义样式的话 要使用className */}
              <div className="box title"></div>
              <div className={"box title " + (this.state.active?"active":"")}>asd</div>

              {/* 绑定style */}
              <div style={{color:"red",fontSize:"50px"}}>绑定style属性</div>
            </div>
          )
     }
}
```

### JSX 传递参数

```react
class App extends React.Component {
           constructor(){
                super()
                this.state = {
                    movies:['aaa','bbb','ccc','ddd']
                }
                this.btnClick = this.btnClick.bind(this)
            }

            render(){
                return(
                    <div>
                        <button onClick={this.btnClick}>bbbb</button>
                        <ul>
                            {
                                this.state.movies.map((item,index,arr) => {
                                    return (
                                        <li onClick={e => {this.liClick(item,e)}}>
                                            {item}
                                        </li>
                                    )
                                })
                            }    
                        </ul>
                    </div>
                )
            }
            btnClick(e) {
                console.log(e)
            }
            liClick(item,event){
            	console.log(item,event)
            }
}
```

### JSX 条件渲染

```react
class App extends React.Component {
	constructor() {
		super()
		this.state = {
			isLogin:false
		}
	}
    
    render() {
        return (
        	let welcome = null;
            // 方案一：通过if判断：适用于逻辑代码非常多的情况
            if(this.state.isLogin){
                welcome = <h2>aaaa</h2> 
            }else{
                welcome = <h2>bbbb</h2> 
            }
        	return (
            	<div>
                	{welcome}
                    {/* 方案二：通过三元运算符 */}
                    <button onClick={e => this.loginClick()}>
                        {this.state.isLogin?"退出":"登录"}
                    </button>
                    {/* 逻辑与：一个条件不成立，后面的条件都不会进行判断 */}
				   <h2>{this.state.isLogin && "Hello Penny!!!"}</h2>
                    {this.state.isLogin && <h2>Hello Penny!!!</h2>}
                </div>
            )
        )
    }
    loginClick() {
        this.setState({
            isLogin:!this.state.isLogin
        })
    }
}
```

## JSX的本质

JSX只是React.createElement(component,props,...children) 函数的语法糖 

所有的 jsx 最终都会被转换成 React.createElement的函数调用

```jsx
<div>
    <div className="header">
    	<h1 title="标题">我是标题</h1>
    </div>
    <div className="content">
    	<h2 className="text_font">我是页面的内容</h2>
    	<button>按钮</button>
    	<button>+1</button>
    	<a href="https://www.baidu.com">百度一下</a>
    </div>
    <div>
    	<p id="footer">我是尾部的内容</p>
    </div>
</div>
```

使用createElement来创建，认识JSX的本质

```js
React.createElement("div", null, React.createElement("div", {
        className: "header"
      }, React.createElement("h1", {
        title: "\u6807\u9898"
      }, "\u6211\u662F\u6807\u9898")), 
      React.createElement("div", {
        className: "content"
      }, React.createElement("h2", {
        className: "text_font"
      }, "\u6211\u662F\u9875\u9762\u7684\u5185\u5BB9"), 						React.createElement("button", null, "\u6309\u94AE"), 					React.createElement("button", null, "+1"), 		     					React.createElement("a", {
        href: "https://www.baidu.com"
      }, "\u767E\u5EA6\u4E00\u4E0B")), 
      	React.createElement("div", null, 
      	React.createElement("p", {
        id: "footer"
      }, "\u6211\u662F\u5C3E\u90E8\u7684\u5185\u5BB9")))
```

### 转换过程

#### reactWeb

```shell
jsx -> createElement 函数 -> ReactElement(对象树) -> ReactDOM.render(映射到浏览器) -> 真实DOM
```

#### reactNative

```shell
jsx -> createElement 函数 -> ReactElement(对象树) -> ReactDOM.render(映射到浏览器) -> 原生控件（UIButton/Button）
```

JSX代码

```js
<div>
    <div className="header">
    	<h1 title="标题">我是标题</h1>
    </div>
    <div className="content">
    	<h2 className="text_font">我是页面的内容</h2>
    	<button>按钮</button>
    	<button>+1</button>
    	<a href="https://www.baidu.com">百度一下</a>
    </div>
    <div>
    	<p id="footer">我是尾部的内容</p>
    </div>
</div>
```

转换成 createElement 代码

```js
React.createElement("div", null, React.createElement("div", {
        className: "header"
      }, React.createElement("h1", {
        title: "\u6807\u9898"
      }, "\u6211\u662F\u6807\u9898")), 
      React.createElement("div", {
        className: "content"
      }, React.createElement("h2", {
        className: "text_font"
      }, "\u6211\u662F\u9875\u9762\u7684\u5185\u5BB9"), 						React.createElement("button", null, "\u6309\u94AE"), 					React.createElement("button", null, "+1"), 		     					React.createElement("a", {
        href: "https://www.baidu.com"
      }, "\u767E\u5EA6\u4E00\u4E0B")), 
      	React.createElement("div", null, 
      	React.createElement("p", {
        id: "footer"
      }, "\u6211\u662F\u5C3E\u90E8\u7684\u5185\u5BB9")))
```

### React.createElement

所有的 `jsx` 最终都会转换成React.createElement的函数调用

此函数需要传入三个参数

参数一：type

- 当前ReactElement的类型
- 如果是标签元素，那么就使用字符串表示 `div`
- 如果是组件元素，那么就直接使用组件的名称

参数二：config

- 所有 `jsx` 中的属性都在config中以对象的属性和值的形式存储

参数三：children

- 存放在标签中的内容

### React.createElement源码

```shell
package > react > index => src > React.js > 
```

这里会判断 传入的ReactCreate的代码在开发或者上线的环境是否需要验证

```js
const createElement = __DEV__ ? createElementWithValidation : createElementProd;
```

开发环境下的警告代码

```js
if (!validType) {
    let info = '';
    if (
      type === undefined ||
      (typeof type === 'object' &&
        type !== null &&
        Object.keys(type).length === 0)
    ) {
      info +=
        ' You likely forgot to export your component from the file ' +
        "it's defined in, or you might have mixed up default and named imports.";
    }

    const sourceInfo = getSourceInfoErrorAddendumForProps(props);
    if (sourceInfo) {
      info += sourceInfo;
    } else {
      info += getDeclarationErrorAddendum();
    }

    let typeString;
    if (type === null) {
      typeString = 'null';
    } else if (Array.isArray(type)) {
      typeString = 'array';
    } else if (type !== undefined && type.$$typeof === REACT_ELEMENT_TYPE) {
      typeString = `<${getComponentName(type.type) || 'Unknown'} />`;
      info =
        ' Did you accidentally export a JSX literal instead of a component?';
    } else {
      typeString = typeof type;
    }

    if (__DEV__) {
      console.error(
        'React.createElement: type is invalid -- expected a string (for ' +
          'built-in components) or a class/function (for composite ' +
          'components) but got: %s.%s',
        typeString,
        info,
      );
    }
  }
```

生产环境 

```js
export function createElement(type, config, children) {
  let propName;

  // Reserved names are extracted
  const props = {};	// 用来存放子标签

  let key = null;
  let ref = null;
  let self = null;
  let source = null;

  if (config != null) {
    if (hasValidRef(config)) {  // 验证属性的自身标签
      ref = config.ref;

      if (__DEV__) {
        warnIfStringRefCannotBeAutoConverted(config);
      }
    }
    if (hasValidKey(config)) {
      key = '' + config.key;
    }

    self = config.__self === undefined ? null : config.__self;
    source = config.__source === undefined ? null : config.__source;
    // Remaining properties are added to a new props object
    // // 我们所有的属性都是放在config里面的 name title className..
    for (propName in config) {
      if (
        hasOwnProperty.call(config, propName) &&
        !RESERVED_PROPS.hasOwnProperty(propName)
      ) {
        // // 最后将所有的config属性放入 props 当中
        props[propName] = config[propName];
      }
    }
  }

  // Children can be more than one argument, and those are transferred onto
  // the newly allocated props object.
  // 使用数组的argument(数组传入的所有参数)的length来判断传入的元素 然后在递归
  const childrenLength = arguments.length - 2;	// 判断子标签下面还有没有内容
  if (childrenLength === 1) {
    props.children = children;
  } else if (childrenLength > 1) {
    //  先分配个数在进行传值 效率会更高一些
    const childArray = Array(childrenLength);	// 将元素转换为数组
    for (let i = 0; i < childrenLength; i++) {
      childArray[i] = arguments[i + 2];	// 	子标签的内容都存放入数组
    }
    if (__DEV__) {
      if (Object.freeze) {
        // Object.freeze : 一个被冻结的对象再也不能被修改
        Object.freeze(childArray);
      }
    }
    props.children = childArray;  // 这里把数组追加在props当中
  }

  // Resolve default props
  if (type && type.defaultProps) {
    const defaultProps = type.defaultProps;
    for (propName in defaultProps) {
      if (props[propName] === undefined) {
        props[propName] = defaultProps[propName];
      }
    }
  }
  if (__DEV__) {
    if (key || ref) {
      const displayName =
        typeof type === 'function'
          ? type.displayName || type.name || 'Unknown'
          : type;
      if (key) {
        defineKeyPropWarningGetter(props, displayName);
      }
      if (ref) {
        defineRefPropWarningGetter(props, displayName);
      }
    }
  }
  /*
  	React利用ReactElement对象组成了一个JavaScript的对象树（DOM树 virtual dom）
  */
  return ReactElement(	
    type,
    key,
    ref,
    self,
    source,
    ReactCurrentOwner.current,
    props,
  );
}
```

virtual dom 对象树

```jsx
<div>
    <div className="header">
    	<h1 title="标题">我是标题</h1>
    </div>
    <div className="content">
    	<h2 className="text_font">我是页面的内容</h2>
    	<button>按钮</button>
    	<button>+1</button>
    	<a href="https://www.baidu.com">百度一下</a>
    </div>
    <div>
    	<p id="footer">我是尾部的内容</p>
    </div>
</div>
```

![image-20210116144918990](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210116144918990.png)

通过 ReactDOM.render 来渲染真实DOM

ReactDOM.render放虚拟DOM和真实DOM同步起来，整个过程叫协调（Reconciliation）

### 为什么要使用虚拟DOM

1. 很难追踪状态发送的改变，不方便我们对应用进行调试
2. 操作真实DOM性能较低
   1. document.createElement创建出来的就是一个非常复杂的对象
   2. DOM操作会引起浏览器的回流和重绘

**example**：频繁的操作DOM

需要追加5条数据

1. 重新遍历整个数组
2. 在原有的数据上追加五条数据（appendChild()）

document.createElement创建元素，再通过appendChild(li)渲染到DOM上，进行了多次操作

对于批量操作的，最好的方法不是一次次修改DOM，而是对批量的操作进行合并，可以通过`DocumentFragment` 进行合并操作

## 脚手架

**create-react-app** 使用node编写，基于`webpack`  

```shell
npm install create-react-app -g
```

创建项目

```powershell
create-react-app "项目名称"
// 创建成功后
yarn start
```

### 目录结构

![image-20210116162859638](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210116162859638.png)

- node_modules：依赖插件库
- public：
  - favicon.ico：页面logo
  - index.html：单页面复应用这个是入口文件
  - manifest.json：和web app配置相关
  - robots.txt：设置爬虫规则
- src：写源代码的文件
  - App.css：App组件相关的样式
  - App.js：App组件的相关代码
  - App.test：对app进行测试的
  - index.js：当前react的入口
  - reportWebVitals.js：默认帮助我们写好的注册PWA相关代码
  - setupTest.js：在测试之前做一些初始化工作
- .gitignore：GitHub的忽略文件
- package.json：项目配置文件
- README.md：项目说明
- yarn.lock：记录真实版本依赖，解决版本混乱

如果需要暴露`webpack`的配置文件，webpack不需要我们随随便便去修改

```shell
yarn eject // 有时候会报 Remove untracked files, stash or commit any changes, and try again. 是因为我们修改了相关的代码 需要提交一下
		  // 先缓存在提交git commit -m "修改"
```

## 组件化开发

React的组件相对于Vue更加的灵活和多样，按照不同的方式可以分成很多类组件（与flutter十分相似）

- 根据组件的定义方式：可以分为，函数和组件和类组件
  - 函数组件（Function Component）
  - 类组件（Class Component）
- 根据是否有状态需要维护，可以分为
  - 无状态组件（Stateless Component）
  - 有状态组件（Stateful Component）
- 主要是关注数据逻辑和UI展示的分离
  - 函数组件，无状态组件主要关注UI的展示
  - 类组件，有状态组件，容器型组件主要关注数据逻辑

### 类组件

1. 组件的名称是大写字符开头的 （在JSX当中是严格区分大小写的，在JSX当中大写开头的都是组件 ）
2. 类组件需要继承自 `React.Component`
3. 类组件必须实现 `render` 函数
4. constructor是可选的，我们通常在constructor中初始化一些数据
5. this.state 中维护的就是我们组件内部的数据
6. render() 方法是 class 组件中唯一必须实现的方法

```jsx
export default class App extends Component {
  constructor(){
    super()

    this.state = {
      name:'james'
    }
  }
  render(h) {
    return (
      <div>
        <div>components</div>
        <span>
          {this.state.name}
        </span>
      </div>
    )   
  }
}
```

### 函数式组件

1. 没有this对象（this对象）不存this绑定有没有问题等现象
2. 没有内部的状态（hooks的出现能让函数式组件来维护属于自己的状态）
3. 没有生命周期，也会被更新并挂载，但是没有生命周期函数

```jsx
export default function App () {
  return (
    <div>
      <div>function</div>
      <div>component</div>
    </div>
  )
}
```

### render函数的返回值

当 `render` 被调用时，它会检查 `this.props` 和 `this.state` 的变化并返回以下的类型

1. React元素
2. 数组或者 `fragments`
3. Portals：可以渲染子节点到不同的DOM子树中
4. 字符串或者数值类型：在DOM中会被渲染为文本节点
5. 布尔值或者null：什么都不渲染

### 组件生命周期

React生命周期主要是类的生命周期，函数式组件是没有生命周期函数的

1. `constructor` 函数

   1. 通过 `this.state` 赋值对象来初始化内部的state

   2. 为事件绑定实例（this）

      ```js
      this.function.bind(this)
      ```

2. `componentDidMount` 函数，组件已经挂载到DOM上时回调

   1. 有些操作需要依赖DOM的时候可以在这里完成
   2. 网络请求的发送
   3. 可以添加一些订阅

3. `componentDidUpdate` 函数，组件发生更新时回调，刚开始渲染时是不会执行这个函数的

   1. 组件更新后对DOM 进行操作
   2. 对比更新前后的 `props` 进行了比较

4. `componentWillUnmount` 函数，组件即将被移除时回调

   1. 在此处执行必要的清理操作（清除定时器，订阅，网络请求.....）

![image-20210116181757862](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210116181757862.png)

```react
import React, { Component } from 'react'

class OtherCpn extends Component {
 render() {
   return <h2>我是OtherCpn组件</h2>
 }
 componentWillUnmount(){
   console.log('OtherCpn 执行了 componentWillUnmount')
 }
}

export default class App extends Component {
  constructor() {
    super()
    this.state = {
      counter:0,
      isShow:true
    }
    console.log("执行了constructor方法")  
  }
  render() {
    console.log('执行了render方法')
    return (
      <div>
        Hello
        <div>
          current count: {this.state.counter}
          <button onClick={e => this.increment()}>+1</button>
          <button onClick={e => this.change()}>切换</button>
        </div>
        <hr></hr>
        {this.state.isShow && <OtherCpn/>}
      </div>
    )
  }
change(){
  this.setState({
    isShow:!this.state.isShow
  })
}
increment() {
  this.setState({
     counter:this.state.counter + 1
  })
}

componentDidUpdate(){
  console.log("执行了componentDidUpdated方法 ")
}

componentDidMount() {
  console.log('执行了componentDidMount方法')
}
}
```

不常用的生命周期

`getDerivedStateFromProps`：state的值任何时候都依赖于 `props` 时使用；当我们执行Updating时，不会执行 `constructer` ，如果我们希望`New props` 同时发生变化，我们就可以使用这个方法让我们的state永远和`new props` 保持一致，该方法返回一个对象更新`state` 

`getSnapshotBeforeUpdate` ：在React更新DOM之前回调的一个函数；可以获取DOM更新前的一些信息（滚动位置）

```js
componentDidUpdate(perviousPros,previousState,snapshot){
    /**
     * previousProps:更新之前的props
     * previousState:更新之前的State
     * snapshot:是getSnapshotBeforeUpdate 返回的参数
     */
    console.log('组件更新后');
  }
```

`shouldComponentUpdate`：SCU优化，决定我们组件 是否需要渲染

### 组件通信

#### 父子组件通信

#### 父 > 子：父组件传子组件

类组件

```react
import React, { Component } from 'react'

class ChildCpn extends Component{
  // constructor 不写也能实现数据的传递
  constructor(props){
     // 父组件传来的值在props
     super(props); // 可以直接传给父类，让父类直接进行保存
     // 
     this.props = props
  }

  render(h) {
    const {name,age} = this.props
    return (
      <div>
        <h2>son component show data</h2>
        <h2>{name}</h2>
        <h2>{age}</h2>
      </div>
    )
  }
}

export default class App extends Component {
  render() {
    return (
      <div>
        <ChildCpn name="penny" age="19"></ChildCpn>
      </div>
    )
  }
}

```

函数式组件

```react
import React, { Component } from 'react'

function ChildCpn(props) {
  const {name,age} = props;
  return (
    <div>
      <h1>FatherAndSonFunction</h1>
      <h2>{name}</h2>
      <h2>{age}</h2>
    </div>
  )
}

export default class App extends Component {
  render() {
    return (
      <div>
        <ChildCpn name="penny" age="19"></ChildCpn>
      </div>
    )
  }
}

```

#### **属性进行验证**

参数 `propTypes`

对传值的数据进行验证

prop-types 库

```react
import React, { Component } from 'react'
import propTypes from 'prop-types'

function ChildCpn(props) {
  const {name,age,names} = props;
  return (
    <div>
      <h1>FatherAndSonFunction</h1>
      <h2>{name}</h2>
      <h2>{age}</h2>
      <ul>
        {
          names.map((item,index) => {
            return <li>{item}</li>
          })
        }
      </ul>
    </div>
  )
}

// 对传入的值进行验证
ChildCpn.propTypes = {
  name:propTypes.string.isRequired,	// isRequired 表示未传直接报错
  age:propTypes.number,
  names:propTypes.array
}

// 设置默认值
ChildCpn.defaultProps = {
  name:'penny-example',
}

export default class App extends Component {
  render() {
    return (
      <div>
        <ChildCpn name="penny" age={19} names={['A','B']}></ChildCpn>
		<ChildCpn age={19} names={['A','B']}></ChildCpn>
      </div>
    )
  }
}
```

在类当中的实现方式

```react
import React, { Component } from 'react'
import propTypes from 'prop-types'

// 方式一
class child2 extends Component {

}

child2.propTypes = {
    name:propTypes.string.isRequired,	// isRequired 表示未传直接报错
}

// 方式二 es6中class field写法
class child extends Component {
  // 对传入的参数进行验证
  static propTypes = {
    name:propTypes.string
  }

  // 给参数设置默认值
  static defaultProps = {
    name:'penny-example'
  }
}
```

探讨

我们父组件传入的值会被放入到 `props` 当中，然后作为一个参数传递给我们的子组件在 `constructor` 当中获取 `props` ，放入 props当中 我们可以自己对他进行一个保存 `this.props = prosp`

然后在render函数当中就能使用它了，如果我们不在 constructor 当中去保存那么按道理来说我们在render当中获取的props是 undefined 的，但是我们如果将constructor函数删除，我们运行时还是能在render函数当中获取props，我们得去了解它渲染的原理（render）	

源码：package > react-test-render > ReactShallowRender.js

![image-20210117140351684](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210117140351684.png)

![image-20210117140458052](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210117140458052.png)

`render` 在这个函数当中创建了对象

![image-20210117141155078](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210117141155078.png)

在render的最后面调用 `_mountClassComponent` 这个函数进行赋值相关的操作

`_mountClassComponent` 在这个函数里对他们进行赋值

```js
this._instance.props = element.props;
```

#### 子组件 > 父组件 通信

```react
import React, { Component } from 'react'

class Btn extends Component {
  render(){
    const {decrement} = this.props
    return <button onClick={decrement}>-</button>
  }

}

export default class App extends Component {
  constructor(){
    super()
    this.state = {
      counter:0
    }
  }
  render() {
    return (
      <div>
        <button onClick={() => this.increment()}>+</button>
        <h2>{this.state.counter}</h2>
        <Btn decrement={() => this.decrement()}></Btn>
      </div>
    )
  }

  decrement(){
    this.setState({
      counter:this.state.counter - 1
    })
  }
  increment(){
    this.setState({
      counter:this.state.counter +1
    })
  }
}
```

#### 综合练习

![image-20210117160916155](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210117160916155.png)

组件化拆分

App.js

```react
export default class App extends Component {
  
  constructor(props){
    super(props)

      // 把不需要修改的数据 最好写在外面
      this.titles = ['主播','排行榜','我的']

    this.state = {
      // 这里最好写需要更改状态的数据
      currentTitle:"主播"
    }
  }

  render() {
    const {currentTitle} = this.state
    return (
      <div>
        <TabControl itemClick={index => this.itemClick(index)} titles={this.titles}></TabControl>
        <h2>{currentTitle}</h2>
      </div>
    )
  }

  itemClick(index) {
    this.setState({
      currentTitle : index
    })
  }
}
```

TabControl.js

```react
import React, { Component } from 'react'
import PropsTypes from 'prop-types'

export default class TabControl extends Component {

  constructor(props) {
    super(props)


    this.state = {
      // 这里最好填写 状态会发生改变的数据
      currentIndex:0
    }
  }

  render() {
    const {titles} = this.props;
    const {currentIndex} = this.state
    return (
      <div className="tabControl">
        {
          titles.map((item,index) => {
            return (
              <div 
              key={index} 
              className={"TabItem " + (index === currentIndex ? "active" : "")}
              onClick={() => this.itemClick(index,item)}
              >
                <span>
                  {item}
                </span>
              </div>
            )
          })
        }
      </div>
    )
  }

  itemClick(index,item) {
    this.setState({
      currentIndex:index
    })
    
    const {itemClick} = this.props
    itemClick(item)
  }
}

TabControl.propsTypes = {
  // 对传入的数据进行验证
  titles:PropsTypes.array.isRequired
}
```

#### 实现 Vue 当中slot效果 

我们在 `vue` 里面可以使用 `slot`，但是我们在 `React` 需要使用，我们要如何实现呢？

当我们的填充的内容只有一个时可以直接使用 `this.props.children` 

App.js

```react
import React, { Component } from 'react'
import NavBar from './NavBar'

export default class App extends Component {
  render() {
    return (
      <div>
        <NavBar 
          leftSlot={<span>aaaaa</span>}
          centerSlot={<span>bbbbb</span>}
          rightSlot={<span>ccccc</span>}
          {/*在属性里面填写我们可以直接使用props拿到*/}
        >
          {/* 在这里加入的内容会被放入NavBar里的this.props.children */}
        	<span>aaaaa</span>
            <span>bbbbb</span>
            <span>ccccc</span>
        </NavBar>
      </div>
    )
  }
}

```

NavBar.js

```react
import React, { Component } from 'react'

export default class NavBar extends Component {
  render() {
    const {leftSlot,centerSlot,rightSlot} = this.props
    return (
      <div className="nav-bar">
        // 使用this.props.children 顺序不能乱
        {/* <div className="nav-item nav-left">
          {this.props.children[0]}
        </div>
        <div className="nav-item nav-center">
          {this.props.children[1]}
        </div>
        <div className="nav-item nav-right">
          {this.props.children[2]}
        </div> */}

        <div className="nav-item nav-left">
          {leftSlot}
        </div>
        <div className="nav-item nav-center">
          {centerSlot}
        </div>
        <div className="nav-item nav-right">
          {rightSlot}
        </div>
      </div>
    )
  }
}

```

#### Cotext 跨组件之间的通信

在使用跨组件通信的时候，我们有三种方式：

- 每一个组件逐次转递
- 使用展开运算符，可以减少在传递组件里的冗余代码
- 使用context

如果不使用 Context 传递，只能用 `Props` 逐层传递数据

```react
import React,{Component} from 'react'

function Header(props) {
  return (
    <div>
      <div>name:{props.name}</div>
      <div>age:{props.age}</div>
    </div>
  )
}

function Profile(props) {
  return (
    <div>
      {/* 这样也能完成跨组件之间的通信 但是这些代码在这个组件内是用不到的 */}
      {/* <Header name={props.name} age={props.age}></Header> */}
      {/* 展开语法 ... 也能完成上述的功能 */}
      <Header {...props}></Header>
      <div>1</div>
      <div>2</div>
      <div>3</div>
      <div>4</div>
    </div>
  )
}

export default class App extends Component {
  constructor(props){
    super(props)
    this.state = {
      name:'penny',
      age:18
    }
  }
  render() {
    return (
      <div>
        {/* <Profile name={this.state.name} age={this.state.age}></Profile> */}
        {/* 使用展开运算符 */}
        <Profile {...this.state}></Profile>
      </div>
    )
  }
}
```

##### Context

React 提供了一个API

Context 提供了一种在组件之间共享此类值的方式，而不必通过组件树的逐层传递 `props` 

Context 设计目的是为了共享那些对于一个组件树而言是 “全局” 的数据

##### React.createContext

创建一个需要共享的Context对象

如果一个组件订阅了Context，那么这个组件会从离自身最近的那个匹配的 `Provider` 中读取到当前的 `context` 值；

defaultValue 是组件在顶层查找过程中没有找到对应的 `Provider` ，那么就使用默认值

```react
const MyContext = React.createContext(defaultValue)
```

##### Content.Provider

每一个Context对象都会返回一个 `Provider React` 组件，他允许使用它的组件订阅 `Context` 的变化

Provider 接收一个 value 属性，传递个使用它的组件

当Provider的value值发生了变化，它内部所有的子组件都会重新渲染

##### Class.contextType

挂载在 class 上的 `contextType` 属性会被重新赋值为一个由 `React.createContext()` 创建的 Context 对象

这能让你使用 `this.context` 来消费最近 Context 上的那个值

```react
import React, {Component} from 'react'

const UserContext = React.createContext({
  name:'occupy placeholder',
  age:18
})

class ProfileHeader extends Component {
  // 每一个类组件当中都有一个 context 属性
  render() {
    return (
      <div>
        <div>name:{this.context.name}</div>
        <div>age:{this.context.age}</div>
      </div>
    )
  }
}

ProfileHeader.contextType = UserContext

function Profile(props) {
  return (
    <div>
      {/* <Header></Header> */}
      <ProfileHeader></ProfileHeader>
      <div>1</div>
      <div>2</div>
      <div>3</div>
      <div>4</div>
    </div>
  )
}

export default class App extends Component{
  constructor(props){
    super(props)
    this.state = {
      name:'penny1111',
      age:19
    }
  }

  render() {
    return (
      <div>
        {/*在外层要包裹UserContext.Provider*/}
        <UserContext.Provider value={this.state}>
          <Profile></Profile>
        </UserContext.Provider>
      </div>
    )
  }
}
```

##### Context.Consumer

在函数当中使用Context来进行通信，需要使用到 `Context.Consumer`

- 这里，React组件也可以订阅到context的变化

```react
import React ,{Component} from 'react'

const UserContext = React.createContext({
  name:'penny',
  level:1
})

function ProfileHeader() {
  return (
    <UserContext.Consumer>
      { // 这里的value就是子组件提供的值
        value => {
          return (
            <div>
              <h2>name:{value.name}</h2>
              <h2>level:{value.level}</h2>
            </div>
          )
        }
      }
    </UserContext.Consumer>
  )
}

function Profile(props){
  return (
    <div>
      <ProfileHeader/>
    </div>
  )
}

export default class App extends Component{
  constructor(props){
    super(props)
    this.state = {
      name:'aaa',
      level:11
    }
  }
  render() {
      return (
        <UserContext.Provider value={this.state}>
          <Profile></Profile>
        </UserContext.Provider>
      )
    }
}
```

多个context需要订阅时

```react
import React ,{Component} from 'react'

const UserContext = React.createContext({
  name:'penny',
  level:1
})

const ThemeContext = React.createContext({
  color:'blue'
})

function ProfileHeader() {
  return (
    <UserContext.Consumer>
      { // 这里的value就是子组件提供的值
        value => {
          return (
            <ThemeContext.Consumer>
              {
                theme => {
                  return (
                    <div>
                      <h2>name:{value.name}</h2>
                      <h2>level:{value.level}</h2>
                      <h2>color:{theme.color}</h2>
                    </div>
                  )
                }
              }
            </ThemeContext.Consumer>
          )
        }
      }
    </UserContext.Consumer>
  )
}

function Profile(props){
  return (
    <div>
      <ProfileHeader/>
    </div>
  )
}

export default class App extends Component{
  constructor(props){
    super(props)
    this.state = {
      name:'aaa',
      level:11
    }
  }
  render() {
      return (
        <UserContext.Provider value={this.state}>
          <ThemeContext.Provider value={{color:'red'}}>
            <Profile></Profile>
          </ThemeContext.Provider>
        </UserContext.Provider>
      )
    }
}
```

这样的代码看起来太乱了，目前都是使用 **redux** 

#### 全局对象传递Event

非嵌套组件通信

依赖一个库 `yarn add events`

```react
import React, { PureComponent } from 'react'
import {EventEmitter} from 'events'
// 注册
const eventBus = new EventEmitter()
class Home extends PureComponent{
  constructor(props){
    super(props)
    this.state = {
      name:'Home'
    }
  }
  componentDidMount() {
    // 绑定事件监听
    // eventBus.addListener("sayHello",this.sayHelloListener.bind(this))
    eventBus.addListener("sayHello",(e => this.sayHelloListener(e)))
  }
  componentWillUnmount(){
    // 取消事件监听
    eventBus.removeListener("sayHello",this.sayHelloListener)
  }
  sayHelloListener(e){
    console.log(e)
    // console.log(this)
    this.setState({
      name:"asd"
    })
  }
  render() {
    return (
      <div>
        {this.state.name}
      </div>
    )
  }
}

class Profile extends PureComponent{
  render() {
    return (
      <div>
        Profile
        <button onClick={e => this.emitEvent()}>ItemClick</button>
      </div>
    )
  }
  // 发送事件
  emitEvent(){
    eventBus.emit("sayHello","Hello emit")
  }
}

export default class index extends PureComponent {
  render() {
    return (
      <div>
        <Home/>
        <Profile/>
      </div>
    )
  }
}

```

#### refs

- ref属性用于HTML元素时，使用的是原生的方法
- ref属性用于自定义class组件时，ref对象接收组件的挂载实例作为其current属性
- 不能在函数组件中使用ref属性，因为他没有实例

```react
import React, { PureComponent,createRef } from 'react'

export default class App extends PureComponent {
  constructor(props){
    super(props)
    this.MianRef = createRef()
    this.MainEl = null
  }

  render() {
    return (
      <div>
        {/* ref=字符串/对象/函数 */}
        {/* <h2 ref="main">Hello</h2> */}
        <h2 ref={this.MianRef}>Hello</h2>
        <h2 ref={arg => this.MainEl = arg}>aaaaa</h2>
        <button onClick={e => this.change()}>change</button>
      </div>
    )
  }

  change() {
    // 方式一 不推荐
    // this.refs.main.innerHTML = "welcome"

    // 方式二 推荐
    this.MianRef.current.innerHTML = "Welcome!!!"

    // 方式三 函数式
    this.MainEl.innerHTML = "three Method"
  }
}

```

在开发模式中，不建议直接操作DOM原生，在某些情况下需要对DOM进行操作时

某些时候，我们可能想要获取函数式组件种的某个DOM元素， 我们可以通过 React.forwardRef 高阶函数

### setState

React并没有实现像Vue2中的Object.defineProperty或者Vue3当中的Proxy的方式来监听数据的改变

直接修改 `state` React是不会重新渲染界面的，必须通过 `setState` 来告诉React数据已经发生了变化

`setState` 做的是浅层比较对Object类型的引用判断不了render不会发生渲染

如果我们直接使用state进行修改数据就会报警告

![image-20210311154930458](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210311154930458.png)

为什么可以直接调用setState，是因为这个方法直接挂载在原型上

![image-20210311155647121](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210311155647121.png)

#### setState异步更新

为什么要将setState设置成异步

setState设计为异步，可以显著的提升性能

- 如果每次调用setState都进行一次更新，那么意味着render函数会被频繁的调用，频繁的重新渲染
- 最好是能获取多个，然后批量进行更新

如果我们需要在setState当中获取更新完后的数据

```
// 1.this.setState(更新的state,回调函数)
// 2.在componentDidUpdate当中获取this.state 也行
```

#### setState同步更新

在setTimeout中的更新是同步的

原生DOM事件是同步的

```react
import React, { Component } from 'react'

export default class App extends Component {
  constructor(props){
    super(props)
    this.state = {
      counter:0
    }
  }
  render() {
    return (
      <div>
        <h2>current count {this.state.counter}</h2>
        <button onClick={e => this.increment()}>+1</button>
        <button id="btn">+1</button>
      </div>
    )
  }
  
  componentDidMount(){
    document.getElementById("btn").addEventListener("click",() => {
      this.setState({
        counter:"这里是id同步更新"
      })
      console.log(this.state.counter)
    })
  }

  increment() {
    // 方式一：将setState放入定时器当中
    setTimeout(() => {
      this.setState({
        counter:'在定时器中发生同步更新'
      })
      console.log(this.state.counter)
    }, 0);
  }
}
```

setState有时候是同步有时候是异步的是因为React有自己处理事件的优先级，在源码有做过判断，会根据当前上下文的情况来返回你现在是同步还是异步（是否为批量处理）

#### setState数据合并

setState更新完后返回的是一个全的对象，怎么将没有改变的数据按原来的状态进行保留

在 `getStateFromUpdate` 函数中最终的返回值是将对象进行了合并的处理

```js
return Object.assign({}, prevState, partialState);
```

本身的累加

```react
import React, { Component } from 'react'

export default class App extends Component {
  constructor(props){
    super(props)
    this.state = {
      counter:0
    }
  }
  render() {
    return (
      <div>
        <h2>current count {this.state.counter}</h2>
        <button onClick={e => this.increment()}>+1</button>
      </div>
    )
  }
  
  increment() {
    // this.setState({
    //   counter:this.state.counter+1
    // })
    // this.setState({
    //   counter:this.state.counter+1
    // })
    // this.setState({
    //   counter:this.state.counter+1
    // })
    // 这种的累加在react当中是不会产生的
    // 相当于 执行了三次getStateFromUpdate方法 是一样的结果
    // Object.assign({}, prevState, partialState);
    // Object.assign({}, {counter:0}, {counter:1});

    // 如果我们希望他进行累加 合并 传入function 
    this.setState((prevState,props) => {
      return {
        counter:prevState.counter + 1
      }
    })
    this.setState((prevState,props) => {
      return {
        counter:prevState.counter + 1
      }
    })
    this.setState((prevState,props) => {
      return {
        counter:prevState.counter + 1
      }
    })
  }
}
```

源码位置在packages > react > ReactBaseClasses.js

![image-20210311170823086](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210311170823086.png)

package > react-reconciler > src > ReactFiberClassComponent.js  classComponentUpdater方法

ReactUpdateQuenue.js 里的getStateFromUpdate方法 和 getStateFromUpdate方法

![image-20210311171422759](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210311171422759.png)

#### React更新机制

更新流程：props/state改变  > render函数重新执行 > 产生新的DOM树 > 新旧DOM树进行diff算法对比 > 计算出差异进行更新 > 更新到真实的DOM

- 当一个元素从<div>变成<span> 时，会触发一个完整的重建流程
- 当写在一棵新的树时，对应的DOM节点会被创建以及插入到DOM中，组件实例将执行 `componentWillMount()` 方法
- 当建立一棵新的树时，对应的DOM节点会被创建以及插入到DOM中，会执行`componentWillMount()` 紧接着 `componentDidMount()` 方法

```html
<div>
    <Cpn></Cpn>
</div>

<span>
    <Cpn></Cpn>
</span>
```

- 当对比两个相同的React元素时，React会保留DOM节点，仅比对及更新有改变的属性
- 组件会保持不变，React会更新改组件的props，调用`componentWillReceiveProps()`和 `componentWillUpdate()`

```html
<div title="before"/>
<div title="after"/>	// 只会更改title
```

- 对子节点进行递归，React会同时遍历两个子元素的列表，当产生差异时，生成一个mutation

```html
<div>
    <div>456</div>
    <div>789</div>
</div>
// 变成下面这种时会直接插入<div>789</div> 但是保持位置和位置的不不变
<div>
    <div>123</div>
	<div>456</div>
    <div>789</div>
</div>
```

其实要是能把<div>456</div> 和下面的 <div>456</div> 进行比较的直接位移就行了，但是默认情况下是一个一个进行比较的

#### setState不可变

```react
import React, { Component } from 'react'

export default class App extends Component {
  constructor(props){
    super(props)
    this.state = {
      py:[
        {name:'aaa',age:10},
        {name:'bbb',age:20},
        {name:'ccc',age:30}
      ]
    }
  }
  render() {
    return (
      <div>
        <h2>LIST</h2>
        <ul>
          {
            this.state.py.map((item,index) => {
              return (
                <li key={index}>{item.name}</li>
              )
            })
          }
        </ul>
        <button onClick={e => this.insert()}>add</button>
      </div>
    )
  }

  shouldComponentUpdate(newProps,newState){
    if(newState.py !== this.state.py){
      return true 
    }
    return false
  }

  insert() {
    // 第一种写法我们在添加时可能不会渲染界面
    // const newData = {name:"ddd",age:40}
    // this.state.py.push(newData)
    // this.setState({
    //   py:this.state.py
    // })

    // 在开发中我们推荐
    const newData = [...this.state.py]
    newData.push({name:"ddd",age:40})
    this.setState({
      py:newData
    })
  }
}

```

第一种方式

![image-20210311221040526](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210311221040526.png)

对比的是引用类型（引用地址）所以在shouldComponentUpdate当中不会发送重新渲染

第二种方式

![image-20210311221759568](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210311221759568.png)

### 性能优化

#### keys的作用

- 当在最后的位置插入值，这种情况key值的意义并不大
- 在前面插入数据，没有key的情况下，所有的元素都需要进行修改
- 当写入key值时，React使用key来匹配原有树上的子元素以及最新树上的子元素
- key值应该是唯一的，不要使用随机数（在下次render的时候会产生一个新的数字，在进行新旧DOM匹配的时候就匹配不上了）

```js
insert() {
    // this.setState({
    //   // 这样子添加的话 keyd作用就不大
    //   name:[...this.state.names,"angular"]
    // })

    this.setState({
      // 这样子插入 匹配时所有的元素都会重新渲染
      names:[...this.state.names,"angular"]
    })
  }
```

#### 组件嵌套的render调用

多个组件嵌套在一起，数据发送改变时，所有的界面都会重现渲染，这样非常的消耗性能

我们可以通过`shouldComponentUpdate(nextProps,nextState)` 生命周期函数来控制 `render` 函数的执行

也可以使用 `PureComponent` 组件，能够实现有数据更新的组件才调用render函数，但是无法解决函数式组件的重新渲染

`shouldComponentUpdate` 

源码： `react-reconcoiler > ReactFiberClassComponent.js`

```js
function checkShouldComponentUpdate(
  workInProgress,
  ctor,
  oldProps,
  newProps,
  oldState,
  newState,
  nextContext,
) {
  const instance = workInProgress.stateNode;
  // 在这里判断实例对象shouldComponentUpdate
  if (typeof instance.shouldComponentUpdate === 'function') {
    //startPhaseTimer(workInProgress, 'shouldComponentUpdate');//记录开始解析的时间
    const shouldUpdate = instance.shouldComponentUpdate(  //这里的返回值是Boolean来绝定我们要不要渲染render
      newProps,
      newState,
      nextContext,
    );
    //stopPhaseTimer();// 记录结束解析的时间
    return shouldUpdate;
  }
  // 判断是否需要重新渲染
  if (ctor.prototype && ctor.prototype.isPureReactComponent) {
    return (
      // 浅层比较
      !shallowEqual(oldProps, newProps) || !shallowEqual(oldState, newState)
    );
  }
  // 默认返回的是true 会更新
  return true;
}
```

#### PureComponent

源码位置： package > react > ReactBaseClasses.js

```js
pureComponentPrototype.isPureReactComponent = true;
```

shallowEqual：浅层比较

```js
function shallowEqual(objA: mixed, objB: mixed): boolean {
  if (is(objA, objB)) {
    // 判断是否为同一个对象
    return true;
  }

  if (
    typeof objA !== 'object' ||
    objA === null ||
    typeof objB !== 'object' ||
    objB === null
  ) {
    return false;
  }

  const keysA = Object.keys(objA);
  const keysB = Object.keys(objB);

  if (keysA.length !== keysB.length) {
    return false;
  }

  // Test for A's keys different from B.
  for (let i = 0; i < keysA.length; i++) {
    if (
      !hasOwnProperty.call(objB, keysA[i]) ||
      !is(objA[keysA[i]], objB[keysA[i]])
    ) {
      return false;
    }
  }

  return true;
}
```

#### memo

在updateMemoComponent当中有对memo传入的值作比较

![image-20210311211457847](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210311211457847.png)

#### 受控组件和非受控组件

```html
<form>
    <input type="text" name="data"/>
    <input type="submit" value="submit"/>
</form>
```

##### 受控组件

React当中，HTML表单的提交方式和普通的DOM元素不一样，表单元素通常会保存一些内部的state

通常情况下会使用JavaScript函数来方便的处理表单提交，同时可以访问提交的数据

这种效果的标准方式是 “受控组件”

```react
import React, { PureComponent } from 'react'

export default class App extends PureComponent {
  constructor(props){
    super(props)
    this.state = {
      username:"",
      Multiple:"two"
    }
  }
  render() {
    return (
      <div>
        <form onSubmit={e => this.submit(e)}>
          <label htmlFor="username">
            {/* 受控组件里的input数据永远要和state里面的数据保持一致 单向数据流 这就是受控组件*/}
            <input type="text" id="username" onChange={e => this.handleChange(e)} value={this.state.username}/>

            {/* <input type="checkbox" onChange={e => this.handleCheckout(e)}/>
            <input type="radio" onChange={e => this.handleRadio(e)}/> */}

            <select name="Multiple" onChange={e => this.selection(e)} value={this.state.Multiple}>
              <option value="one">类型一</option>
              <option value="two">类型二</option>
              <option value="three">类型三</option>
            </select>
          </label>
          <input type="submit" value="提交"/>
        </form>
      </div>
    )
  }
  submit(e){
    // 取消默认行为 e当中有原生对象上所有的方法
    e.preventDefault();
    console.log(this.state.username)
    console.log(this.state.Multiple)
  }
  handleChange(e){
    this.setState({
      username:e.target.value
    })
  }
  selection(e){
    this.setState({
      Multiple:e.target.value
    },() => {
      console.log(this.state.Multiple)
    })
  }
  handleCheckout(e){
     console.log(e.target.checked)
   }
   handleRadio(e){
     console.log(e.target.checked)
   }
}
```

##### 非受控组件(不推荐)

React推荐多数情况下使用受控组件来处理表单数据

受控组件当中表单数据是由React组件来控制

非受控组件表单数据是由DOM节点来处理

```react
import React, { PureComponent,createRef } from 'react'

export default class App extends PureComponent {
  constructor(props){
    super(props)
    this.usernameRef = createRef()
  }
  render() {
    return (
      <div>
        <form onSubmit={e => this.submit(e)}>
          <label htmlFor="username">
            <input type="text" id="username" ref={this.usernameRef}></input>
            <input type="submit" value="提交"></input>
          </label>
        </form>
      </div>
    )
  }

  submit(e) {
    e.preventDefault();
    console.log(this.usernameRef.current.value) // React不推荐我们直接操作DOM 节点
  }
}
```

### 高阶组件

高阶函数：接收一个或多个函数作为输入，输出一个函数

高阶组件： Higher-Order-Components : HOC 高阶组件是参数为组件，返回值为新组件的函数

```react
import React, { PureComponent } from 'react'

class App extends PureComponent {
  render() {
    return (
      <div>
        App
      </div>
    )
  }
}

// 可以自定义名字
App.displayName = "OtherName"

// 高阶函数
function enhanceComponent(WrapperComponent) { //  接收组件
  // 返回一个新的组件
  return class NewComponent extends PureComponent{
    render(){
      return <WrapperComponent/>
    }
  }
}

const EnhanceComponent = enhanceComponent(App)

export default EnhanceComponent;
```

高阶组件是基于React的组合特性而形成的设计模式

缺陷：大量使用高阶组件会让调试变得很困难，HOC可以劫持props。在不遵守约定的情况下可能造成冲突

Hooks的出现解决了大多数问题，例如this的指向，HOC的嵌套复杂度... 

#### Props应用

如果我们需要给每一个组件添加一个属性时

```react
import React, { PureComponent } from 'react'

// 类似于 WithRouter
function EnhanceRegion(WrapperComponent){
  return props => {
    return <WrapperComponent {...props} region="china"/>
  }
}

class Home extends PureComponent{
  render() {
    return <h2>Home{this.props.name} region:{this.props.region}</h2> 
  }
}

class About extends PureComponent{
  render() {
    return <h2>About{this.props.name}</h2> 
  }
}

const EnhanceHome = EnhanceRegion(Home)

class App extends PureComponent {
  render() {
    return (
      <div>
        App
        <EnhanceHome name="Home"/>
        <About name="About"/>
      </div>
    )
  }
}

export default App
```

可以将Context同时共享给多个组件

```react
import React, { createContext, PureComponent } from 'react'


// 类似于 WithRouter
// 改进做法
function WithUserInfo(WrapperComponent){
  return props => {
    return (
      <UserContext.Consumer>
        {
          user => {          
           return <WrapperComponent {...user} {...props}/>
          }
        }
      </UserContext.Consumer>
    )
  }
}

// Context 
const UserContext = createContext({
  name:"baby",
  region:"china"
})

class Home extends PureComponent{
  render() {
    // 默认做法
    // return (
    //   <UserContext.Consumer>
    //     {
    //       user => {          
    //         return <h2>{`Home ${user.name} region:${user.region}`}</h2> 
    //       }
    //     }
    //   </UserContext.Consumer>
    // )
    return <h2>{`Home ${this.props.name} region:${this.props.region}`}</h2> 
  }
}

class About extends PureComponent{
  render() {
    return <h2>{`About ${this.props.name} region:${this.props.region}`}</h2> 
  }
}

const EnhanceHome = WithUserInfo(Home)
const EnhanceAbout = WithUserInfo(About)

class App extends PureComponent {
  render() {
    return (
      <div>
        App
        <UserContext.Provider value={{name:"satisfaction" ,region:"America"}}>
          <EnhanceHome/>
          <EnhanceAbout/>
        </UserContext.Provider>
      </div>
    )
  }
}

export default App
```

#### 判断鉴权

登录鉴权操作

```react
import React, { PureComponent } from 'react'

function withAuth(WrapperComponent) {
  return props => {
    const {isLogin} = props
    if(isLogin){
      return <WrapperComponent {...props}/>
    }else{
      return <LoginPage></LoginPage>
    }
  }
}

class LoginPage extends PureComponent{
  render() {
    return <h2>LoginPage</h2>
  }
}

class CartPage extends PureComponent{
  render() {
    return <h2>CartPage</h2>
  }
}

const NewCartPage = withAuth(CartPage)

export default class App extends PureComponent {
  render() {
    return (
      <div>
        <NewCartPage isLogin={true}/>
      </div>
    )
  }
}
```

#### 记录组件渲染时间

```react
import React, { PureComponent } from 'react'

// 改进的写法
function WithRenderTimer(WrapperComponent){
  return class extends PureComponent{
    UNSAFE_componentWillMount(){
      // componentWillMount 这个生命周期函数不推荐使用 
      this.beginTime = Date.now()
    }
    render() {
      return <WrapperComponent {...this.props}/>
    } 
    componentDidMount(){
      this.endTime = Date.now()
      const interval = this.endTime - this.beginTime
      console.log(WrapperComponent.name + interval)
    }
  }
}

// 未改进前的写法
// 查看组件渲染时间
class Home extends PureComponent{
  // 即将渲染时获取一个很时间 beginTime
  componentWillMount(){
    // componentWillMount 这个生命周期函数不推荐使用 
    this.beginTime = Date.now()
  }
  render(){
    return <h2>Home</h2>
  }

  // 渲染完成时获取 endTime
  componentDidMount(){
    this.endTime = Date.now()
    const interval = this.endTime - this.beginTime
    console.log(interval)
  }
}

class About extends PureComponent{
  render(){
    return <h2>About</h2>
  }
}

const TimerAbout = WithRenderTimer(About)

export default class App extends PureComponent {
  render() {
    return (
      <div>
        App
        {/* <Home/> */}
        <TimerAbout/>
      </div>
    )
  }
}
```

​	在早期的时候React有提供组件之间的一种复用方式是Mixin，目前已经不建议使用了

#### refs的转发

因为在函数式组件没有实例，所以不能获取对应的组件对象，可以通过forwardRef高阶函数来获取

```react
import React, { PureComponent,createRef,forwardRef} from 'react'

class Home extends PureComponent{
  render() {
    return <h1>Home</h1>
  }
}

function Profile(params) {
  return <div>Porfile</div>  
 }

// 高阶组件
const EnhanceProfile = forwardRef((props,ref) => {
  console.log(props.name)
  return <p ref={ref}>EnhanceProfile</p>
})

export default class App extends PureComponent {
  constructor(props){
    super(props)

    this.homeRef = createRef()
    this.aboutRef = createRef()
    this.profileRef = createRef()
  } 

  render() {
    return (
      <div>
        <h2 ref={this.homeRef}>Hello World</h2>
        <Home ref={this.aboutRef}/>
        {/* <Profile ref={this.profileRef}/> */}
        <EnhanceProfile ref={this.profileRef}/>
        <button onClick={e => this.itemClick(e)}>click</button>
      </div>
    )
  }
  itemClick(e){
    console.log(this.homeRef.current)
    console.log(this.aboutRef.current)
    console.log(this.profileRef .current)
  }
}
```

### Portals

有时候我们希望渲染的组件能独立于当前挂载到的DOM元素种（默认都是挂载到id为root的DOM元素）

Portal 提供了一个将子节点渲染到存在于父组件以外的DOM节点的方式

- 第一个参数是任何可渲染的React子元素
- 第二个参数是一个DOM元素

Model组件

index.html 需要写入一个div

```html
<div id="model"></div>
```

```react
import React, { PureComponent,createRef,forwardRef} from 'react'
import ReactDOM from 'react-dom'

class Model extends PureComponent{
  render(){
    return ReactDOM.createPortal(
      this.props.children,
      document.getElementById("model")  
    )
  }
}

class Home extends PureComponent{
  render() {
    return (
      <div>
        <h1>Home</h1>
        <Model>
          <h2>Title</h2>
        </Model>
      </div>
    )
  }
}

// 高阶组件

export default class App extends PureComponent {
  constructor(props){
    super(props)
  } 

  render() {
    return (
      <div>
        <Home ref={this.aboutRef}/>
      </div>
    )
  }
}
```

### fragment占位符

类似于小程序的block标签

```react
import React, { PureComponent , Fragment} from 'react'

export default class App extends PureComponent {
  constructor(props){
    super(props)
    this.state={
      counter:0
    }
  }
  render() {
    return (
      // <Fragment>
      //   <h2>current counter {this.state.counter}</h2>
      //   <button onClick={e => this.increment()}>btn</button>
      // </Fragment>

      // Fragment 短语法 不能添加任何的属性
      <>
        <h2>current counter {this.state.counter}</h2>
        <button onClick={e => this.increment()}>btn</button>
      </>
    )
  }
  increment(){
    // this.state.counter+= 1
    // this.setState({
    //   counter:this.state.counter
    // })
    this.setState({
      counter:this.state.counter+1
    })
  }
}
```

### 严格模式

StrictMode是一个用来突出显示应用程序中潜在问题的工具

为后代的元素触发额外的检查和警告，严格模式尽在开发模式下运行

- 识别不安全的生命周期
- 使用过时的ref API
- 检查意外的副作用
  - 组件的constructor会被调用两次

```react
import React, { PureComponent,StrictMode } from 'react'

class Home extends PureComponent{
  UNSAFE_componentWillMount(){
    console.log('a')
  }
  constructor(props){
    super(props)
    console.log("Home constructor")
  }
  render(){
    return (
      <div ref="aaa">
        Home
      </div>
    )
  }
}

class About extends PureComponent{
  constructor(props){
    super(props)
    console.log("About constructor")
  }
  render(){
    return (
      <div ref="bbb">
        About
      </div>
    )
  }
}

export default class App extends PureComponent {
  constructor(props){
    super(props)
  }
  render() {
    return (
      <div>
        <StrictMode>
          <Home/>
        </StrictMode>
        <About/>
      </div>
    )
  }
}
```

## 样式CSS

#### 内联样式

优点：

- 内联样式，样式之间不会有冲突
- 可以动态获取当前state中的状态

缺点

- 写法上都需要使用驼峰标识
- 某些样式没有提示
- 大量的样式没有提示
- 某些样式无法编写（伪元素）

#### CSS modules

css modules并不是React特有的解决方案，而是使用了类似于webpack配置的环境下都可以使用的

解决了局部作用域的问题

```react
import React, { PureComponent } from 'react'
import Cart from '../cart'
import style from  './style.module.css'
export default class App extends PureComponent {
  render() {
    return (
      <div>
        <h2 className={style.title}>App-title</h2>
        <p>App-subtitle</p>
        <Cart/>
      </div>
    )
  }
}
```

style.module.css

```css
.title{
	color:green
}
```

就是不方便去修改某些样式，还需要使用内联样式来修改

#### CSS in JS

“CSS-in-JS” 是指一种模式，其中CSS由JavaScript生成而不是在外部文件定义

由类似于CSS预处理器一样的样式嵌套，函数定义，逻辑复用，动态修改...

- styled-components
- emotion
- glamorous

```
yarn add styled-components
```

在 Vscode 上添加CSS代码提示工具

![image-20210313105923803](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210313105923803.png)

```react
import React, { PureComponent } from 'react'
import styled,{ThemeProvider} from 'styled-components'
import About from '../about'
import Cart from '../cart'


const HButton = styled.button`
  padding:10px 20px;
  color:red;
`

// const PrimaryButton = styled.Button`
// /* 这样写代码重复 */
//   padding:10px 20px;
//   color:red;
//   background-color: red;
// `
// 继承HButton的属性
const PrimaryButton = styled(HButton)`
  background-color: green;
`

/**
 * ThemeProvider 主题
 */

export default class App extends PureComponent {
  render() {
    return (
      <ThemeProvider theme={{themeColor:"yellow"}}>
        App
        <About/>
        <Cart/>
        <HButton>aaaa</HButton>
        <PrimaryButton>bbbb</PrimaryButton>
      </ThemeProvider>
    )
  }
}
```

About

```react
import styled,{ThemeProvider} from 'styled-components'
export const AboutHome = styled.div`
  font-size:50px;
  color:red;
  background-color:${props => props.theme.themeColor};	// 拿出主题颜色
`
```

#### ClassName的设置

React原生设置动态class的方法，设置的方法比较麻烦

```react
constructor(props){
	super(props)
	this.state = {
		isActive:true
	}
}
render() {
    const {isActive} = this.state
    return (
      <div>
        <HInput color={this.state.color}/>
        <h3>Cart</h3>
        <div className={"title " + (isActive?"active":"")}>bbbb</div>
        <div className={["title", (isActive?"active":"")].join(" ")}>cccc</div>
      </div>
    )
  }
```

我们可以使用一个第三方的库 `classnames`

```
yarn add classnames
```

classnames 不会识别为false的参数

```react
render() {
    const {isActive} = this.state
    const isBar = false;
    const err = "error";
    const n = null;
    const u = undefined;
    
    return (
      <div>
        <HInput color={this.state.color}/>
        <h3>Cart</h3>
        {/* 原生React添加class放啊 */}
        <div className={"title " + (isActive?"active":"")}>bbbb</div>
        <div className={["title", (isActive?"active":"")].join(" ")}>cccc</div>

        {/* classnames库的使用 */}
        <div className="foo bar"></div>
        <div className={classNames("foo","bar")}></div>
        <div className={classNames({"active":isActive,"bar":isBar},u,n,err)}>classnames</div>
        <div></div>
      </div>
    )
  }
```

![image-20210313115859837](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210313115859837.png)

## AntDesign

```
npm install 
yarn add antd
// icon 矢量库
yarn add @ant-design/icons
// 使用到日期相关的组件时 需要使用一个 moment 的库
yarn add moment
```

兼容性

现代浏览器和IE11（需要polyfills）

支持服务端渲染

Electron（桌面端）

antd的 JS 代码默认支持基于ES modules 的 tree shaking ，直接i import {} from 'antd' 就会有按需加载的效果

### Craco

我们需要在 React 当中修改默认的配置可以通过 yarn run eject 来暴露出来对应的配置信息进行修改，但是这个方式对于不熟悉webpack的人来说并不友好

antd推荐的方案是（craco）

```
yarn add @craco/craco
```

然后在 package.json 当中修改配置

![image-20210313134430804](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210313134430804.png)



![image-20210313135130869](C:\Users\25798\AppData\Roaming\Typora\typora-user-images\image-20210313135130869.png)



### 自定义主题

自定义主题需要用到类似 **less-loader** 提供了 less 变量覆盖功能，我们可以引入craco-less 来帮助加载 less 样式和修改变量

```
yarn add craco-less
```

在根目录下新建一个 `craco.config.js` 

```js
const CracoLessPlugin = require('craco-less');

module.exports = {
  plugins: [
    {
      plugin: CracoLessPlugin,
      options: {
        lessLoaderOptions: {
          lessOptions: {
            modifyVars: { '@primary-color': '#1DA57A' },
            javascriptEnabled: true,
          },
        },
      },
    },
  ],
}
```

到 `index.js` 当中 要引入  `antd/dist/antd.less`   

### 配置别名

```js
const CracoLessPlugin = require('craco-less');
const path = require('path')
const resolve = dir => path.resolve(__dirname,dir)
module.exports = {
  plugins: [
    {
      plugin: CracoLessPlugin,
      options: {
        lessLoaderOptions: {
          lessOptions: {
            modifyVars: { '@primary-color': '#1DA57A' },
            javascriptEnabled: true,
          },
        },
      },
    },
  ],
  webpack:{
    // 配置别名
    alias:{
      "@":resolve("src"),
      "02":resolve("src/AntDesign")
    }
  }
}
```

