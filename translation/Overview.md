## 概述

Draft.js 是一个基于React用于构建富文本编辑器的框架，由持久化数据层作为驱动，并且跨浏览器兼容。

Draft.js是的创建任何类型的文本输入框都变得容易，无论你仅仅是想支持一些内联文本样式还是为了长篇文章构建的复杂编辑器。

Draft.js在2016年2月的[React.js Conf](http://conf.reactjs.com/)公开与众。

<iframe width="650" height="365" src="https://www.youtube.com/embed/feUYwoLhE_4" frameborder="0" allowfullscreen></iframe>

### 安装

现在的Draft.js需要通过npm包安装。它依赖React和React DOM，所以他们也必须要安装。

```sh
npm install --save draft-js react react-dom
```

### API 注意

在开始之前，请注意我们正在更改实体(Entity)存储的API。现在，在主分支上新旧API都支持。我们希望很快就能发布`v0.10.0`。紧接着我们就会发布`v0.11.0`，移除老的API。这个更新也会提供如何升级的文档。如果你想帮助我们，或者追踪这个项目的进展，请看[issue 839](https://github.com/facebook/draft-js/issues/839)。

### 用法

```js
import React from 'react';
import ReactDOM from 'react-dom';
import {Editor, EditorState} from 'draft-js';

class MyEditor extends React.Component {
  constructor(props) {
    super(props);
    this.state = {editorState: EditorState.createEmpty()};
    this.onChange = (editorState) => this.setState({editorState});
  }
  render() {
    return (
        <Editor editorState={this.state.editorState} onChange={this.onChange} />
    );
  }
}

ReactDOM.render(
  <MyEditor />,
  document.getElementById('container')
);
```

因为Draft.js支持unicode，所以你必须在你的HTML文件的`<head></head>`中加入下面的标签。

```html
<meta charset="utf-8" />
```

现在，让我们进入API的基础部分并且学习我们还可以用Draft.js做些什么。
