# 受控组件和非受控组件


## 1 受控组件

使用 React 时， `<input>` 的 value 存储在 Component 的 state 里，value 受到 React 的 state 控制，只有 state 改变，渲染出来的 DOM 的 value 才改变。

## 2 非受控元素

指 value 存储在 DOM 里，例如 `<input>` 的 value 属性里，没有受到 React 的 state 控制。

## 3 非受控组件

针对 Uncontrolled Elements，React 使用 **`ref`** 对其进行引用，value 依旧是存储在 DOM 里，引用可以看似 `document.querySelector()` 这样的 DOM 操作。

## 4 Codepen 示例

{{< codepen id="PoWegMz" height=500 >}}

## 5 总结

**受控组件** - 体现 React 通过 state 控制元素的思想，数据都是在 React 组件的 state 里，直到被渲染到 DOM 上，DOM 更新也是根据 state 来更新。

**非受控组件** - 使用 `ref` 来对 DOM 进行引用，类似 `document.querySelector()` 。

**核心是：Data 在哪里！**


