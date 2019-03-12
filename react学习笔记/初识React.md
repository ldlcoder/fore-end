React的核心思想是：组件封装

每个组件维护自己的状态和UI，当状态变更，自动重新渲染整个组件。

基于这样的方式，我们不需要在不厌其烦的来回查找某个DOM元素，然后去反复的操作DOM更改UI。

React大体上包含这些概念：

- 组件
- JSX
- Virtual DOM
- DATA Flow

这里咱们通过一个组件来简单的认识一下React


```
import React, { Component } from 'react'
import { render } from 'react-dom'

class HelloMessage extends Component {
    render () {
        return <div> Hello { this.props.name } </div>
    }
}

//加载组件到DOM元素 mountNode
render(<HelloMessage name="LDL" />, mountNode)
```
#### 组件
React应用都是构建在组件之上的。
上面的HelloMessage就是一个React构建的组件，最后一句render函数会把这个组件显示到页面某个元素mountNode里面，显示的内容就是

```
<div>Hello LDL</div>
```
**props**是react的两个核心概念之一，另一个是**state**（这个组件没有使用到）。可以把props看作是组件的配置属性，在组件内部是不变的，只是在调用的时候传入不同的属性（比如这里name）来定制显示这个组件。

#### JSX
从上面的代码可以看到将HTML直接嵌入到JS代码中，这个就是React提出的一种叫JSX的语法，这个应该是最开始接触React最不能接受的设定之一，因为前端被“表现和逻辑分层的思想“洗脑”了”。但是实际上HTML是组成一个组件不可分割的一部分，能够将HTML封装起来才是组件的完全体。React发明了JSX语法是非常聪明的做法，让前端实现真正意义上的组件化成为可能。

#### Virtual DOM
当组件状态state发生变化的时候，React会自动调用render方法重新渲染整个组件的UI。当然如果真的是这样大面积的渲染，那么性能将是很大的问题，所以，React实现了Virtual DOM，组件DOM结构就是映射到这个Virtual DOM上的，React在这个Virtual DOM上
实现了一个diff算法，当要重新渲染组件的时候，会通过diff寻找要变更的DOM节点，再把这个修改更改到浏览器实际的DOM节点上，所以实际上不是真的渲染整个DOM树。这个Virtual DOM是一个纯粹的js数据结构，所以性能会比原生的DOM快很多。

#### DATA Flow
“单向数据绑定”是React推崇的一种应用架构的方式。当应用足够复杂时候才能体会到它的好处，虽然在一般的应用场景下你可能不会意识到它的存在，也不会影响你的使用。