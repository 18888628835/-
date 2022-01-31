# React 是什么？

React 是开源的前端 JavaScript 库，用于构建用户界面，它主要用来构建Web和移动应用程序的视图层，特别是单页应用（single page applications）， React是由为Facebook工作的软件工程师[Jordan Walke]（https://github.com/jordwalke）创建的。 React于2011年首次部署在Facebook的News Feed上，2012年首次部署在Instagram上。



# React 主要特点有哪些？

- 考虑到真实 dom 的操作成本非常高，React 使用虚拟 dom（VirtualDOM）来替代真实 dom
- 支持服务端渲染
- 遵循单向数据流或者说是单向数据绑定
- 使用可复用/组合的 UI 组件来构建视图



# JSX是什么

是ECMAScript的类似XML的语法扩展（缩写为 **JavaScript XML**）。

基本上，它为`React.createElement（）`函数提供语法糖，用JavaScript的形式将类似HTML的模板语法展现出来。

在下面的示例中，标签`<h1>`作为JavaScript函数返回给render函数。

```jsx harmony
class App extends React.Component {
  render() {
    return(
      <div>
        <h1>{'Welcome to React world!'}</h1>
      </div>
    )
  }
}
```



# React 元素和组件之间有什么区别

React Element（元素）是一个普通的对象，它描述了您希望以DOM节点或其他组件的形式显示在屏幕上的内容。 元素可以在其props中包含其他元素。 创建一个React元素很简单。 一旦创建了元素，它就不会被改变。

React 的 **Element **的对象表现形式就类似于下面这样：

```javascript
const element = React.createElement(
  'div',
  {id: 'login-btn'},
  'Login'
)
```

 上面的`React.createElement()`函数会返回一个对象：

```javascript
{
  type: 'div',
  props: {
    children: 'Login',
    id: 'login-btn'
  }
}
```

最终会用`ReactDOM.render()`函数渲染这样的 DOM 节点

```javascript
<div id='login-btn'>Login</div>
```

**Component**组件可以通过几种不同的方式声明。 它可以是带有`render（）`方法的类。 或者，在简单情况下，可以将其定义为一个函数。 无论哪种情况，它都将props作为输入，并返回JSX树作为输出：

```javascript
const Button = ({ onLogin }) =>
  <div id={'login-btn'} onClick={onLogin}>Login</div>
```

然后 JSX 会编译成`React.createElement()`函数

```javascript
const Button = ({ onLogin }) => React.createElement(
  'div',
  { id: 'login-btn', onClick: onLogin },
  'Login'
)
```



# 如何创建 component 组件

有两种途径创建 component 组件：

函数组件：这是创建组件的最简单方法。 这些是纯JavaScript函数，接受props对象作为第一个参数并返回React元素

```javascript
function Greeting({ message }) {
  return <h1>{`Hello, ${message}`}</h1>
}
```

类组件：使用ES6类来定义组件。 上面的功能组件可以写成：

```javascript
class Greeting extends React.Component {
  render() {
    return <h1>{`Hello, ${this.props.message}`}</h1>
  }
}
```

# 什么时候在函数组件上使用类组件

如果组件需要状态或生命周期方法，则使用类组件，否则使用纯函数组件。 但是，从带有1 Hooks的React 16.8开始，您可以在纯函数组件中使用state，生命周期方法和其他功能。



# 什么是Pure Components（译者有修改）

React.PureComponent与React.Component完全相同，不同之处在于Pure Components会内置处理shouldComponentUpdate（）方法。 当props和state发生变化时，PureComponent将对props和state进行浅比较。如果 PureComponent 里有 shouldComponentUpdate 函数的话，直接使用 shouldComponentUpdate 的结果作为是否更新的依据。

 另一方面，Component不会将当前的props和state与下一个进行比较。 因此，默认情况下，每当调用shouldComponentUpdate时，Component将根据调用的结果决定是否重新渲染。

## shouldComponentUpdate 有什么用（译者加）

这个钩子在组件更新前(render 函数前)执行，它主要是用来手动设置是否要进行组件更新，我们可以根据应用场景灵活地设置是否让UI更新，以达到性能的优化.

