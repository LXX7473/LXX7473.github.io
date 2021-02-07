---
title: Javascript中的模块化(更新中~)
date: 2020-11-26 17:50:47
tags: node.js
categories: 笔记
---

### 前言
**写笔记的本意是对自己所学知识的一个总结，目前文笔和技术都不算成熟，可能有些知识点表达的不是很准确或有误，欢迎批评指正~~**
## Javascript模块化开发

​	众所周知，早期的`Javascript`大多情况被用来做表单验证、动画实现，通常情况下短短几行代码就可以搞定。并且都被编写在`script标签`里面。
​	但是随着前端和`JavaScript`的快速发展，`JavaScript`代码变得越来越复杂，从`Ajax`的出现，到后来的前后端分离，再到现在的SPA应用，前端页面正在变的越来越复杂，而没有”模块化“这件事，在很长一段时间都是`Javascript`的硬伤


#### 没有模块化会引发的问题

+ 代码多，命名冲突概率大
+ 代码冗余，请求过多拖慢速度
+ 文件间依赖增多，易出现引用错误，导致代码运行出错
+ 修改或者改版时，要去成百上千行代码里找，难维护
综上所述，不论是从开发还是体验角度，都需要解决方案。
#### 前端众多的模块化解决方案

JavaScript社区为了解决上面的问题，曾涌现出一系列好用的规范

例如：

- CommonJs
- AMD
- CMD
- ES module

由于AMD和CMD现在应用的地方并不是很多，所以本文主要针对**CommonJS**的模块化进行介绍，也会对**ES Module**进行简要介绍

## CommonJS和Node

 我们需要知道`CommonJS`是一个规范，最初提出来是在浏览器以外的地方使用，并且当时被命名为`ServerJS`，后来为了体现它的广泛性，修改为`CommonJS`，平时我们也会简称为`CJS`。
 
- `Node`是`CommonJS`在服务器端一个具有代表性的实现； 
- ` Browserify`是`CommonJS`在浏览器中的一种实现；
- `webpack`打包工具具备对`CommonJS`的支持和转换；

由于`Node`对`CommonJS`进行了支持和实现，所以我们可以在开发中很方便的进行模块化开发，但要特别注意两点

- 在`Node`中每一个`JS`文件都是一个单独的模块；
- 这个模块中包括`CommonJS`规范的核心变量：`exports`、`module.exports`、`require`；

#### Node模块化的导入和导出

其中`exports`和`module.exports`可以负责对模块中的内容进行导出； 

`require`函数可以帮助我们导入其他模块，包括：（自定义模块、系统模块、第三方库模块）；

#### exports与require

下图短短几行代码，就是对`Node`模块化的体现~

`exports`的本质是一个对象（如下图）。

在`1.js文件`中

- 通过`.`的方式动态的为`exports`这个对象添加属性，将该对象导出该模块
- 蓝色框中代表的是被导出的属性名，**可以为任意值。**

在`2.js文件`中

- 通过`data`来对`require函数`的返回值进行接收，**实际上：接受到的data值实际就是导出的exports对象**！相信很多人看到这个结论都是疑惑的。那我们不妨论证一下这个观点。

{% asset_img  1.png This is an example image %}

#### 验证require函数的返回值是否与exports相等

如上图所示：

1. 首先我们先将`name`,`age`两个变量分别导出，然后在`2.js`中进行引入，
2. 打印结果，然后在1s之后给`data`的`name`属性重新赋值
3. 在2s后重新打印`1.js`中`exports`这个对象的值，
4. 如果`exports`这个对象的`name`属性被重新赋值，就说明`require函数`的返回值与`exports`这个对象指向的是同一个地址

{% asset_img  2.png This is an example image %}



#### 画图解析上图代码

{% asset_img  3.png This is an example image %}



#### 结论

`exports`被重新赋值，说明`exports`对象和`require`函数的返回值指向同一个引用地址（浅拷贝）。

到这里我们已经看似完成了模块化的两个关键因素，导入和导出，那么`module.exports`又是什么呢？它和`exports`又有什么区别呢？让我们接着往下看。

#### module.exports

首先，我们要知道`CommonJS规范`中是没有`module.exports`这个概念的，

Node在实现的过程中使用的是`Module类`，每一个模块都是`Module`的一个实例，我们可以尝试打印一下这个属性（如下图所示），它的默认值是`{}`,





{% asset_img  4.png This is an example image %}

