## React
### 1.虚拟DOM的原理，为什么虚拟dom会提高性能
我们知道前端的DOM是一棵树，对于一个element来说，我们需要关注的是这个element的
tagName、属性、以及子元素，而这完全可以用一个js对象来表示，比如，使用tagName属性
来说明标签名，将所有的属性和值作为一个对象表示为props，children属性来表示这个element的
子元素，同样有了这个js对象，我们就可以构建一棵真实的DOM树，我们可以在每一次元素也就是js对象
有任何变动的时候来重新构造一棵树，将这棵新的树与旧的DOM数进行比对，找出真正差异的地方，然后
将这些差异应用在真实的DOM中，也就实现了一个简单的Virtual DOM算法。
利用 dom diff 算法避免了没有必要的 dom 操作，从而提高性能。

参考
[深度剖析：如何实现一个 Virtual DOM 算法](https://github.com/livoras/blog/issues/13)

### 2.简要介绍一下React组件的生命周期以及react的生命周期函数
React的组件在第一次挂载的时候回首先获得父组件传递的props，接着获取初始的state值，接着经历挂载
阶段的三个生命周期函数也就是ComponentWillMount、render、ComponentDidMount，这三个函数
分别代表着组件将会挂载、组件渲染、组件挂载完毕三个阶段。在组件挂载完成之后，组件的props和state的任一
改变都会导致组件进入更新状态，在组件更新阶段如果是props改变，则进入ComponentWillReceiveProps函数，
接着进入ComponetShouldUpdate进行判定是否需要更新，如果是state的改变则直接进入ComponentShouldUpdate
判定，这个默认是true，当判定不需要更新的话，组件继续运行，需要更新则依次进入ComponentWillUpdate、render、
ComponentDidUpdate三个生命周期函数，依次代表着组件将要更新、组件在渲染、组件更新完毕。当组件卸载时，会首先
进入生命周期函数ComponentWillUnmount，之后才进行卸载。如下图：
![](../image/react1.png)
生命周期函数：
初始化阶段：
getDefaultProps:获取实例的默认属性
getInitialState:获取每个实例的初始化状态
componentWillMount：组件即将被装载、渲染到页面上
render:组件在这里生成虚拟的 DOM 节点
componentDidMount:组件真正在被装载之后
运行中状态：
componentWillReceiveProps:组件将要接收到属性的时候调用
shouldComponentUpdate:组件接受到新属性或者新状态的时候（可以返回 false，接收数据后不更新，阻止 render 调用，后面的函数不会被继续执行了）shouldComponentUpdate 这个方法用来判断是否需要调用 render 方法重新描绘 dom。因为 dom 的描绘非常消耗性能，如果我们能在 shouldComponentUpdate 方法中能够写出更优化的 dom diff 算法，可以极大的提高性能。
componentWillUpdate:组件即将更新不能修改属性和状态
render:组件重新描绘
componentDidUpdate:组件已经更新
销毁阶段：
componentWillUnmount:组件即将销毁

### 3.简要介绍一下React中的refs以及它的作用
Refs 是 React 提供给我们的安全访问 DOM 元素或者某个组件实例的句柄。我们可以为元素添加 ref 属性然后在回调函数中接受该元素在 DOM 树中的句柄，该值会作为回调函数的第一个参数返回：
```javascript
class CustomForm extends Component {
  handleSubmit = () => {
    console.log("Input Value: ", this.input.value)
  }
  render () {
    return (
      <form onSubmit={this.handleSubmit}>
        <input
          type='text'
          ref={(input) => this.input = input} />
        <button type='submit'>Submit</button>
      </form>
    )
  }
}
```
上述代码中的 input 域包含了一个 ref 属性，该属性声明的回调函数会接收 input 对应的 DOM 元素，我们将其绑定到 this 指针以便在其他的类函数中使用。另外值得一提的是，refs 并不是类组件的专属，函数式组件同样能够利用闭包暂存其值：
```javascript
function CustomForm ({handleSubmit}) {
  let inputElement
  return (
    <form onSubmit={() => handleSubmit(inputElement.value)}>
      <input
        type='text'
        ref={(input) => inputElement = input} />
      <button type='submit'>Submit</button>
    </form>
  )
}
```
### 4.简要介绍一下key以及它的作用
Keys 是 React 用于追踪哪些列表中元素被修改、被添加或者被移除的辅助标识。
```javascript
render () {
  return (
    <ul>
      {this.state.todoItems.map(({item, key}) => {
        return <li key={key}>{item}</li>
      })}
    </ul>
  )
}
```
在开发过程中，我们需要保证某个元素的 key 在其同级元素中具有唯一性。在 React Diff 算法中 React 会借助元素的 Key 值来判断该元素是新近创建的还是被移动而来的元素，从而减少不必要的元素重渲染。此外，React 还需要借助 Key 值来判断元素与本地状态的关联关系，因此我们绝不可忽视转换函数中 Key 的重要性。

### 5.在实际开发中shouldComponentUpdate有什么作用
shouldComponentUpdate 这个方法用来判断是否需要调用 render 方法重新描绘 dom。因为 dom 的描绘非常消耗性能，如果我们能在 shouldComponentUpdate 方法中能够写出更优化的 dom diff 算法，可以极大的提高性能。
### 6.简要介绍一下Redux，她有什么缺点
redux 是一个应用数据流框架，主要是解决了组件间状态共享的问题，原理是集中式管理，主要有三个核心方法，action，store，reducer，工作流程是 view 调用 store 的 dispatch 接收 action 传入 store，reducer 进行 state 操作，view 通过 store 提供的 getState 获取最新的数据，flux 也是用来进行数据操作的，有四个组成部分 action，dispatch，view，store，工作流程是 view 发出一个 action，派发器接收 action，让 store 进行数据更新，更新完成以后 store 发出 change，view 接受 change 更新视图。Redux 和 Flux 很像。主要区别在于 Flux 有多个可以改变应用状态的 store，在 Flux 中 dispatcher 被用来传递数据到注册的回调事件，但是在 redux 中只能定义一个可更新状态的 store，redux 把 store 和 Dispatcher 合并,结构更加简单清晰
新增 state,对状态的管理更加明确，通过 redux，流程更加规范了，减少手动编码量，提高了编码效率，同时缺点时当数据更新时有时候组件不需要，但是也要重新绘制，有些影响效率。一般情况下，我们在构建多交互，多数据流的复杂项目应用时才会使用它们
缺点是：
一个组件所需要的数据，必须由父组件传过来，而不能像 flux 中直接从 store 取。
当一个组件相关数据更新时，即使父组件不需要用到这个组件，父组件还是会重新 render，可能会有效率影响，或者需要写复杂的 shouldComponentUpdate 进行判断。
### 7.调用setState后发生了什么，setState的第二个参数是什么，作用又是什么？
在代码中调用 setState 函数之后，React 会将传入的参数对象与组件当前的状态合并，然后触发所谓的调和过程（Reconciliation）。经过调和过程，React 会以相对高效的方式根据新的状态构建 React 元素树并且着手重新渲染整个 UI 界面。在 React 得到元素树之后，React 会自动计算出新的树与老树的节点差异，然后根据差异对界面进行最小化重渲染。在差异计算算法中，React 能够相对精确地知道哪些位置发生了改变以及应该如何改变，这就保证了按需更新，而不是全部重新渲染。
setState的第二个参数是一个回调函数，组件更新完后执行的回调函数（setState函数是异步的）

### 8.简要介绍一下你所了解的flux架构的思想？
Flux 的最大特点，就是数据的"单向流动"。

用户访问 View
View 发出用户的 Action
Dispatcher 收到 Action，要求 Store 进行相应的更新
Store 更新后，发出一个"change"事件
View 收到"change"事件后，更新页面
### 9.聊一聊你对React的DOM diff算法的理解
把树形结构按照层级分解，只比较同级元素。
给列表结构的每个单元添加唯一的 key 属性，方便比较。
React 只会匹配相同 class 的 component（这里面的 class 指的是组件的名字）
合并操作，调用 component 的 setState 方法的时候, React 将其标记为 dirty.到每一个事件循环结束, React 检查所有标记 dirty 的 component 重新绘制.
选择性子树渲染。开发人员可以重写 shouldComponentUpdate 提高 diff 的性能。


### 10.虚拟DOM的优缺点有哪些？

### 11.说说几个主流框架之间的优缺点，

### 12.MVVM MVC MVP的区别

### 13.props和state的区别，
State 是一种数据结构，用于组件挂载时所需数据的默认值。State 可能会随着时间的推移而发生突变，但多数时候是作为用户事件行为的结果。
Props(properties 的简写)则是组件的配置。props 由父组件传递给子组件，并且就子组件而言，props 是不可变的(immutable)。组件不能改变自身的 props，但是可以把其子组件的 props 放在一起(统一管理)。Props 也不仅仅是数据--回调函数也可以通过 props 传递。
### 14.react的特性，reactV16的新特性

### 15.jsx怎么被解析，具体说说过程，解析JSX用了babel，和web pack的什么插件，

### 16.展示组件(Presentational component)和容器组件(Container component)之间有何不同
展示组件关心组件看起来是什么。展示专门通过 props 接受数据和回调，并且几乎不会有自身的状态，但当展示组件拥有自身的状态时，通常也只关心 UI 状态而不是数据的状态。
容器组件则更关心组件是如何运作的。容器组件会为展示组件或者其它容器组件提供数据和行为(behavior)，它们会调用 Flux actions，并将其作为回调提供给展示组件。容器组件经常是有状态的，因为它们是(其它组件的)数据源。

### 17.类组件(Class component)和函数式组件(Functional component)之间有何不同
类组件不仅允许你使用更多额外的功能，如组件自身的状态和生命周期钩子，也能使组件直接访问 store 并维持状态
当组件仅是接收 props，并将组件自身渲染到页面时，该组件就是一个 '无状态组件(stateless component)'，可以使用一个纯函数来创建这样的组件。这种组件也被称为哑组件(dumb components)或展示组件
