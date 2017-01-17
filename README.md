# Draft.js 中文文档

**项目地址：<https://github.com/mqyqingfeng/draft-js-doc-translation>，欢迎Star和帮忙改进。**

有任何意见或建议，可以到这里提出 [Create New Issue](https://github.com/mqyqingfeng/draft-js-doc-translation/issues/new)

## 说明

文档翻译的是Draft.js `v0.9.1`, 并不仅仅是翻译文档，更希望做到是能通过阅读这些文章，深入了解Draft.js的使用，所以翻译的同时也补充了说明和示例。

项目正在进行中。

## 目录

* [Overview(概述)](https://github.com/mqyqingfeng/draft-js-doc-translation/blob/master/translation/Overview.md)
* API Basics(API基础)
* Rich Styling(富文本样式)
* [Entities(实体)](https://github.com/mqyqingfeng/draft-js-doc-translation/blob/master/translation/Advanced-Topics-Entities.md)
* [Decorators(装饰器)](https://github.com/mqyqingfeng/draft-js-doc-translation/blob/master/translation/Advanced-Topics-Decorators.md)
* [Key Bindings(快捷键)](https://github.com/mqyqingfeng/draft-js-doc-translation/blob/master/translation/Advanced-Topics-Key-Bindings.md)
* [Managing Focus(管理聚焦)](https://github.com/mqyqingfeng/draft-js-doc-translation/blob/master/translation/Advanced-Topics-Managing-Focus.md)
* [Block Styling(块样式)](https://github.com/mqyqingfeng/draft-js-doc-translation/blob/master/translation/Advanced-Topics-Block-Styling.md)
* [Custom Block Rendering(自定义块渲染)](https://github.com/mqyqingfeng/draft-js-doc-translation/blob/master/translation/Advanced-Topics-Custom-Block-Render.md)
* [Custom Block Components(自定义块组件)](https://github.com/mqyqingfeng/draft-js-doc-translation/blob/master/translation/Advanced-Topics-Block-Components.md)
* [Complex Inline Styles(复合内联样式)](https://github.com/mqyqingfeng/draft-js-doc-translation/blob/master/translation/Advanced-Topics-Inline-Styles.md)
* Nested Lists(嵌套列表)
* Text Direction(文字方向)

## 官方文档：

[http://facebook.github.io/draft-js/docs/overview.html#content](http://facebook.github.io/draft-js/docs/overview.html#content)

## 介绍

Draft.js是Facebook发布的是基于React用于构建富文本编辑器的框架，由持久化数据层作为驱动，并且跨浏览器兼容。

Draft.js在2016年2月的[React.js Conf](http://conf.reactjs.com/)公开与众。

[介绍视频](https://www.youtube.com/embed/feUYwoLhE_4)

## 开始使用

```
npm install --save draft-js react react-dom
```

### 使用 Draft.js

```
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