如果返回true，就让浏览器更新UI(执行 render)

如果返回 false,就让浏览器不更新UI(不执行 render)

```javascript
class App2 extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 1 };
  }

  //   shouldComponentUpdate(nextProps, nextState) {
  //     if (this.state.count !== nextState.count) {
  //       return true;
  //     } else {
  //       return false;
  //     }
  //   }
  add = () => {
    this.setState((state, props) => {
      return { count: state.count + 0 }; //每次点击后加0，所以值是相同的
    });
  };

  render() {
    return (
      <div>
        {console.log("render了一次")}//这里表示render了
        <button onClick={this.add}>Count: {this.state.count}</button>
      </div>
    );
  }
}
```

shouldComponentUpdate接受两个参数,分别为新的 props 和新的 state,如果返回 false,那么就相当于告诉 React不需要执行 render 函数.

React 官方建议使用 PureComponent

```javascript
class CounterButton extends React.PureComponent {
  constructor(props) {
    super(props);
    this.state = {count: 1};
  }
```



# 什么是 React 的 state

组件的state是一个对象，其中包含一些可能在组件的整个生命周期中发生变化的信息。 我们应该始终尝试使state尽可能简单，并最大程度减少有状态组件的数量。

比如创建一个带有message 的用户组件

```javascript
class User extends React.Component {
  constructor(props) {
    super(props)

    this.state = {
      message: 'Welcome to React world'
    }
  }

  render() {
    return (
      <div>
        <h1>{this.state.message}</h1>
      </div>
    )
  }
}
```

<img src="https://tva1.sinaimg.cn/large/008i3skNly1gqqav8xmzfj31cu0keagj.jpg" alt="image-20210521200158933" style="zoom:50%;" />

状态类似于props，但它是私有的，并由组件完全控制。 如果不进行传递那么其他组件都无法访问它。





# React 的 props 是什么

props是组件的输入。 它们是单个值或包含一组值的对象，这些对象在创建时会使用类似于HTML标签属性的命名约定传递给组件。 它们是从父组件传递到子组件的数据。

React中props的主要目的是提供以下组件功能：

- 传递自定义数据给组件
- 触发状态改变
- 在 render 方法中使用

例如：我们创建一个带有 reactProp 属性的 React 元素

```javascript
<Element reactProp={'1'} />
```

然后，这个reactProp（或您想出的任何名称）将成为附加到React的本地props对象的属性，该对象一开始就存在于使用React库创建的所有组件上。

```
props.reactProp
```



# state 和 props 有什么区别

所有的 props 和 state 都是 js 对象。尽管它们两者都具有影响渲染输出的信息，但它们在组件方面的功能却有所不同。 props被用于数据传递给其他组件，就类似于函数参数。而state则类似于与函数中声明的变量一样在组件内进行管理。



# 为什么我们不能直接更新 state

如果直接更新 state，则不会在组件中进行渲染

```javascript
//Wrong
this.state.message = 'Hello world'
```

而是使用setState（）方法。 它用于组件state对象的更新。 状态改变时，组件做出响应并重新渲染。

```javascript
//Correct
this.setState({ message: 'Hello World' })
```





# 回调函数作为 setState 的参数，目的是什么

当组件渲染并且setState 方法被执行后，会调用回调函数，由于 setState 是异步的，因此回调函数可以用于任何后期操作。

> 建议使用生命周期方法而不是此回调函数。

```javascript
setState({ name: 'John' }, () => console.log('The name has updated and component re-rendered'))
```





# HTML的事件处理和 React 的事件处理有什么区别

以下是HTML和React事件处理之间的一些主要区别：

**1.在HTML中，事件名称通常以小写形式表示为约定**

```javascript
<button onclick='activateLasers()'>
```

React 中，事件用小驼峰命名

```javascript
<button onClick={activateLasers}>
```

**2.HTML中，你可以返回 false 来阻止默认行为**

```javascript
<a href='#' onclick='console.log("The link was clicked."); return false;' />
```

React里我们必须用`preventDefault()`

```javascript
function handleClick(event) {
  event.preventDefault()
  console.log('The link was clicked.')
}
```

