# iPhone 的 <Input> 视口缩放现象


## 1 先来看看缩放现象

{{< codepen id="eYgLOLj" height=300 >}}

iPhone 上的浏览器  Safari\Chrome\Firefox，会出现如下现象：

- 当聚焦的元素的字体大小 `>=` 16px 时，`Viewport` 不缩放。
- 当聚焦的元素的字体大小 `<` 16px 时，`Viewport` 缩放。

这个操作貌似是 Apple 为了照顾视障人士的体贴设定，但是仅对 iPhone 生效，iPad 和 Mac 都不会出现这样的现象哦。🤔

当我们有小于 16px 的 `<input>` 元素时，点进 `<input>`，`Viewport` 放大，可以理解。

但是表单提交后，`<input>` 失去 `focus` 了，Apple 却不把 `Viewport` 缩放回原来的大小，这就蛋疼了。

## 2 看看网上的吐槽和解决方案

[No input zoom in Safari on iPhone, the pixel perfect way](https://thingsthemselves.com/no-input-zoom-in-safari-on-iphone-the-pixel-perfect-way/)

这篇文章很贴心地对这样的现象分析了前因后果，顺便还提供自己的解决方案。

总结来说，有 3 种解决方案：

1. 把 font-size 设置为16px

2. `<meta name="viewport">` 禁止 `Viewport` 缩放
   1.  `maximum-scale=1.0` 
   2.  `user-scalable=no`

3. 伪造 16px

   原始

   ```css
   input[type="text"] {
       /* styles to be adjusted */
       border-radius: 5px;
       font-size: 12px;
       line-height: 20px;
       padding: 5px;
       width: 100%;
   }
   ```

   修改

   ```css
   input[type="text"] {
       /* enlarge by 16/12 = 133.33% */
       border-radius: 6.666666667px;
       font-size: 16px;
       line-height: 26.666666667px;
       padding: 6.666666667px;
       width: 133.333333333%;
   }
   ```

## 3 总结

从视障关怀的角度，我可以接受缩放，我会说 `Good Job, Apple!`

从页面设计者的角度看，元素失去 `focus` 还不复原 `Vieport` 的操作，我会说 `FXXX!`


