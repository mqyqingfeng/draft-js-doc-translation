## API基础快速入门

这篇文章提供`Draft`API的基础概览。[示例代码](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/plaintext)中也可以看到。

### 受控输入框（Controlled Inputs）

`Editor`React组件被构建陈明刚一个受控的可编辑的组件。它的目标是提供顶层的API，跟React的*受控输入框（controlled input）*API很像。

作为一个简单的介绍，受控输入框包含两个关键的部分：

1. 一个呈现输入框的状态的_value_
2. 一个接受输入框改变的_onChange_prop函数

这种方法可以让输入框构成的组件能够严格控制输入框的state,同时依然可以更新DOM提供用户写的内容信息。

```js
class MyInput extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};
    this.onChange = (evt) => this.setState({value: evt.target.value});
  }
  render() {
    return <input value={this.state.value} onChange={this.onChange} />;
  }
}
```

顶层组件通过`value`state值可以控制输入框的state。

## 施控的富文本

在一个富文本编辑器解决方案中，有两个要解决的问题：

1. 纯文本的空字符串是不足以呈现一个富文本编辑器的复杂的状态的

2. 在一个可编辑的元素中没有类似`onChange`的事件是可用的。

因此状态值会以一个持久化的[EditorState](/draft-js/docs/api-reference-editor-state.html)形式呈现，并且在`Editor`中会使用`onChange`来给顶层提供状态值。

`EditorState`就像是编辑器状态值的副本，包括了内容，光标，以及撤销/反撤销历史。所有内容和选区的改变都会导致编辑器产生一个新的`EditorState`对象。注意它之所以能高效是因为使用不可变对象所带来的数据持久化性。

```js
import {Editor, EditorState} from 'draft-js';

class MyEditor extends React.Component {
  constructor(props) {
    super(props);
    this.state = {editorState: EditorState.createEmpty()};
    this.onChange = (editorState) => this.setState({editorState});
  }
  render() {
    return <Editor editorState={this.state.editorState} onChange={this.onChange} />;
  }
}
```

对于出现在编辑器DOM中任何编辑内容或者选区的改变，你的`onChange`处理函数都会执行有这些改变产生的最新的`EditorState`对象。