**3.在HTML中，您需要通过附加（）来调用函数，而在react中，您不应该在函数（）中附加（）。(第一个例子)**





# 如何在JSX回调中绑定方法或事件处理程序？

三种方法：

1.写在类组件的 constructor 里

```javascript
class Component extends React.Component {
  constructor(props) {
    super(props)
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick() {
    // ...
  }
}
```

2.共用类方法

```javascript
handleClick = () => {
  console.log('this is:', this)
}
```

```javascript
<button onClick={this.handleClick}>
  {'Click me'}
</button>
```

3.直接写函数

```javascript
<button onClick={(event) => this.handleClick(event)}>
  {'Click me'}
</button>
```





# 如何传递参数给事件处理函数

例子1：箭头函数包围

```javascript
<button onClick={() => this.handleClick(id)} />
```

例子2：使用 bind

```javascript
<button onClick={this.handleClick.bind(this, id)} />
```

例子3：除了这两种方法，您还可以将参数传递给返回箭头函数的函数

```javascript
<button onClick={this.handleClick(id)} />
handleClick = (id) => () => {
    console.log("Hello, your ticket number is", id)
};
```





# 什么是 React 的合成事件

合成事件是跨浏览器的浏览器本地事件包装，它的 API 跟浏览器本地事件是一致的，包括`stopPropagation()`停止冒泡 和 `preventDefault()`阻止默认行为。这些事件在浏览器中的工作方式相同。





# 什么是内联条件语句表达式

您可以使用if语句或JS可用的三元表达式来有条件地渲染表达式。 除了这些方法之外，还可以通过将所有表达式括在花括号中，然后再添加JS逻辑运算符&&，来将任何表达式嵌入JSX中。

```javascript
<h1>Hello!</h1>
{
    messages.length > 0 && !isLogin?
      <h2>
          You have {messages.length} unread messages.
      </h2>
      :
      <h2>
          You don't have unread messages.
      </h2>
}
```





# props 的 key 是什么？在元素数组中使用它的好处是什么？

`key`是props 中特殊的字符串属性，当我们用数组创建React元素的时候应该加上它。`key`可以帮助 React 来识别哪些数组的 items 更改了，或者添加删除了等。

大多数情况下，我们都从数组中使用 ID来作为 key

```jsx
const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
)
```

如果你的数组 items 中没有唯一的ID 的话，也可以使用索引作为 key。

```javascript
const todoItems = todos.map((todo, index) =>
  <li key={index}>
    {todo.text}
  </li>
)
```

> 如果项目的顺序可能更改，则不建议将索引当做 key。 这可能会对性能产生负面影响，并可能导致组件状态出现问题
>
> 如果没有 key属性，控制台会显示警告信息





# refs 的用途是什么

ref用于返回对该元素的引用。 在大多数情况下，应避免使用它们，但是，当您需要直接访问DOM元素或组件实例时，它们会很有用。





# 如何去创建 refs

## class 组件两种使用方法

1. 使用React.createRef（）方法创建引用，并通过ref属性将其附加到React元素。 为了在整个组件中使用引用，只需将引用分配给构造函数中的instance属性。
   ```jsx
   class MyComponent extends React.Component {
     constructor(props) {
       super(props)
       this.myRef = React.createRef()
     }
     render() {
       return <div ref={this.myRef} />
     }
   }
   ```
2. 不管 React 的版本如何，你都可以使用 ref 回调函数。举个例子，下面的SearchBar 组件中的 input 元素访问方式如下
   ```jsx
   class SearchBar extends Component {
      constructor(props) {
         super(props);
         this.txtSearch = null;
         this.state = { term: '' };
         this.setInputSearchRef = e => {
            this.txtSearch = e;
         }
      }
      onInputChange(event) {
         this.setState({ term: this.txtSearch.value });
      }
      render() {
         return (
            <input
               value={this.state.term}
               onChange={this.onInputChange.bind(this)}
               ref={this.setInputSearchRef} />
         );
      }
   }
   ```



## hooks 组件使用 ref（译者补充）

- 在函数组件中使用 useRef

```javascript
const refContainer = useRef(initialValue);
```