在很多时候我们发现`module.exports={name}`和`exports.name=name`这两种导出方式，都可以实现，除了语法之外，二者几乎并无区别，都完成了导出这件事，并且通过下图这种方式我们还可以得知`module.exports===exports===2.js`中的`data`，三者指向同一个引用地址

{% asset_img  5.png This is an example image %}



但是有一件事还没有确定，那就是`module.exports`和`exports`到底是谁真正的实现了`Node`的模块化导出，我们可以做一个实验，**注意看下面两段代码**！！！

{% asset_img  6.png This is an example image %}

##### 第一种操作：

1. 给`module.exports`赋值为`{}`
2. 通过`exports`进行导出
3. 输出结果为{}

说明虽然`exports`在后面进行导出，但仍然被前面的`module.exports`给覆盖掉了

{% asset_img  7.png This is an example image %}

##### 第二种操作:

1. 先给`exports`的值设置为{}，
2. 用`module.exports`导出变量，
3. 仍然可以导出成功变量。

#### 为什么第一种操作中，exports会被module.exports覆盖掉？

结合图片（图没画好。。）我们可以看出，第二步的时候`module.exports`改变了引用地址，所以不管`exports`的值再怎么变化，也不会影响导出的结果。

{% asset_img  8.png This is an example image %}





#### 结论

**所以在Node中真正实现导出的是`module.exports`**,而不是**`exports`**  ，也就是**`exports=module.exports`**,并且这个操作是在模块开始加载的时候就被执行了。

## require中的细节

介绍了这么多的模块的导出，接下来讲一下`require`导入的细节，我们知道`require`是一个函数，可以帮助我们引入一个文件（模块）中导入的对象，那么`require`的查找规则到底是怎么样的呢？

暂且假定`require(X)`

#### 情况一：核心模块

X是一个核心模块，比如`path`，`http`

- 直接返回核心模块停止查找

如果你想引入一个和核心模块相同名字的自定义模块的时候，你是不成功的，如果想要成功就必须换一个名字，

#### 情况二：路径形式的文件模块

X是以./或者../或者/（根目录开头的）

**第一步：将X当做一个文件在对应的目录下查找**

1. 如果有后缀名，直接按照文件路径查找对应的文件
2. 如果没有后缀名，会按照如下顺序
   1. 直接查找文件X（无后缀）
   2. 查找`X.js文件`
   3. 查找`X.json文件`
   4. 查找`X.node文件`

**第二步：没有找到对应的文件，将X作为一个目录**

- 查找目录下面的`index`文件

  1. 查找`X/index.js文件 `
  2. 查找`X/index.json文件 `
  3. 查找`X/index.node文件` 

  如果仍然没找到，那么报错`not found`

#### 情况三：自定义模块

直接是一个X（没有路径），并且X不是一个核心模块，也可以称为**自定义模块**

查找顺序：首先查找当前目录下的node_modules目录，然后查找父级....沿路径向上依次递归，直到根目录下的`node_modules`目录，该查找方式与`Javascript`的原型链/作用域链查找方式十分类似，由此也可以看出，模块的路径越深，模块查找的耗时也就越多，这也是自定义模块加载速度是最慢的原因。

ps:(图中的`paths`，是`module`中的属性)
{% asset_img  9.png This is an example image %}

#### 情况四（特殊）：优先从缓存加载

除了以上三种情况之外，我们还需要知晓一点，与前端浏览器会缓存静态脚本文件以提高性能一样，`Node`对引入过的模块都会进行缓存，以减少二次引用的开销，

#### 几种情况的加载速度对比

1. 不管是核心模块还是文件模块，`require`对于相同模块的二次加载都一律采用**缓存加载优先**的方式，所以属于第一优先级。
2. 而`Node`核心模块在`Node`源代码编译过程中，编译进了二进制执行文件
3. 以路径形式的文件模块，以路径作为路引进行查找，由于给定了明确的文件位置，所以也可以节约大量的时间，慢于核心模块
4. 由于既不是核心模块，也没有详细的地址，所以这类文件查找起来是最费时的

**缓存加载>核心模块>路径形式的文件模块>自定义模块**

#### 分析模块的加载过程

- 如果模块被循环引入，那么加载顺序是什么？让我们看下下面这段代码

{% asset_img  10.png This is an example image %}

涉及的文件太多，所以我把它抽象成图看起来会清晰一些

{% asset_img  11.png This is an example image %}

