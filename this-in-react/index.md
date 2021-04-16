# React 中的 "this"


## 0 简介

这里写的 React 中的 `this` 问题，其实不是 React 特有，而是 JavaScript 的，我这里仅介绍 React 中常见的情况。

`this` 简而言之就是：Function **call** `this`

更详细的前因后果，我可能会在有空的时候另开新一篇文章。

## 1 this 错误现象

`this` is undefined ? 🤔

```react
/* TypeError: Cannot read property 'setState' of undefined. 🥲 */
class ThisError extends React.Component {
  constructor(props) {
    super(props);
    this.state = { counter: 0 };
  }

  handleClick(event) {
    this.setState((state, props) => {
      return { counter: state.counter + 1 };
    });
  }

  render() {
    return <button onClick={this.handleClick}>Click Me</button>;
  }
}
```

## 2 错误原因

`this` “丢失” - 在回调 `handleClick()` 时，可以看作如下:

```jsx
class ThisError extends React.Component {...}
/* 上面的执行完，再 callback */
handleClick() // 这个 Function Call this 不就是 undefined 么
```

## 3 解决方案

2 种原理：

1. 给 this 绑定 class 中的 this。
2. 箭头函数没有 this，它往上找最近的 this(此处是 class 的 this) 作为自己的 this。

```jsx
/* 1. fix 1 - bind 😃 */
class ThisFix2 extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }
}

/* 2. fix 2 - arrow function 😃 */
class ThisFix2 extends React.Component {
  handleClick = (event) => {
    this.setState();
  };
}

/* 3. fix 3 - arrow function 😆 */
class ThisFix3 extends React.Component {
  render() {
    return (
      <button onClick={(event) => this.handleClick(event)}>Click Me</button>
    );
  }
}
```

## 4 Codepen Example

{{< codepen id="OJWERNK" height=600 >}}

## 5 总结

`this` 其实没啥好说的，就是 `Call` 决定的。

`Callback` 可看作在代码最底部执行（非严谨比喻）。