useRef返回一个可变的ref对象，该对象的.current属性已初始化为传递的参数（initialValue）。 返回的对象将在组件的整个生命周期内保持不变。

- 在 JSX 中使用

```jsx
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` points to the mounted text input element
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

本质上，useRef就像一个盒子，它的内部`.current `属性保存着可变的值

如果您通过`<div ref = {myRef} />`将ref对象传递给React，则只要该节点发生更改，React就会将其`.current`属性设置为相应的DOM节点

useRef跟在class 组件中使用 ref 的方式类似，可以很方面的保留任何可变值。

请记住，useRef的内容更改时不会通知您。 更改`.current`属性不会导致重新渲染。

如果你希望将 ref 引用到DOM 节点时运行一些代码，可以使用 ref 回调函数。

比如：以下方式可以测量 DOM 节点位置或大小，每当ref引用附加到节点时，React都会调用该回调。[在线 demo](https://codesandbox.io/s/l7m0v5x4v9?file=/src/index.js)

```javascript
function MeasureExample() {
  const [height, setHeight] = useState(0);

  const measuredRef = useCallback(node => {
    if (node !== null) {
      setHeight(node.getBoundingClientRect().height);
    }
  }, []);

  return (
    <>
      <h1 ref={measuredRef}>Hello, world</h1>
      <h2>The above header is {Math.round(height)}px tall</h2>
    </>
  );
}
```

在此示例中，我们未选择useRef，因为对象ref不会通知我们有关当前ref值的更改。使用回调引用可确保即使子组件会稍后显示测量的DOM节点，我们仍会在父组件中收到有关此事件的通知，并可以更新度量值。[在线 demo](https://codesandbox.io/s/818zzk8m78)

请注意，例子中我们将`[]`为依赖项数组传递给useCallback。 这样可以确保我们的ref回调在重新渲染时不会发生变化，因此React不会不必要地调用它。





## 21.什么是 forward refs





# findDOMNode 和 refs 回调哪个是首选

首选是使用 refs 回调函数来替代 findDOMNode，因为findDOMNode将来会影响 React 的某些改进

**使用findDOMNode的传统方法**

```jsx
class MyComponent extends Component {
  componentDidMount() {
    findDOMNode(this).scrollIntoView()
  }

  render() {
    return <div />
  }
}
```

**推荐方法**

```javascript
class MyComponent extends Component {
  constructor(props){
    super(props);
    this.node = createRef();
  }
  componentDidMount() {
    this.node.current.scrollIntoView();
  }

  render() {
    return <div ref={this.node} />
  }
}
```



# 为什么字符串 refs停用了（不翻）



# 什么是虚拟 DOM

虚拟DOM（VDOM）是真实DOM的内存表示形式。 UI的表示形式保留在内存中，并与“真实” DOM同步。 这是在调用渲染函数和在屏幕上显示元素之间发生的一步。



## 虚拟DOM是什么(译者加)

简单来说虚拟 DOM 就是一个 JS 对象,它里面会包括标签名,属性,事件监听函数,子元素等等.

```javascript
const vNode={
   key:null,
   props:{ 
     children:[
     {type: 'span', ...},{type: 'span', ...}//子元素们
     ]
     className:'red',//标签属性
     onClick:()=>{} //事件
   },
   ref:null,
   type:'div', //标签名
   ...
}
```

React 的虚拟 DOM 是通过`creatElement`创建出来的,然后再通过 `babel`编译器编译成 JSX 语法,等编译器编译完了,就是`creatElement`的最开始的形态,创建完虚拟 DOM 之后,最后由一个叫 `ReactDOM.render`的函数来将虚拟 DOM 渲染到页面上.

React 函数执行的顺序为

```javascript
Component组件例如 App() ==> 生成虚拟 DOM ==> 浏览器将虚拟 DOM 解析成 DOM 节点 ==> render ==> 更新视图 ==> effect
```



## 虚拟 DOM 的优点(译者加)

1.减少次数

虚拟 DOM 通过算法能够有效减少操作节点的次数,例如通过数组的形式插入节点

```javascript
var arr = [];
var a = '<a href="javascript=;">添加</a>';
for (var j = 0; j < 100; j++) {
   arr.push(a); //把a都丢入数组中
    }
div.innerHTML = arr.join('') //把数组变成字符串 然后让innerHtml获取，得到添加元素的效果
```

2.减少范围

通过 DOM diff 算法能够把多余的 DOM 操作给省略掉,比如页面上已经有的内容,Diff 算法就不会重复渲染而是把多余的部分渲染出来

3.跨平台

虚拟DOM支持小程序、IOS应用、安卓应用等多平台，因为本身它就是一个JS对象，自然支持各依赖环境平台的语法



## DOM diff是什么(译者加)

DOM diff算法就是针对虚拟DOM的不同做的对比算法。

它本质上就是一个函数，我们称之为patch函数。它有两个参数，分别是旧的虚拟DOM和新的虚拟DOM

```javascript
patches=patch(oldVnode,newVnode)

//返回值类似于这个
[
  {type: 'INSERT', vNode: ... },
  {type: 'TEXT',  vNode: ... },
  {type: 'PROPS', propsPatch: [...]}
]
```

在比较的时候,它会进行记录,比如我给一个 div 加了 className,它就会记录div 变更了什么内容,然后通过优化算法返回一个数组,里面包含需要的操作,最后再去执行对应的 DOM 增删改查操作.



## DOM diff的缺陷(译者加)

由于是算法对比,所以有时候会存在问题,这个问题需要开发者解决,就是手动加上 `key`.



# 虚拟 DOM 是怎么运作的

虚拟 DOM（Virtual DOM） 的运作可分成三个简约的步骤

**1.不管数据什么时候改变，在虚拟 DOM 的描述中整个 UI都要发生重新渲染**

<img src="https://tva1.sinaimg.cn/large/008i3skNly1gqqaubmd7ij31a80i2n0f.jpg" alt="image-20210521200105939" style="zoom:50%;" />

**2.新的虚拟 DOM 和旧的虚拟 DOM 之间会计算差异（译者注：DOM Diff 算法）**

![image-20210521200031143](https://tva1.sinaimg.cn/large/008i3skNly1gqqatppr8fj31ae0kiteu.jpg)

**3.一旦完成计算，将仅使用实际已更改的内容来更新实际DOM。**

<img src="https://tva1.sinaimg.cn/large/008i3skNly1gqqat50cfgj30sk0iwq5y.jpg" alt="image-20210521195957683" style="zoom:50%;" />

# Shadow DOM和虚拟 DOM（Virtual DOM）之间的区别是什么

Shadow DOM是一种浏览器技术，主要用于确定Web组件中的变量和CSS。

 虚拟DOM是由浏览器API之上的JavaScript库实现的概念。



# React Fiber 是什么



# React Fiber 的目标是什么





# 受控组件是什么

在 form 里，用户在 input 元素中输入内容的同时控制 input 元素的组件就叫受控组件。每个状态的改变都会有相关的事件处理函数。

举个例子：以下例子中每个值的改变，我们都需要调用handleChange 函数

```javascript
handleChange(event) {
  this.setState({value: event.target.value.toUpperCase()})
}
```

## 受控组件（译者加）

在 React 中,受控组件就是表单元素 的state跟 React 的state 进行关联,并且通过 setState 来更新.React 组件控制表单,就叫受控组件.

```javascript
export default function App() {
  const [name,setName]=useState('')
  return (
    <div className="App">
     姓名<input value={name} onChange={(e)=>{
       setName(e.target.value)}
     }
     />
    </div>
  );
}
```

当我给 input 组件写上 value 属性,那么这个组件就受控了,你会发现页面上怎么输入都不行.然后需要通过 onChange 修改 state 的值.这里有个小插曲,React 的 onChange 跟 input 的 onChange 事件不一样,它是每写一个字就改变一下 state 的值.



# 非受控组件是什么

非受控组件是内部存储其自身状态的组件，您可以使用ref储存DOM以在需要时查找其当前值。 这有点像传统的HTML。

在下面的UserProfile组件中，使用ref访问输入的 `name`。

```javascript
class UserProfile extends React.Component {
  constructor(props) {
    super(props)
    this.handleSubmit = this.handleSubmit.bind(this)
    this.input = React.createRef()
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.current.value)
    event.preventDefault()
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          {'Name:'}
          <input type="text" ref={this.input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

在大多数情况下，建议使用受控组件来实现表单。

## 非受控组件（译者加）

非受控组件就是不使用 React 默认操控表单,这里一般都使用 ref 跟 defalutValue 结合.

```javascript
export default function App() {
  const [name, setName] = useState("");
  const ref = useRef(null);
  const count = useRef(0);
  useEffect(() => {
    count.current += 1;
    if (count.current > 1) {
      console.log(name);
    }
  }, [name]);
  return (
    <div className="App">
      姓名
      <input
        defaultValue={name}
        ref={ref}
        onBlur={() => {
          setName(ref.current.value);
        }}
      />
    </div>
  );
}
```



# React 有哪些生命周期函数

常见的生命周期函数有两种区分,一种是 Class 组件,还有一种是函数式组件

## Class 组件

常见的生命周期函数有component­Did­Mount,component­Did­Update,component­Will­Unmount

**component­Did­Mount**

组件第一次加载之后调用

一般会在这里请求 ajax 数据

**component­Did­Update**

组件更新的时候调用

**component­Will­Unmount**

组件卸载的时候调用

## 函数式组件

相当于**component­Did­Mount**

```javascript
useEffect(()=>{
  ...
},[])
```

相当于**component­Did­Update**,但是实际上它第一次渲染也会执行,所以需要跟 ref 结合

```javascript
const count = useRef(0);

useEffect(() => {
  count.current += 1;
  if (count.current > 1) {
    ...
  }
}, [name]);
```

相当于**component­Will­Unmount**

```javascript
useEffect(()=>{
  return ()=>{
    ... 这里的函数会在组件卸载时调用
  }
},[state])
```



# 如何实现组件通信

React 是单向数据流,意味着数据是自上而下流转的,在顶层的 state 中,数据会随着 props 向下流转,就像下面这个组件一样:

<img src="https://tva1.sinaimg.cn/large/008i3skNly1gqqaw0p58jj30ce06g0t4.jpg" alt="image-20210521200241232" style="zoom:80%;" />

count 存在 App 的 state 里，会以 prop 的形式向下传递：

<img src="https://tva1.sinaimg.cn/large/008i3skNly1gqqawm2rufj30b804ywey.jpg" alt="image-20210521200318369" style="zoom:80%;" />

如果想要数据向上传递，需要通过回调函数实现，因此必须首先将回调函数向下传递到任何想通过调用它来传递数据的组件中。

<img src="https://tva1.sinaimg.cn/large/008i3skNly1gqqax1zzfcj308404zwew.jpg" alt="image-20210521200343425" style="zoom:80%;" />

根据组件层级划分,我们大概可以分为以下关系:

1.父传子(props)

2.子传父(Callbacks)

3.爷传孙(context)

4.兄弟间(状态提升,需要有一个共同的爸爸)

5.全局管理(Redux)

针对上面的传递关系,我查了一些博客,总结了一下目前社区对于 Hooks 项目相关的数据流传递管理的方法,简单来说分为以下内容

- 使用 children 插槽传递 props
- Redux(可以换成Mobx之类的其他库)
-  useReducer + Context


# Redux 是什么

`Redux` 是提供可视化的状态管理工具,它主要围绕的思想是一个应用只有一个 store,通过 reducer 逻辑函数return 一个 state 来修改数据,通过 dispatch一个action 来修改数据.常用 api:store.getState(),dispatch(action).

我在项目中配合 react-redux 来使用,因为使用的是 hooks,所以还用了两个 API,useSelector 和 useDispatch.



# 什么是高阶组件？

高阶组件主要用来写React Class 组件的逻辑复用,使用方法是传入一个组件并且返回一个组件,所以它是一个参数和返回值都是函数的高阶函数.比如说 react-redux 里面的 connect 也是一个高阶组件



# connect原理是什么

react-redux 库提供的一个 API，connect 的作用是让你把组件和store连接起来，产生一个新的组件（connect 是高阶组件）.

