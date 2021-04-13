# 实用库 FakerJS


## 1 简介

> faker.js - generate massive amounts of fake data in the browser and node.js.
>
> faker.js - 在浏览器和 node.js 中生成大量假数据。

在开发中我们经常需要数据的临时占位，例如头像、背景图片、邮箱、电话等等数据，如果我们去临时找这些资料，会打断开发流程。

[faker.js](https://github.com/marak/Faker.js/) 是解决这个问题的实用库，它可以伪造各种数据，而且使用也非常简单。

## 2 用例

API

```javascript
var randomEmail = faker.internet.email()
```

Codepen 示例

{{< codepen id="wvgybRN" height=200 >}}

每次刷新都能 `fake` 新的数据，非常方便。


