# 必须了解的有关 React.js 的概念

前言：作为一个开发者从入门到进阶每一步都不容易，有人教有人带很幸运，但是没人带我们就需要自己行动起来，用文字纪录我们成长的每一步。

华丽分割线以下是本文重点。

---

![1551842544586](C:\Users\ziwei\AppData\Roaming\Typora\typora-user-images\1551842544586.png)

你已经学习了React.js 教程，你感觉很好。现在怎么办呢?在下面文章中，我将讨论 5 个概念，它们将把您的 React 的技能和知识提升到一个新的层次。

如果您完全是新手，请花些时间来完成[官方教程](https://reactjs.org/tutorial/tutorial.html)，然后再回来! PS：[React 官网](https://reactjs.org)是最好的 React 的学习进阶资源，所以强烈建议大家有时间多看看，它的内容包括基础知识，底层原理还有最权威的官方更新博客，不要错过哦。甚至很多文章的 demo 都是翻译自那里～

## 1. 组件生命周期

在列出的 5 个概念中，这是应该是最重要的概念了。组件的生命周期听起来就像：它详细描述了组件的一生。就像我们人类，组件出生，然后在他的生命中做一些事情，最后狗带了 ☹️。

但是和我们人类不一样的是，组件的生命周期有所不同。

![1551842456948](C:\Users\ziwei\AppData\Roaming\Typora\typora-user-images\1551842456948.png)

让我们把这个图片分解。每个彩色的水平矩形代表一个生命周期方法(“React updates DOM and ref”除外)。彩色列矩形表示组件生命周期中的不同阶段（挂载阶段，更新阶段，卸载阶段）。

组件在某一个时间点只能处于一个阶段。它从挂载开始，然后转移到更新阶段，然后它会一直更新直到被从虚拟 DOM 中移除，最后它会进入卸载阶段并从 DOM 中移除。

这个部分我们通过一个简单的demo来验证我们的对上图的理解正确。

```jsx
import React from "react";
import ReactDOM from "react-dom";
import "./styles.css";

class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      date: new Date(2017, 2, 1),
      count: 0
    };
    console.log("constructor()");
  }

  // 新增加的生命周期函数
  static getDerivedStateFromProps(props, state) {
    console.log("getDerivedStateFromProps()", "props:", props, "state:", state);

    return null;
  }
  // 逐步废弃
  // componentWillMount() {
  //   console.log("componentWillMount()");
  // }

  // // 逐步废弃
  // componentWillReceiveProps() {
  //   console.log("componentWillReceiveProps()");
  // }

  // // 逐步废弃
  // componentWillUpdate() {
  //   console.log("componentWillUpdate()");
  // }

  // 新增加的生命周期函数
  getSnapshotBeforeUpdate(prevProps, prevState, snapShot) {
    console.log(
      "getSnapshotBeforeUpdate()",
      "prevProps:",
      prevProps,
      "prevState:",
      prevState
    );
    return null;
  }

  shouldComponentUpdate() {
    if (this.state.count > 10) {
      // console.log("shouldComponentUpdate()");
      return false;
    } else {
      console.log("shouldComponentUpdate()");
      return true;
    }
  }

  componentDidMount() {
    console.log("componentDidMount()");
  }

  componentDidUpdate() {
    console.log("componentDidUpdate()");
  }

  componentWillUnmount() {
    console.log("componentWillUnmount()");
  }

  render() {
    console.log("render()");
    return (
      <div
        className="ui container"
        style={{ padding: "1rem", border: "1px solid" }}
      >
        <h1>当前计数：{this.state.count}</h1>
        <h2>It is {this.state.date.toDateString()}.</h2>
        <button
          className="ui button"
          onClick={() =>
            this.setState({
              count: this.state.count + 1
            })
          }
        >
          更新计数
        </button>
      </div>
    );
  }
}

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      showClock: true,
      inputRef: React.createRef()
    };
  }

  render() {
    return (
      <div className="App ui">
        {this.state.showClock && (
          <Clock date={new Date()} ref={this.state.inputRef} />
        )}
        <div
          className="ui container"
          style={{ padding: "1rem", margin: "1rem", border: "1px solid" }}
        >
          <button
            className="ui button"
            onClick={() => {
              this.setState({
                showClock: !this.state.showClock
              });
            }}
          >
            {this.state.showClock ? "卸载Clock组件" : "加载Clock组件"}
          </button>
        </div>
      </div>
    );
  }
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```



### 挂载

按顺序分别涉及到以下四个函数：

- **constructor()**
- static getDerivedStateFromProps(props, state)
- **render()**
- **componentDidMount()**

由于基于类的的组件其实是由类组成的，所以一个运行的方法其实就是构造函数`constructor`方法。通常，`constructor`就是你将要初始化组件状态的地方。

接下来，组件将要运行`getDerivedStateFromProps`方法，这个方法是用来替换componentWillReceiveProps方法。

然后我们来讨论`render`方法，这个方法会返回你的 jsx 代码，然后 React 将会“挂载”到 DOM 上去，

最后，`componentDidMount`方法会运行。在这里，你可以对数据执行异步操作或者在需要时直接操作 DOM。这样，一个组件就诞生了。

运行上面的demo，输出结果是：

![1551866962419](C:\Users\ziwei\AppData\Roaming\Typora\typora-user-images\1551866962419.png)

Bingo! 完全符合我们的理解。

顺便提一下，有些同学在异步获取数据的时候喜欢把这个步骤放在constructor函数或者render函数中，其实这些策略是不可取的。有两点原因：

- 首先，constructor函数其实只有两个用途：`1. 初始化局部状态;` `2. 绑定事件处理函数；`

  ![1551844166291](C:\Users\ziwei\AppData\Roaming\Typora\typora-user-images\1551844166291.png)

- 其次，从上面的组件生命周期图中我们可以看到constructor函数和render函数都处于“Render 阶段”，这个阶段的操作可能会被 React 暂停，中止或重新启动。我们可以看到，如果不在componentDidMount()中进行获取数据，这个过程可能会被多次执行或者中断。

### 更新

按顺序分别涉及到以下五个函数：

- static getDerivedStateFromProps(props, state)
- shouldComponentUpdate(nextProps, nextState)
- **render()**
- getSnapshotBeforeUpdate(prevProps, prevState)
- **componentDidUpdate(prevProps, prevState, snapshot)**

这个阶段会因为`state`或者`props`的改变而不断地被触发。就像在挂载阶段，`getDerivedStateFromProps`会被调用。但是在更新阶段构造函数`constructor`方法不会被调用。

接下来`shouldComponentUpdate`方法运行。在这你可以比较旧的 props/state 和新的 props/state。你可以通过返回 true/false 来决定你的组件是否应该重新渲染。返回 false 的话，你的组件就不会重新渲染。通过调用`shouldComponentUpdate`这个函数你可以避免重复渲染来提高你的 web app 的效率。如果不这样的话，React 会重新渲染并且在这之后运行`getSnapshotBeforeUpdate`函数，这个函数在最近的一次渲染输出提交给DOM之前进行调用，它能够让组件获得当前DOM中的一些信息（比如滚动位置），这些信息有可能会在提交之后修改当前这些数据，此外这个函数的返回数据可以在下一个生命周期函数componentDidUpdate中被获取到。具体用法可以查看[官方文档](https://reactjs.org/docs/react-component.html#getsnapshotbeforeupdate)。最后，React会运行 componentDidUpdate进行更新。就像 componentDidMount 方法，你可以使用它运行任何异步操作或者操作 DOM。

好了，让我们运行上面的demo，在页面点击`更新计数`按钮

<video src="C:\Users\ziwei\Downloads\84e671a6fc8a447f8a52ed45abaeae94.mp4"></video>

从运行结果来看也是符合我们的预期。

### 卸载

卸载阶段是组件生命周期的最后一个阶段。当你从 DOM 中移除组件的时候，在组件被移除之前，React 运行 componentWillUnmount 方法。

最后的测试，点击页面上的“卸载Clock组件”按钮进行组件卸载。

![1551853492997](C:\Users\ziwei\AppData\Roaming\Typora\typora-user-images\1551853492997.png)

### 其他生命周期方法

在我们进入下一个主题之前，让我们简要讨论一下`forceUpdate`和`getDerivedStateFromError`。

`forceUpdate` 是一种直接导致重新渲染的方法。虽然它可能有一些用例，但是通常应该避免使用它。

`getDerivedStateFromError`是一个生命周期方法，它不是组件生命周期的直接部分。在组件中出现错误时，`getDerivedStateFromError`将运行，可以更新状态以反映发生的错误。应当多使用这种方法。

了解 React 的组件生命周期和方法将使您能够维护适当的数据流并处理应用程序中的事件。

PS: 需要注意的是`componentWillMount, componentWillReceiveProps, componentWillUpdate`这三个生命周期函数在 17.x 下个大版本中被移除。但是在当前的 16.x 版本中可以继续使用，但是会有警告。具体可以查看官网的[博客](https://reactjs.org/blog/2018/03/29/react-v-16-3.html)。

![](https://user-gold-cdn.xitu.io/2018/11/29/1675e36952d47fb8?w=729&h=225&f=png&s=43505)



**在线代码查看**

[![Edit LifeCycle](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/1r0jo8jnk3?fontsize=14)



## 2. 高阶组件（Higher-Order Components）

你可能已经使用了高阶组件，或者 HOCs。例如，Redux 的 connect 函数是一个返回 HOC 的函数。但到底什么是 HOC?

根据 React 的文章介绍：

**A higher-order component is a function that takes a component and returns a new component.**

返回 Redux 的 connect 函数，我们看一下下面的代码片段。

```jsx
const hoc = connect(state => state);
const WrappedComponent = hoc(SomeComponent);
```

当我们调用`connect`方法，我们得到一个可以用来包装组件的特殊的返回。从这里开始，我们只需将组件传递给 HOC，并开始使用 HOC 返回的组件。

HOCs 允许我们将组件之间的共享逻辑抽象为一个包含各种逻辑功能的组件。

HOC 的一个很好的使用场景是授权。您可以在每个需要它的组件中编写身份验证代码。但是，它会迅速而不必要地膨胀您的代码量。令我们不爽的是，这其中逻辑是重复不必要的。让我们先看看不使用高阶组件的代码：

```jsx
class RegularComponent extends Component {
  render() {
    if (this.props.isLoggedIn) {
      return <p>From RegularComponent, hi</p>;
    }
    return <p>From RegularComponent, You're not logged in☹️ ️</p>;
  }
}

class OtherRegularComponent extends Component {
  render() {
    if (this.props.isLoggedIn) {
      return <p>From OtherRegularComponent, hello</p>;
    }
    return <p>From OtherRegularComponent, You're not logged in ☹️</p>;
  }
}

const FunctionalComponent = ({ isLoggedIn }) =>
  isLoggedIn ? (
    <p>From FunctionalComponent, Hi There</p>
  ) : (
    <p>From FunctionalComponent, You're not logged in ☹️</p>
  );

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isLoggedIn: false
    };
  }

  toggleAuth = () => {
    this.setState((prevState, props) => ({
      isLoggedIn: !prevState.isLoggedIn
    }));
  };

  render() {
    const { isLoggedIn } = this.state;
    return (
      <React.Fragment>
        <h1>Hello! React!</h1>
        <button onClick={this.toggleAuth}>
          {isLoggedIn ? "Logout" : "Login"}
        </button>
        <RegularComponent isLoggedIn={isLoggedIn} />
        <OtherRegularComponent isLoggedIn={isLoggedIn} />
        <FunctionalComponent isLoggedIn={isLoggedIn} />
      </React.Fragment>
    );
  }
}
```

如果使用 HOC 的话，你可以这样做：

```jsx
function AuthWrapped(BaseComponent) {
  return props => {
    if (props.isLoggedIn) {
      return <BaseComponent {...props} />;
    }
    return <p>You're not logged in ☹️</p>;
  };
}

class RegularComponent extends Component {
  render() {
    return <p>From RegularComponent, hi</p>;
  }
}

class OtherRegularComponent extends Component {
  render() {
    return <p>From OtherRegularComponent, hello</p>;
  }
}

const FunctionalComponent = () => <p>From FunctionalComponent, Hi there</p>;

const WrappedOne = AuthWrapped(RegularComponent);
const WrappedTwo = AuthWrapped(OtherRegularComponent);
const WrappedThree = AuthWrapped(FunctionalComponent);

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isLoggedIn: false
    };
  }

  toggleAuth = () => {
    this.setState((prevState, props) => ({
      isLoggedIn: !prevState.isLoggedIn
    }));
  };

  render() {
    const { isLoggedIn } = this.state;
    return (
      <React.Fragment>
        <h1>Hello! React!</h1>
        <button onClick={this.toggleAuth}>
          {isLoggedIn ? "Logout" : "Login"}
        </button>
        <WrappedOne isLoggedIn={isLoggedIn} />
        <WrappedTwo isLoggedIn={isLoggedIn} />
        <WrappedThree isLoggedIn={isLoggedIn} />
      </React.Fragment>
    );
  }
}
```

下面是上述代码的工作代码段，点击下面的按钮访问查看效果。[![Edit React-hoc-example](https://user-gold-cdn.xitu.io/2018/11/29/1675e6e483dfba6d?w=201&h=42&f=svg&s=21059)](https://codesandbox.io/s/9y7jp1r604)

查看上面的代码，可以看到我们能够使常规组件非常简单和“愚蠢”，同时仍然为它们提供身份验证。AuthWrapped 组件将所有身份验证逻辑提升到一个统一的组件中。它所做的就是获取一个名为 isLoggedIn 的 prop，并根据该 prop 是否为真返回 BaseComponent 或段落标记。

我们通过这个列子可以看出HOC 非常有用，因为使用它我们可以分离出出重复的代码以此消除代码膨胀。另外，在React v16.8版本中，新增加了Hooks也可以实现同样的效果，通过编写自定义Hooks抽离公用逻辑。具体请查看具体的[官方文档](https://reactjs.org/docs/hooks-intro.html)。

## 3. React State 和 setState()

大多数人在应该使用 React 的 state，我们甚至在我们的 HOC 例子的使用过。但是，重要的是理解，当状态发生变化的时候，React 将触发该组件的重新渲染（除非你指定`shouldComponentUpdate`中返回 false）。

现在我们来讨论如何改变状态。您应该更改状态的惟一方法是通过`setState`方法。该方法接受一个对象并将其合并到当前状态。除此之外，还有一些事情你也应该知道。

首先，setState 是异步的。这意味着状态不会在您调用 setState 之后完全更新，这可能会导致一些恼人的行为，我们希望现在能够避免这些行为!

![1551871037418](C:\Users\ziwei\AppData\Roaming\Typora\typora-user-images\1551871037418.png)

查看上面的图像，您可以看到我们调用 setState，然后打印。新的计数器变量应该打印出来是` 1`，但实际上是`0`。如果我们想在 setState 更新counter状态之后立即访问counter新状态呢?

我们应该知道的关于 setState 的知识就是**setState可以接受一个回调函数作为它的第二个参数**。让我们修改代码!

![1551871094852](C:\Users\ziwei\AppData\Roaming\Typora\typora-user-images\1551871094852.png)

很好，成功了，我们在回调函数中获得了更新后的counter的值。但是我们其实还没有完全正确使用 setState。我们不把对象作为第一个参数传递给 setState，而是给它传递一个函数，当使用当前状态来设置新状态时，我们经常这么做。让我们再次更新代码!

![1551871130438](C:\Users\ziwei\AppData\Roaming\Typora\typora-user-images\1551871130438.png)

看完了上面的 2 个例子，你肯定会有一个疑问：传递函数而不是对象有什么意义?因为 setState 是异步的，依赖它来创建我们的新值会有一些陷进。例如，当 setState 运行时，另一个 setState 可能已经改变了状态。传递 setState 函数给我们带来两个好处。首先，**它允许我们获取状态的静态副本，该副本不会自行更改**。第二，**它将对 setState 调用进行排队，以便它们按顺序运行**。

看看下面的例子，我们尝试使用两个连续的 setState 调用将计数器增加 2。

![1551870852025](C:\Users\ziwei\AppData\Roaming\Typora\typora-user-images\1551870852025.png)

上面的代码就是我们之前看到的让人很纳闷的代码及其结果。因为两个 setState 函数都直接使用 this.state。我们之前学过，this.state.counter 在调用第一个 setState 之后，counter 仍然为零。因此，我们得到 1 而不是 2，因为两个 setState 函数都将 counter 设置为` 1`。

![1551870899136](C:\Users\ziwei\AppData\Roaming\Typora\typora-user-images\1551870899136.png)

这一次，我们传递给 setState 一个函数，该函数将保证两个 setState 函数按顺序运行。在此之上，它获取状态快照，而不是使用当前未更新的状态。现在我们得到了 `2 `这个期望的结果。

[![Edit 30r4mzypp6](https://user-gold-cdn.xitu.io/2018/11/29/1675e6e483dfba6d?w=201&h=42&f=svg&s=21059)](https://codesandbox.io/s/30r4mzypp6)

这就是关于 React state 需要知道的内容。

## 4. React Context

官网的解释是

![](https://user-gold-cdn.xitu.io/2018/11/29/1675eee3e76ef259?w=761&h=89&f=png&s=17528)

翻译成中文就是：**Context 提供了一种通过组件树传递数据的方法，而不必在每个级别手动传递props**。

通常在 React 应用程序中，数据是通过道具自上而下(父到子)传递的，但对于程序中那些都需要的特定 props 值(例如地区设置首选项、UI 主题)的组件来说，这可能有些麻烦。Context 提供了一种在组件之间共享这些值的方法，而不必显式地在 DOM 树的每一层传递一次 prop 值，我的理解就是避免了**props 下钻（drill-down props）**。

所以我们应该如何使用Context呢？

首先，创建一个context对象：

```js
const ContextObject = React.createContext({ foo: "bar" });
```

React 官网上描述在组件上如何设置context。

```js
MyClass.contextType = MyContext;
```

然而在React(16.4.2)中这样用貌似有问题。所以，我们使用HOC来使用context。

```jsx
function contextWrapper(WrappedComponent, Context) {
  return class extends React.Component {
    render() {
      return (
        <Context.Consumer>
          { context => <WrappedComponent context={context} { ...this.props } /> }
        </Context.Consumer>
      )
    }
  }
}
```

我们所做的是用Context.Consumer组件来包装组件，并作为props传入context。

所以我们可以这么写：

```jsx
class Child extends React.Component {
  render() {
    console.log(this.props.context)
    return <div>Child</div>
  }
}
const ChildWithContext = contextWrapper(Child, AppContext)
```

这样我们就可以从props中的context对象中获得`foo`的值。

你可能会问，我们如何改变Context。不幸的是，它有点复杂，但我们可以再次使用HOC，它可能看起来像这样:

```jsx
function contextProviderWrapper(WrappedComponent, Context, initialContext) {
  return class extends React.Component {
    constructor(props) {
      super(props)
      this.state = { ...initialContext }
    }
    
    // define any state changers
    changeContext = () => {
      this.setState({ foo: 'baz' })
    }

    render() {
      return (
        <Context.Provider value={{
          ...this.state,
          changeContext: this.changeContext
        }} >
          <WrappedComponent />
        </Context.Provider>
      )
    }
  }
}
```

让我们一步一步来。首先，我们获取初始context状态，即传递给`React.createcontext()`的对象，并将其设置为包装器组件的状态。接下来我们定义任何用来改变状态的方法。最后，我们在上下文中包装组件`Context.Provider`组件。我们将状态和函数传递给值prop。现在，当用上下文包装时，任何子元素都将在上下文中获得这些`Context.Consumer`组件。

最终的代码为：

```jsx
const initialContext = { foo: 'bar' }
const AppContext = React.createContext(initialContext);

class Child extends React.Component {
  render() {
    return (
      <div>
        <button onClick={this.props.context.changeContext}>Click</button>
        {this.props.context.foo}
      </div>
     )
  }
}

const ChildWithContext = contextConsumerWrapper(Child, AppContext)
const ChildWithProvide = contextProviderWrapper(ChildWithContext, AppContext, initialContext)

class App extends React.Component {
  render() {
    return (
      <ChildWithProvide />
    );
  }
}
```



现在我们的子组件可以访问全局上下文。它能够将状态中的foo属性更改为baz。

下面是上下文代码的完整代码页的[链接]()。



## 5. 及时更新 React 知识

最后一个概念可能是最容易理解的。它只是跟上了 React 的最新版本。React 最近做出了一些重大的改变，它会继续增长和发展。

例如，在 React 16.3 中，某些生命周期方法被弃用，在 React 16.6 中，我们现在得到异步组件，在 16.8 中又新增加了Hooks，可以完全不需要使用类组件。

有时间可以多刷一刷网上的视频。

- [React的官网](https://reactjs.org)（不需要翻墙）

- [React和Redux的作者Dan Abramov的技术博客](https://overreacted.io/) （不需要翻墙）

- Github上面React相关技术的文档（不需要翻墙）

- Medium上面的相关技术博客 （需要翻墙）

- Twitter上面很多React技术大牛（需要翻墙）

- Youtube上面React Conf的技术大会视频（需要翻墙）

  

## 结束语

感谢你的阅读!我希望你喜欢并学到了很多关于 React 的知识。虽然我希望您确实从阅读中学到了很多，但我鼓励您自己尝试所有这些特性/怪癖。读书是一回事，但掌握它的唯一方法是自己动手!

最后，继续编码。学习一项新技术可能看起来令人生畏，但接下来你就会成为 React 的专家。

如果你有任何意见、问题，或者认为我遗漏了什么，请在下面留言。

再次感谢您的阅读!如果喜欢的话请点赞分享，快乐编码。
