#一，条件渲染

##1.1 `v-if`

类似于字符串模板如 Handlebars，可以用条件块。

    <!-- Handlebars 模板 -->
    {{#if ok}}
      <h1>Yes</h1>
    {{/if}}

在 Vue中，可以是用 v-if 指令和 v-else 指令实现：

    <h1 v-if="ok">ok</h1>
    <h1 v-else>no</h1>

### `<template>` 中 `v-if` 条件组

因为 `v-if` 是一个指令，需要将它添加到一个元素上。但是如果我们想切换多个元素呢？此时我们可以把一个 `<template>` 元素当做包装元素，并在上面使用 `v-if`，最终的渲染结果不会包含它。

        <template v-if="ok">
            <h1>Title</h1>
            <p>Paragraph 1</p>
            <p>Paragraph 2</p>
        </template>
        
##1.2 `v-else`

可以用 `v-else` 指令给 v-if 添加一个 “else” 块:

    <div v-if="Math.random() > 0.5">
      Sorry
    </div>
    <div v-else>
      Not sorry
    </div>

注意：`v-else` 元素必须紧跟在 `v-if` 元素或 `v-else-if` 元素后面。

##1.3 `v-else-if`
用作 `v-if` 的 `else-if` 块。可以链式的多次使用

    <div v-if="type === 'A'">
      A
    </div>
    <div v-else-if="type === 'B'">
      B
    </div>
    <div v-else-if="type === 'C'">
      C
    </div>
    <div v-else>
      Not A/B/C
    </div>

##1.4 使用 key 控制元素的可重用

Vue 尝试尽可能高效的渲染元素，通常会复用已有元素而不是从头开始渲染。

    <template v-if="loginType === 'username'">
      <label>Username</label>
      <input placeholder="Enter your username">
    </template>
    <template v-else>
      <label>Email</label>
      <input placeholder="Enter your email address">
    </template>
  
在代码中切换 loginType 不会删除用户已经输入的内容，两个模版由于使用了相同的元素，`input` 输入的内容会被复用，仅仅是替换了他们的 `placeholder`。

Vue 提供一种方式让你可以自己决定是否要复用元素。你要做的是添加一个属性 `key` ，`key` 必须带有唯一的值。

    <template v-if="loginType === 'username'">
      <label>Username</label>
      <input placeholder="Enter your username" key="username-input">
    </template>
    <template v-else>
      <label>Email</label>
      <input placeholder="Enter your email address" key="email-input">
    </template>

注意, `<label>` 元素仍然会被复用，因为没有被添加了 `key` 属性

##1.5 `v-show`

根据条件展示元素的选项是 `v-show` 指令

    <h1 v-show="ok">Hello!</h1>

`v-show `的元素会始终渲染并保持在 DOM 中。`v-show` 是简单的切换元素的 CSS 属性 `display `
注意: `v-show` 不支持 `<template>` 语法

##1.6 `v-show` vs `v-if`

一般来说， `v-if` 有更高的切换消耗而 `v-show` 有更高的初始渲染消耗。因此，如果需要频繁切换使用 `v-show` 较好，如果在运行时条件不大可能改变则使用 `v-if` 较好。














