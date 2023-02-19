# 面试题

## React

### 一、React Fiber 是什么

React Fiber 是 React16.8 后新增的一种架构。它是 Reconciler 层的一种新型实现，它保存着各个节点静态的虚拟 DOM 数据结构和动态的诸如副作用以及状态的信息。从顶层 App 到 text 节点，都属于 Fiber 节点。

从 RootFiber 开始往下，所有子节点都用 child 连接，子节点用 return 保存父节点。从数据结构来说，它属于链表。

详情见：[ Fiber 架构](https://qiuyanxi.com/react/01#fiber-架构)

### 二、能讲讲 React Fiber 的工作原理吗

Fiber 架构用双缓存来保存生成的虚拟 DOM 树，其中当前渲染的是 current Fiber，而由于更新产生的叫 workInProgress Fiber，两种树的各个节点之间用 alternate 属性连接。 这种设计是为了最大程度地提高 Fiber 节点的复用性。

假设界面需要更新，如果节点的 tag 以及 key 没有发生变化，那么就可以直接根据 alternate 属性来对上次渲染的 Fiber 节点做一个副本并直接返回，这样就省去了重新构建 Fiber 节点带来性能开销。

当 workInProgress 树构建完成后，FiberRootNode 的 current 会指向它，这时它就变成了 current Fiber 树。

详情见：[Fiber 工作原理](https://qiuyanxi.com/react/01#fiber-工作原理)

### 三、React 16 和之前的版本有什么不一样？为什么？

React 16 升级了 15 的架构，加入了 scheduler （调度器）以及 带有 Fiber 的 Reconciler。

调度器是用来调度任务的，它能够给更新任务调度优先级，并且这种调度是异步的，可中断更新的。

Fiber 则是用来优化 Diff 算法的。一个是加入缓存减少重复生成虚拟 DOM 的开销，还一个是优化 Diff 算法，让 Diff 算法的对比尽量精细。

详情见：[架构的演进](https://qiuyanxi.com/react/01#架构的演进)

### 四、能讲讲 Diff 算法吗

React 16 之后的 Diff 算法是用 JSX 生成的对象与 current Fiber 之间的对比，根据对比结果来构建出 workInProgress Fiber 节点的一种实现。

React 官方文档有介绍过，具体的 Diff 规则是：

1. 如果 tag 不同，则重新生成 Fiber 节点
2. 如果 tag 相同但 key 不同，则重新生成 Fiber 节点
3. 如果 tag 和 key 都相同，则复用 current Fiber 节点，仅调整变更的属性
4. 同时分为单元素 Diff 算法以及多元素 Diff 算法。

总的来说，Diff 算法的实现决定原来的 Fiber 节点是复用还是销毁重建，当 Diff 算法完成后，会生成一个 mutation 交给 renderer 去更新视图。

详情见：[Diff 算法](https://qiuyanxi.com/react/02#diff-算法)

### 五、为什么渲染列表需要写 key，key 用 index 可以吗？

key 是 React 与开发者用来提高 React 渲染性能的一种约定。不单单渲染列表，即使在单元素中也可以用 key 提高 React 性能。

在单元素 Diff 算法中，key 用来标识当前节点，如果节点的 key 和 tag 没有变化，那么 React 会复用原来的 Fiber 节点。否则会销毁原来的节点以及子节点，并且重新构建一个 Fiber 节点。

在多元素 Diff 算法中，key 是元素的标识。当插入或者删除元素时，这个 key 能够告诉 React 到底是新增或删除了一个元素还是原来的元素变化了。

举个例子：`[{name:1},{name:2},{name:3}]`,当变成`[{name:1},{name:3}，{name:2}]`时，React 会疑惑，到底是移动了`{name:2}`还是把`{name:2},{name:3}`变成了`{name:3},{name:2}`呢？

如果仅用 index 是不能够让 React 知道数据是如何变化的。

如果给一个随时会变化的 key 则更糟糕，它会让 React 误以为所有东西都变化了，就会删掉原来的节点重新构建所有 Fiber 节点。

最好的方式是数据里有一个这样的唯一性标识，这样 React 就能最大程度地复用原来的 Fiber 节点。

当然，如果数据不做新增或者删除的处理，用 index 也可以消除 warning。

详情见：[多元素 diff 算法](https://qiuyanxi.com/react/02#多元素-diff-算法)





## JavaScript/TypeScript

### 一、什么是 Falsy 值和 Truthy 值

JavaScript 会在需要返回布尔类型的语句``&& 和 if()``中强制类型转换，falsy值就是转换后返回 false 的值。

常见 `falsy` 值：`0`,`-0`,`undefined`,`null`,`Nan`,`false`,`“”`

`truthy` 值：强制转换后返回 `true` 的值

### 二、JavaScript 有几种类型

四基：`string`、`number`、`bigInt`、`symbol`

两空：`undefined`、`null`

一对象：`object`

### 三、基本数据类型和引用数据类型的区别

对象与原始类型的根本区别之一是，对象是“通过引用”存储和复制的，而原始类型：字符串、数字、布尔值等 —— 总是“作为一个整体”存储和复制。

对象的赋值仅仅是拷贝地址，如果要创建两个完全独立的对象，则需要用到深拷贝来实现。

详情见：[对象引用和复制](https://qiuyanxi.com/javascript/3#对象引用和复制)

### 四、原型是什么

* 每个对象都有一个特殊的隐藏属性`[[Prototype]]`，它要么是另一个对象，要么就是 `null`。

* 通过 `[[Prototype]]` 引用的对象被称为“原型”。

* 原型可以链式引用，每个对象都会链接着一条原型链，终点是 null。

* 很多种方式可以访问到它，比如通过访问器属性`__proto__`、`Object.getPrototypeOf(obj)`等

* 很多种方式可以设置它，比如`Object.setPrototypeOf(obj)`、`__proto__`、`Object.create`等

* 如果对象身上不存在某个属性，那么 JavaScript 会在原型上寻找该属性，这就是原型继承。

* 原型方法上的 `this`是对象自身，因为`对象.method()`相当于显式声明了`this`。

* 虽然array、function、object 等都是不同的对象，它们也有不同的原型(`Array.prototype`、`Object.prototype`、`Date.prototype` 等)，但是它们都会有共同的终极原型`Object.prototype`。

* `prototype`存储着大量的内置方法，原始数据类型也将方法存储在包装器对象的`prototype`中，但`undefined`和`null`没有包装器。

* `for..in`会经过原型对象，但由于属性描述符中的`enumerable`设置成了`false`，所以不会被遍历出来。

* 默认情况下，每个对象的`[[Prototype]]`都指向它的构造函数的`prototype`。

   `对象.__proto__ === 其构造函数.prototype`。

* 当使用 new  时，构造函数的`prototype`会自动加到创建出来的对象的`__proto__`上。

* 原型方法可以被`call`、`apply`方法借用

* 通常，对象会从 `Object.prototype` 继承内建的方法和 `__proto__` getter/setter，会占用相应的键，且可能会导致副作用。原型为 `null` 时，对象才真正是空的。我们可以通过`__proto__`或者`Object.create`设置没有原型的对象。

详情参见：[JavaScript 中的原型](https://qiuyanxi.com/javascript/4#javascript-中的原型)

### 五、简述 JavaScript 的垃圾回收

详情参见：[深入理解垃圾回收](https://qiuyanxi.com/javascript/5#深入理解垃圾回收)

### 六、闭包是什么

* 闭包就是一个函数可以记住其外部变量并可以访问这些变量。
* 闭包能产生的原因是函数的隐藏属性`[[Environment]]`保存着从函数内部词法环境到全局词法环境的引用。
* 闭包能一直存在于内存中是因为它是可达的——有外部变量引用到它。
* 如果闭包函数不再可达，那么垃圾收集器就会删除掉它。
* `var`、`let/const`、`function`这几个变量声明的方式，生成词法环境时实现不太一致。
* `var`和`function`都允许在声明前使用变量，对于 `let/const`来说，它们是提升的。
* `var`和`function`的区别是`function`在初始化时是立即初始化的，而 `var`会先变成`undefined`。

详情参见：[深入理解闭包](https://qiuyanxi.com/javascript/6#深入理解闭包)

旧文章：[闭包](<https://github.com/18888628835/Blog/blob/main/JavaScript设计模式/JavaScript设计模式与开发实践读书笔记(基础篇).md#31-闭包>) 、[深入理解闭包](https://github.com/18888628835/Blog/issues/7)

### 七、let、const 与 var 的区别

* `let` 和 `const` 的区别是 `const` 不能再次赋值

* `let/const`与 `var` 的区别是`var `会变量提升，`let/const`则没有。原因是 `JavaScript` 对 `let/const`声明的变量限制为声明后才能使用,对`var`声明的变量则没有这个限制。这个限制也导致块级作用域下“暂时性死区”问题。

* `let`声明和`var`声明受块级作用域限制不同。

  ```js
  {
    let a=10;
    var b=20
  }
  a //Uncaught ReferenceError: a is not defined
  b //20
  ```

* let声明配合循环可以创建独立的作用域

  ```js
  for(var i=0;i<6;i++){
    setTimeout(()=>{
      console.log(i)
    },0)
  }
  //6 6 6 6 6 6
  
  for(let i=0;i<6;i++){
    setTimeout(()=>{
      console.log(i)
    },0)
  }
  //0 1 2 3 4 5 
  ```

* `let/const`不允许重复声明
* `let/const`声明不会将变量挂到`window`顶层对象中

详情参见：[let、const 与 var 的区别](https://github.com/18888628835/Blog/issues/11)



4.[鼠标事件实现拖放](https://github.com/18888628835/Blog/blob/main/浏览器/UI事件.md#31-鼠标事件实现拖放)

5.[手动实现 new 关键字](https://github.com/18888628835/Blog/issues/40)

6.[手写 call、apply、bind](https://github.com/18888628835/Blog/issues/41)

8.[手写防抖和节流](https://github.com/18888628835/Interview/issues/12)

9.[memory 函数](https://github.com/18888628835/Blog/issues/43)

10.[AOP 函数](<https://github.com/18888628835/Blog/blob/main/JavaScript设计模式/JavaScript设计模式与开发实践读书笔记(基础篇).md#323-高阶函数实现aop>)

11.[谈谈对前端路由的理解](https://github.com/18888628835/Interview/issues/17)

12.[[1,2,3].map(parseInt)返回什么](https://github.com/18888628835/Interview/issues/16)

13.[(a ==1 && a== 2 && a==3) 可能为 true 吗](https://github.com/18888628835/Interview/issues/16)

14.[symbol 是什么](https://github.com/18888628835/Blog/issues/19)

15.[构造函数和 Class 如何实现继承](https://github.com/18888628835/Interview/issues/6)

16.[never 类型有什么用](https://github.com/18888628835/Interview/issues/13)

17.[如何渲染一万条数据-分时函数](<https://github.com/18888628835/Blog/blob/main/JavaScript设计模式/JavaScript设计模式与开发实践读书笔记(基础篇).md#324-高阶函数的其他应用>)

18.[惰性加载函数](<https://github.com/18888628835/Blog/blob/main/JavaScript设计模式/JavaScript设计模式与开发实践读书笔记(基础篇).md#324-高阶函数的其他应用>)

19.[什么是高阶函数](<https://github.com/18888628835/Blog/blob/main/JavaScript设计模式/JavaScript设计模式与开发实践读书笔记(基础篇).md#32-高阶函数>)

## HTML

1.[HTML 语义化](https://github.com/18888628835/Interview/blob/main/HTML面试题.md#HTML语义化)

2.[用过哪些 HTML 5 标签](https://github.com/18888628835/Interview/blob/main/HTML面试题.md#用过哪些HTML5标签)

3.[Canvas 和 SVG 的区别是什么](https://github.com/18888628835/Interview/blob/main/HTML面试题.md#Canvas和SVG的区别是什么)

## CSS

1.使用 CSS 实现三角形

```html
<style>
  #div {
    width: 0;
    height: 0;
    border: 10px transparent solid;
    border-bottom-color: red;
    border-top: none;
  }
</style>
<div id="div"></div>
```

2.[BFC 是什么](https://github.com/18888628835/Interview/issues/9)

3.[清除浮动的三种方法](https://github.com/18888628835/Interview/issues/10)

4.[垂直居中的五种方式](https://github.com/18888628835/Interview/issues/7)

## HTTP

1.[请说说你知道的 HTTP 状态码](https://github.com/18888628835/Blog/issues/31)

2.[http 缓存控制](https://github.com/18888628835/Blog/blob/main/HTTP/HTTP缓存控制.md)

3.[get 和 post 的区别](https://github.com/18888628835/Interview/blob/main/HTTP面试题.md#get-和-post-的区别)

4.[Cookie、Session、localStorage、sessionStorage 的区别](https://github.com/18888628835/Interview/blob/main/HTTP面试题.md#cookiesessionlocalstoragesessionstorage)

5.[什么是 CSRF 攻击](https://github.com/18888628835/Interview/blob/main/HTTP面试题.md#csrf-攻击)

6.[什么是 XSS 攻击](https://github.com/18888628835/Interview/blob/main/HTTP面试题.md#xss-攻击)

7.[Cookie 是什么](https://github.com/18888628835/Blog/issues/35)

8.[从 url 输入页面到页面展示到底发生了什么](https://github.com/18888628835/Blog/issues/27)

9.[HTTP 和 HTTPS 的区别](https://github.com/18888628835/Interview/blob/main/HTTP面试题.md#http-和-https)

10.[HTTP2.0 与 HTTP1.1 的区别](https://github.com/18888628835/Interview/blob/main/HTTP面试题.md#http20)

11.[请介绍一下 WebSocket](https://github.com/18888628835/Blog/blob/main/JavaScript/WebSocket.md#websocket简介)

12.[TCP 和 UDP 的区别](https://github.com/18888628835/Interview/blob/main/HTTP面试题.md#tcp-和-udp-的区别)

13.[跨域是什么,如何实现](https://github.com/18888628835/Interview/issues/11)

14.[点击劫持攻击是什么](https://github.com/18888628835/Blog/blob/main/浏览器/Frame和window.md#点击劫持攻击)

15.[如何跨窗口通信](https://github.com/18888628835/Blog/blob/main/浏览器/Frame和window.md#跨窗口通信)

## 浏览器

1.[请简述页面的生命周期](https://github.com/18888628835/Blog/blob/main/浏览器/浏览器加载文档和资源.md#页面生命周期)

2.[async 和 defer 的区别](https://github.com/18888628835/Blog/blob/main/浏览器/浏览器加载文档和资源.md#脚本加载)

3.[event loop 是什么](https://github.com/18888628835/Blog/blob/main/浏览器/事件循环.md)

4.[浏览器渲染的过程](https://github.com/18888628835/Blog/blob/main/浏览器/浏览器渲染过程.md#浏览器渲染)

5.[getElementBy*与 querySelector*的不同之处](https://github.com/18888628835/Blog/blob/main/浏览器/Document.md#44-实时的集合)

6.[Attributes 和 properties 的区别](https://github.com/18888628835/Blog/blob/main/浏览器/Document.md#五attributes-和-properties)

7.[介绍一下元素的几何属性](https://github.com/18888628835/Blog/blob/main/浏览器/Document.md#八元素大小和滚动)

## 扩展

1.[PWA 是什么](https://github.com/18888628835/Blog/blob/main/Flutter/H5手机App开发概念.md#31-pwa)

## 算法题

1.[移除元素](https://leetcode-cn.com/problems/remove-element/)

2.[回文数](https://leetcode-cn.com/problems/palindrome-number/)

3.[用栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

4.[用队列实现栈](https://leetcode-cn.com/problems/implement-stack-using-queues/)

5.[反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

6.[链表中倒数第 k 个节点](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

7.[扁平化数组与树形结构的互转](https://github.com/18888628835/Blog/issues/65)

8.[替换空格](https://leetcode-cn.com/problems/ti-huan-kong-ge-lcof/)

9.[有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

10.[从上到下打印二叉树](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/)

11.[斐波那契数列](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)

12.[两数之和](https://leetcode-cn.com/problems/two-sum/)

13.[从尾到头打印链表](https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)

14.[删除链表的节点](https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)

15.[旋转链表](https://leetcode-cn.com/problems/rotate-list/)

16.[删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

17.[数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

18.[二进制中 1 的个数](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)

19.[包含 min 函数的栈](https://leetcode-cn.com/problems/bao-han-minhan-shu-de-zhan-lcof/)

20.[复杂链表的复制](https://leetcode-cn.com/problems/fu-za-lian-biao-de-fu-zhi-lcof/)

21.[左旋转字符串](https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)

22.[在排序数组中查找数字 I](https://leetcode-cn.com/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)

23.[0 ～ n-1 中缺失的数字](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)

24.[二维数组中的查找](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

25.[旋转数组的最小数字](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

26.[第一个只出现一次的字符](https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/)

27.[从上到下打印二叉树 II](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

28.[从上到下打印二叉树 III](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)

29.[二叉树的深度](https://leetcode-cn.com/problems/er-cha-shu-de-shen-du-lcof/)

30.[二叉树的镜像](https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/)

31.[对称的二叉树](https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof/)

32.[树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)

33.[二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

34.[二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

35.[二叉树的后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

36.[合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/)

37.[有效的回文](https://leetcode-cn.com/problems/XltzEq/)

38.[最多删除一个字符得到回文](https://leetcode-cn.com/problems/RQku0D/)

39.[合并两个排序的链表](https://leetcode-cn.com/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/)

40.[两个链表的第一个公共节点](https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)

41.[删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

42.[删除排序链表中的重复元素 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

43.[反转链表 II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

44.[判断链表是否有环](https://leetcode-cn.com/problems/linked-list-cycle/)

45.[链表中环的入口节点](https://leetcode-cn.com/problems/c32eOV/)

46.[每日温度](https://leetcode-cn.com/problems/iIQa4I/)

47.[滑动窗口的最大值](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)

48.[二叉搜索树的第 k 大节点](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)

49.[二叉搜索树中的搜索](https://leetcode-cn.com/problems/search-in-a-binary-search-tree/)

50.[二叉搜索树中的插入操作](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

51.[验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

52.[青蛙跳台阶问题](https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/)

53.[股票的最大利润](https://leetcode-cn.com/problems/gu-piao-de-zui-da-li-run-lcof/)

54.[两数之和 IV - 输入 BST](https://leetcode-cn.com/problems/two-sum-iv-input-is-a-bst/)

55.[打印从 1 到最大的 n 位数](https://leetcode-cn.com/problems/da-yin-cong-1dao-zui-da-de-nwei-shu-lcof/)

56.[求 1+2+…+n](https://leetcode-cn.com/problems/qiu-12n-lcof/)
