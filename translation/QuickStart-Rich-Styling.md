# 富文本样式快速入门

现在我们已经讲解了顶层API的基础部分，现在我们可以更进一步，并且学习基础的富文本样式如何加入`Draft`编辑器。

[rich text example](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/rich)也是可以学习的。

## EditorState: 你可以用的命令

之前的文章介绍了`Editor`核心会提供一个`onChange`函数，会返回编辑器全部状态的`EditorState`对象。

然而，尽管你的顶层组件可以维护state，但你依然可以以任何你认为合适的方式修改`EditorState`对象。

对于内联和块样式行为，举个例子，[`RichUtils`](/draft-js/docs/api-reference-rich-utils.html) 模块提供了一系列有帮助的函数可以帮助你修改state。

相似的，[Modifier](/draft-js/docs/api-reference-modifier.html)模块也提供了一系列的常用的操作，包括更改文字，样式等等。这个模块就是一系列由简单的微小的的编辑函数组成的一系列编辑函数，为的是返回期望的`EditorState`对象。

在这个例子中，我们用`RichUtils`展示如何在顶层的组件中应用基础的富文本样式。

## RichUtils和快捷键

`RichUtils`可以支持web编辑器可用的核心快捷键命令，比如Cmd+B (bold), Cmd+I (italic)等等。

我们可以通过`handleKeyCommand`，监听和处理快捷键命令并且可以在`RichUtils`中进行关联，以应用或者删除期望的样式。

```js
import {Editor, EditorState, RichUtils} from 'draft-js';

class MyEditor extends React.Component {
  constructor(props) {
    super(props);
    this.state = {editorState: EditorState.createEmpty()};
    this.onChange = (editorState) => this.setState({editorState});
    this.handleKeyCommand = this.handleKeyCommand.bind(this);
  }
  handleKeyCommand(command) {
    const newState = RichUtils.handleKeyCommand(this.state.editorState, command);
    if (newState) {
      this.onChange(newState);
      return 'handled';
    }
    return 'not-handled';
  }
  render() {
    return (
      <Editor
        editorState={this.state.editorState}
        handleKeyCommand={this.handleKeyCommand}
        onChange={this.onChange}
      />
    );
  }
}
```

> handleKeyCommand
>
> `handleKeyCommand`的`command`参数是一个字符串值，作为要执行的命令的名称。这对应了一个DOM key 事件。看[Advanced Topics - Key Binding](/draft-js/docs/advanced-topics-key-bindings.html)获取详细的信息。在这里，是为了解释为什么函数要返回一个handled`或者`not-handled`。

## UI的样式控制

在你的React组件中，你可以添加按钮或者其他的控制形式让用户在编辑中更改样式。在上述这个例子中，我们使用已知的快捷键,但是我们可以加入更加复杂的UI来实现更多的富文本功能。

这是一个超级基础的例子，使用“Bold”按钮切换`BOLD`样式。

```js
class MyEditor extends React.Component {
  // …

  _onBoldClick() {
    this.onChange(RichUtils.toggleInlineStyle(this.state.editorState, 'BOLD'));
  }

  render() {
    return (
      <div>
        <button onClick={this._onBoldClick.bind(this)}>Bold</button>
        <Editor
          editorState={this.state.editorState}
          handleKeyCommand={this.handleKeyCommand}
          onChange={this.onChange}
        />
      </div>
    );
  }
}
```
