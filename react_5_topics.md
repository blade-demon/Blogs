# 你必须了解的有关 React.js 的概念

废话前言：作为一个开发者从入门到进阶每一步都不容易，有人教有人带很幸运，但是没人带我们就需要自己行动起来，用文字纪录我们成长的每一步。

这是一篇翻译自 medium 的一篇文章，我也会在文章中穿插说一些自己的理解和感悟，有不恰当的地方欢迎大家斧正。想阅读原文的童鞋可以点此链接查看阅读（[原文链接](https://medium.freecodecamp.org/these-are-the-concepts-you-should-know-in-react-js-after-you-learn-the-basics-ee1d2f4b8030)）。

华丽分割线以下是本文重点。

---

![](https://user-gold-cdn.xitu.io/2018/11/29/1675de90110b79b1?w=1000&h=526&f=png&s=398645)

你已经学习了你的 React.js 教程，你感觉很好。现在怎么办呢?在下面文章中，我将讨论 5 个概念，它们将把您的 React 的技能和知识提升到一个新的层次。

如果您完全是新手，请花些时间来完成[官方教程](https://reactjs.org/tutorial/tutorial.html)，然后再回来! PS：[React 官网](https://reactjs.org)是最好的 React 的学习进阶资源，所以强烈建议大家有时间多看看，它的内容包括基础知识，底层原理还有最权威的官方更新博客，不要错过哦。甚至很多文章的 demo 都是翻译自那里哦～

## 1. 组件生命周期

在列出的 5 个概念中，这是应该是最重要的概念了。组件的生命周期听起来就像：它详细描述了组件的一生。就像我们人类，组件出生，然后在他的生命中做一些事情，最后狗带了 ☹️。

但是和我们人类不一样的是，组件的生命周期有所不同。下面是的庐山真面目。

![](https://user-gold-cdn.xitu.io/2018/11/29/1675dfd2b7c16955?w=1800&h=1002&f=png&s=294131)

让我们把这个图片分解。每个彩色的水平矩形代表一个生命周期方法(“React updates DOM and ref”除外)。彩色列矩形表示组件生命周期中的不同阶段（挂载阶段，更新阶段，卸载阶段）。

组件在同一时间只能处于一个阶段。它从挂载开始，然后转移到更新阶段。它会一直更新直到被从虚拟 DOM 中移除。然后它会进入卸载阶段并从 DOM 中移除。

### 挂载

由于基于类的的组件其实是由类组成的，所以一个运行的方法其实就是构造函数`constructor`方法。通常，`constructor`就是你将要初始化组件状态的地方。

接下来，组件将要运行`getDerivedStateFromProps`方法。我先跳过这个函数，因为它用途有限。

现在我们来讨论`render`方法，这个方法会返回你的 jsx 代码。现在 React 将会“挂载”到 DOM 上去。

最后，`componentDidMount`方法会运行。在这里，你可以对数据执行异步操作或者在需要时直接操作 DOM。这样，我们的组件就诞生了。

### 更新

这个阶段会因为`state`或者`props`的改变而不断地被触发。就像在挂载阶段，`getDerivedStateFromProps`会被调用。但是在更新阶段构造函数`constructor`方法不会被调用。

接下来`shouldComponentUpdate`方法运行。在这你可以比较旧的 props/state 和新的 props/state。你可以通过返回 true/false 来决定你的组件是否应该重新渲染。返回 false 的话，你的组件就不会重新渲染。通过调用`shouldComponentUpdate`这个函数你可以避免重复渲染来提高你的 web app 的效率。

如果不这样的话，React 会重新渲染并且在这之后运行`getSnapshotBeforeUpdate`函数。其实这个方法也是用途有限。React 接下来会运行 componentDidUpdate。就像 componentDidMount 方法，你可以使用它运行任何异步操作或者操作 DOM。

### 卸载

我们的组件过着不错的生活，但是所有的好的事情会有一个尽头。卸载阶段是组件生命周期的最后一个阶段。当你从 DOM 中移除组件的时候，在组件被移除之前，React 运行 componentWillUnmount 方法。你应该使用方法来清理任何和 WebSockets 或者定时有关的内容。

### 其他生命周期方法

在我们进入下一个主题之前，让我们简要讨论一下`forceUpdate`和`getDerivedStateFromError`。

`forceUpdate` 是一种直接导致重新呈现的方法。虽然它可能有一些用例，但是通常应该避免使用它。

另一方面，`getDerivedStateFromError`是一个生命周期方法，它不是组件生命周期的直接部分。在组件中出现错误时，`getDerivedStateFromError`将运行，可以更新状态以反映发生的错误。应当多使用这种方法。

![](https://user-gold-cdn.xitu.io/2018/11/29/1675e3008469c516?w=751&h=848&f=png&s=105287)

了解 React 的组件生命周期和方法将使您能够维护适当的数据流并处理应用程序中的事件。

PS: 需要注意的是`componentWillMount, componentWillReceiveProps, componentWillUpdate`这三个生命周期函数在 17.x 下个大版本中被移除。但是在当前的 16.x 版本中可以继续使用，但是会有警告。具体可以查看官网的[博客](https://reactjs.org/blog/2018/03/29/react-v-16-3.html)。

![](https://user-gold-cdn.xitu.io/2018/11/29/1675e36952d47fb8?w=729&h=225&f=png&s=43505)

另外刚刚我们有两个函数需要稍微提一下：

`getDerivedStateFromProps` 是最新刚刚加入的函数用来替换`componentWillReceiveProps`函数。大多数情况下我们用不到它，如果想继续深究的话，可以阅读官网的这篇文章[You Probably Don't Need Derived State
](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)。

`getSnapshotBeforeUpdate` 也是最新加入的的函数用来支持安全读取 DOM 的属性，例如读取更新之前的 DOM 信息。

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

如您所见，HOCs 非常有用，因为它们允许我们重用代码并消除膨胀。

## 3. React State 和 setState()

大多数人在应该使用 React 的 state，我们甚至在我们的 HOC 例子的使用过。但是，重要的是理解，当状态发生变化的时候，React 将触发该组件的重新渲染（除非你指定`shouldComponentUpdate`中返回 false）。

现在我们来讨论如何改变状态。您应该更改状态的惟一方法是通过`setState`方法。该方法接受一个对象并将其合并到当前状态。除此之外，还有一些事情你也应该知道。

首先，setState 是异步的。这意味着状态不会在您调用 setState 之后完全更新，这可能会导致一些恼人的行为，我们希望现在能够避免这些行为!

![](https://user-gold-cdn.xitu.io/2018/11/29/1675e85f6f59037b?w=952&h=535&f=png&s=68453)

查看上面的图像，您可以看到我们调用 setState，然后调用 console。日志状态。新的计数器变量应该是 1，但实际上是 0。如果我们想在 setState 更新状态之后立即访问新状态呢?

这将我们带入下一个我们应该知道的关于 setState 的知识，那就是**setState 可以接受一个回调函数作为参数**。让我们修复代码!

![](https://user-gold-cdn.xitu.io/2018/11/29/1675e8a26da9a0ad?w=937&h=559&f=png&s=80275)

很好，成功了，现在我们做完了，对吧?不完全是。这里我们没有正确使用 setState。我们不把对象传递给 setState，而是给它一个函数。当您使用当前状态来设置新状态时，通常使用此模式，如上面的示例所示。如果您不这样做，可以继续将对象传递给 setState。让我们再次更新代码!

![](https://user-gold-cdn.xitu.io/2018/11/29/1675e9ac084641c6?w=871&h=673&f=png&s=92218)

看完了上面的 2 个例子，你肯定会有一个疑问：传递函数而不是对象有什么意义?因为 setState 是异步的，依赖它来创建我们的新值会有一些缺陷。例如，当 setState 运行时，另一个 setState 可能已经改变了状态。传递 setState 函数给我们带来两个好处。首先，**它允许我们获取状态的静态副本，该副本不会自行更改**。第二，**它将对 setState 调用进行排队，以便它们按顺序运行**。

看看下面的例子，我们尝试使用两个连续的 setState 调用将计数器增加 2。

![wtf](https://user-gold-cdn.xitu.io/2018/11/29/1675e9feff3cb93b?w=852&h=641&f=png&s=97149)

上面的代码就是我们之前看到的让人很纳闷的代码及其结果。

![这是我们修改后的代码](https://user-gold-cdn.xitu.io/2018/11/29/1675ea132ee836a6?w=873&h=679&f=png&s=93715)

在第一幅图中，两个 setState 函数都直接使用 this.state。我们之前学过，this.state.counter 在调用第一个 setState 之后，counter 仍然为零。因此，我们得到 1 而不是 2，因为两个 setState 函数都将 counter 设置为 1。

在第二幅图中，我们传递给 setState 一个函数，该函数将保证两个 setState 函数按顺序运行。在此之上，它获取状态快照，而不是使用当前未更新的状态。现在我们得到了 2 这个期望的结果。

[![Edit 30r4mzypp6](https://user-gold-cdn.xitu.io/2018/11/29/1675e6e483dfba6d?w=201&h=42&f=svg&s=21059)](https://codesandbox.io/s/30r4mzypp6)

这就是关于 React state 需要知道的内容。

## 4. React Context

官网的解释是

![](https://user-gold-cdn.xitu.io/2018/11/29/1675eee3e76ef259?w=761&h=89&f=png&s=17528)

翻译成中文就是：**Context 提供了一种通过组件树传递数据的方法，而不必在每个级别手动传递道具**。

通常在 React 应用程序中，数据是通过道具自上而下(父到子)传递的，但对于程序中那些都需要的特定 props 值(例如地区设置首选项、UI 主题)的组件来说，这可能有些麻烦。Context 提供了一种在组件之间共享这些值的方法，而不必显式地在 DOM 树的每一层传递一次 prop 值，我的理解就是避免了**props 下钻（drill-down props）**。

下面的问题就来了，什么时候该使用 Context 呢？

Context 被设计为用于共享可以被认为是“全局”的用于 React 组件树的数据，例如当前经过身份验证的用户、主题或首选语言。例如，在下面的代码中，我们手动地通过传递 theme 来装饰按钮组件:

![](https://user-gold-cdn.xitu.io/2018/11/29/1675f31e9e698169?w=993&h=531&f=png&s=79861)

看完了上面的代码是不是觉得这么做很麻烦，为了传递一个值，中间的组件(ThemedButton 组件)并没有用到 theme 这个值，但是还是要传给他们。

那使用 Context 会带来什么效果呢？

这里是 context 代码的完整代码页的链接。

## 5. 及时更新 React 的知识

最后一个概念可能是最容易理解的。它只是跟上了 React 的最新版本。React 最近做出了一些重大的改变，它只会继续增长和发展。

例如，在 React 16.3 中，某些生命周期方法被弃用，在 React 16.6 中，我们现在得到异步组件，在 16.7 中，我们得到钩子，目的是完全替换类组件。

## 结束语

感谢你的阅读!我希望你喜欢并学到了很多关于 React 的知识。虽然我希望您确实从阅读中学到了很多，但我鼓励您自己尝试所有这些特性/怪癖。读书是一回事，但掌握它的唯一方法是自己动手!

最后，继续编码。学习一项新技术可能看起来令人生畏，但接下来你就会成为 React 的专家。

如果你有任何意见、问题，或者认为我遗漏了什么，请在下面留言。

再次感谢您的阅读!如果喜欢的话请点赞分享，快乐编码。