- 这个其实是一种数据结构：图结构； 
- 图结构在遍历的过程中，有深度优先搜索`（DFS, depth first search）`和广度优先搜索`（BFS, breadth first search）`；
- `Node`采用的是深度优先算法：`aaa -> ccc -> ddd -> eee ->bbb`

**所以根据上图，我们知道同一个模块不会被重复加载，并且在模块被循环引用时，会根据深度优先进行引入**

#### 问题：模块被多次引入时  为什么只会加载运行一次呢？

- 这是因为每个模块对象`module`都有一个属性：`loaded`。 
- 当`load`为`false`表示还没有加载，为`true`表示已经加载；

#### CommonJS规范缺点

{% asset_img  12.png This is an example image %}

由图可知在`2.js`中引用了`1.js`，而打印的顺序是先执行`1.js`，之后再执行`2.js`这说明`CommonJS`加载模块是同步进行的

- 这个在服务器不会有什么问题，因为服务器加载的js文件都是本地文件，加载速度非常快； 
- 但是在客户端的话情况就不会很乐观，这意味着后面的js代码无法继续正常执行，即使是一些简单的DOM操作

所以在浏览器中我们通常不使用`CommonJS规范`，一般使用`ES Module`进行模块化开发

## ES Module的特点

 `ES Module`模块采用`export`和`import`**关键字**来实现模块化：

- `export`负责将模块内的内容导出；
- `import`负责从其他模块导入内容；

#### 注意：

1. 采用`ES Module`将自动采用严格模式：`use strict` 

 如果你不熟悉严格模式可以简单看一下MDN上的解析： https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode

2. 当你在浏览器中直接打开这段代码的时候，会发生跨域。

这个问题在MDN上也有相应的解释：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Modules

大致意思就是： 当本地测试的时候，如果你通过本地加载Html 文件 (比如一个 file:// 路径的文件), 你将会遇到` CORS `错误，因为`Javascript `模块安全性需要。 

所以你需要通过一个服务器来测试。

{% asset_img  13.png This is an example image %}
{% asset_img  14_1.png This is an example image %}


#### export关键字

`export`关键字将一个模块中的变量、函数、类等导出；

 我们希望将其他中内容全部导出，它可以有如下的方式：

- 方式一：在语句声明的前面直接加上`export`关键字
- 方式二：将所有需要导出的标识符，放到`export`后面的 `{}`中 , 注意：这里的 `{}`里面不是ES6的对象字面量的增强写法，`{}`也不是表示一个对象的； 所以： `export {name: name}`，是错误的写法；
- 方式三：导出时给标识符起一个别名



#### import关键字

`import`关键字负责从另外一个模块中导入内容

导入内容的方式也有多种：

- 方式一：`import {标识符列表} from '模块'`；  注意：这里的{}也不是一个对象，里面只是存放导入的标识符列表内容；
- 方式二：导入时给标识符起别名
- 方式三：通过 * 将模块功能放到一个模块功能对象`（a module object）`上

{% asset_img  14.png This is an example image %}



#### default用法

前面我们学习的导出功能都是有名字的导出（named exports）： 

- 在导出`export`时指定了名字； 
- 在导入`import`时需要知道具体的名字；

还有一种导出叫做默认导出（default export）

- 默认导出`export`时可以不需要指定名字； 
- 在导入时不需要使用 {}，并且可以自己来指定名字； 
- 它也方便我们和现有的`CommonJS`等规范相互操作；

{% asset_img  15.png This is an example image %}

**注意：在一个模块中，只能有一个默认导出（default export）；**



#### Node对ES Module的支持

在最新的`Current`版本中，支持`ES module`我们需要进行如下操作：

- 方式一：在`package.json`中配置 `type: module`
- 方式二：文件以 `.mjs` 结尾，表示使用的是`ES Module`； 

#### ES Module加载过程

`ES Module`加载`js文件`的过程是编译（解析）时加载的，并且是异步的

异步的意味着：`js引擎`在遇到`import`时会去获取这个`js`文件，但是这个获取的过程是异步的，并不会阻塞主线程继续执行； 

- 也就是说设置了` type=module `的代码，相当于在`script标签上`也加上了 `async `属性； 
- 如果我们后面有普通的`script标签`以及对应的代码，那么`ES Module`对应的js文件和代码不会阻塞它们的执行

{% asset_img  16.png This is an example image %}





















































































