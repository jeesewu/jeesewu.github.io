# JavaScript 打字效果


## 1 打字效果

我要把下面的一段原始文本，用 JavaScript 实现打字机效果的动画。

```css
/* Draw a Spider */
.spider {
  width: 400px;
  margin-left: auto;
  margin-right: auto;
}
```

效果如图：

![typewriter-effect](https://raw.githubusercontent.com/jeesewu/my-resource-library/main/uPic/typewriter-effect.gif "纯文本的打字效果")

![typewriter-effect-with-syntax-highlight](https://raw.githubusercontent.com/jeesewu/my-resource-library/main/uPic/typewriter-effect-with-syntax-highlight.gif "语法高亮的文本打字效果")

## 2 文本处理

### 2.1 纯文本

纯文字，直接放在在 `<pre>` 里就行。

```html
<pre>
.spider {
	width: 400px
}
</pre>
```

### 2.2 语法高亮

语法高亮，基本原理是把原始文本的关键词拆分，放进 `<span>` 标签内，搭配 CSS 生效样式。

实现语法高亮的库有 [Prism.js](https://prismjs.com/)、 [highlight.js](https://highlightjs.org/) 等，这里我使用 Prism.js。

生成的 DOM 如下：

```html
<pre>
  <span class="token selector">.spider</span>
  " "
  <span class="token punctuation">{</span>
  " "
  <span class="token property">width</span>
  <span class="token punctuation">:</span>
  " 400px"
  <span class="token punctuation">}</span>
</pre>
```

## 3 打字动画

### 3.1 纯文本

{{< codepen id="WNRRjXa" height=200 >}}

纯文本只需要每秒更新一次 `innerHTML` 就行。

```javascript
let addText = `/* Draw a Spider */
.spider {
  width: 400px;
  margin-left: auto;
  margin-right: auto;
}`;
let i = 0;
let time = 1000;

function typeWriter() {
  if (i < addText.length) {
    document.querySelector("pre").innerHTML += addText.charAt(i);
    i++;
    setTimeout(typeWriter, time);
  }
}
```

### 3.2 语法高亮

{{< codepen id="jOyyBmL" height=200 >}}

语法高亮也是更新 `innerHTML`，但是经过 `Prism.highlight()` 处理。

```javascript
let addText = `/* Draw a Spider */
.spider {
  width: 400px;
  margin-left: auto;
  margin-right: auto;
}`;
let i = 0;
let time = 1000;
let currentText = "";

function typeWriter() {
  if (i < addText.length) {
    currentText += addText.charAt(i);
    const highlightedText = Prism.highlight(currentText, Prism.languages.css);
    document.querySelector("pre").innerHTML = highlightedText;
    i++;
    setTimeout(typeWriter, time);
  }
}
```

{{< admonition >}}

这里不能用 `innerHTML += addText.charAt(i)`，而是用 `currentText`，因为 `innerHTML` 是被语法高亮的 `<span>` 分割并包裹的。

```html
<pre id="code">
	<span class="token comment">/* Draw a Spider */</span>
	...
</pre>
```

{{< /admonition >}}

## 4 改进性能

### 4.1 `insertAdjacentHTML` 比 `innerHTML` 更高效

![element-replace](https://raw.githubusercontent.com/jeesewu/my-resource-library/main/uPic/element-replace.gif "innerHTML 替换整个 pre 节点")

![element-append](https://raw.githubusercontent.com/jeesewu/my-resource-library/main/uPic/element-append.gif "insertAdjacentHTML 插入节点")

`element.innerHTML` 是 [replace]^(替换) 整个节点。

`insertAdjacentHTML` 是 [insert]^(插入) 到节点。

**替换**是要移除整个节点，再放入新节点；而**插入**是在原来的节点里插入的，性能更佳。

{{< admonition tip "更多 [insert]^(插入) 的 API" >}}

[ParentNode.prepend()](https://developer.mozilla.org/en-US/docs/Web/API/ParentNode/prepend)

在父节点的第一个子节点之前插入一系列[`Node`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node)对象或者[`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString)对象。

[ParentNode.append()](https://developer.mozilla.org/zh-CN/docs/Web/API/ParentNode/append)

在父节点的最后一个子节点之后插入一组 [`Node`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node) 对象或 [`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString) 对象。

[Node.appendChild()](https://developer.mozilla.org/en-US/docs/Web/API/Node/appendChild)

将一个节点附加到指定父节点的子节点列表的末尾处。

{{< /admonition >}}

### 4.2 纯文本的 insert

{{< codepen id="ExZZmdK" height=200 >}}

纯文本很简单，因为没有嵌套结构。

```diff
- document.querySelector("pre").innerHTML += addText.charAt(i);
+ document.querySelector("pre").insertAdjacentHTML("beforeend", addText.charAt(i));
```

DOM 结果如下

```html
<pre id="code">
  "."
  "s"
  "p"
  ...
</pre>
```

### 4.3 语法高亮的 insert

{{< codepen id="JjEEJoJ" height=200 >}}

语法高亮比较麻烦，因为要处理嵌套，找出 `text` 节点来分割。

曾经试过用正则表达式来处理，做成一个扁平化的 HTML 文本，如下：

```html
<pre class="code">
  <span class="token comment">.</span>
  <span class="token comment">s</span>
  <span class="token comment">p</span>
</pre>
```

后来发觉 HTML 的嵌套关系太复杂，用正则表达式匹配嵌套的 HTML 似乎是个作死行为😭。

{{< admonition "用正则匹配 HTML 的幻想与现实" >}}

幻想匹配 `<span> 套 <span> 套 文本`

```html
<span>
  <span>H</span>
</span>
```

结果 😭

```html
<span><span>H</span>
</span>
```

{{< /admonition >}}

暂时用 [typewriterjs](https://github.com/tameemsafi/typewriterjs) 吧，有空自己再实现。

## 5 总结

对于 DOM 操作，[insert]^(插入) 往往比 [replace]^(替换) 更高效。

用正则表达式去解析 HTML，大失败！
