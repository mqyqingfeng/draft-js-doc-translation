The `Editor` component offers flexibility to define custom key bindings
for your editor, via the `keyBindingFn` prop. This allows you to match key
commands to behaviors in your editor component.

通过`keyBindingFn`，`Editor`组件提供自定义按键绑定的功能。它允许你在你的编辑器里能匹配按键去控制你的行为。

### Defaults

The default key binding function is `getDefaultKeyBinding`.

默认的键值绑定函数是`getDefaultKeyBinding`。

Since the Draft framework maintains tight control over DOM rendering and
behavior, basic editing commands must be captured and routed through the key
binding system.

因为Draft框架会维持DOM渲染和行为的强控制，基础的编辑命令通过键值绑定系统一定会被捕捉到。

`getDefaultKeyBinding` maps known OS-level editor commands to `DraftEditorCommand`
strings, which then correspond to behaviors within component handlers.

`getDefaultKeyBinding`映射已知的OS编辑器命令到`DraftEditorCommand`字符串，对应了组件处理函数中的行为。

For instance, `Ctrl+Z` (Win) and `Cmd+Z` (OSX) map to the `'undo'` command,
which then routes our handler to perform an `EditorState.undo()`.

举个例子，`Ctrl+Z` (Win) and `Cmd+Z` (OSX)都映射的是`'undo'`命令，然后引导我们的处理函数执行一个`EditorState.undo()`命令

### Customization

You may provide your own key binding function to supply custom command strings.

你可以提供你自己的键值绑定函数应用于自定义按键字符串。

It is recommended that your function use `getDefaultKeyBinding` as a
fall-through case, so that your editor may benefit from default commands.

推荐你用`getDefaultKeyBinding`作为一个降级例子，这样的话，你的编辑器就会受益于默认的那些命令。

With your custom command string, you may then implement the `handleKeyCommand`
prop function, which allows you to map that command string to your desired
behavior. If `handleKeyCommand` returns `'handled'`, the command is considered
handled. If it returns `'not-handled'`, the command will fall through.

使用自定义的命令字符串，你可以使用`handleKeyCommand`函数，它可以让你将命令字符串对应到你想要的行为。如果`handleKeyCommand`返回`'handled'`，这个命令会被处理，如果返回`'not-handled'`，这个命令会被落空。

### Example

Let's say we have an editor that should have a "Save" mechanism to periodically
write your contents to the server as a draft copy.

让我们举个例子，如果我们希望我们的编辑器有个保存功能，周期性的将内容，相当于一个draft副本记录在服务器中。

First, let's define our key binding function:

首先，让我们定义我们的按键绑定函数

```js
import {getDefaultKeyBinding, KeyBindingUtil} from 'draft-js';
const {hasCommandModifier} = KeyBindingUtil;

function myKeyBindingFn(e: SyntheticKeyboardEvent): string {
  if (e.keyCode === 83 /* `S` key */ && hasCommandModifier(e)) {
    return 'myeditor-save';
  }
  return getDefaultKeyBinding(e);
}
```

Our function receives a key event, and we check whether it matches our criteria:
it must be an `S` key, and it must have a command modifier, i.e. the command
key for OSX, or the control key otherwise.

我们的函数接收一个按键对象，并且我们检查它是否符合我们条件：
如果它是`S`, 并且有一个快捷键，比如在OSX下的command，或者control键

If the command is a match, return a string that names the command. Otherwise,
fall through to the default key bindings.

如果说命令是匹配的，会返回这个命令的名字，否则会返回默认的按键绑定的key

In our editor component, we can then make use of the command via the
`handleKeyCommand` prop:

在我们的编辑器组件中，我们可以利用这个命令通过`handleKeyCommand`

```js
import {Editor} from 'draft-js';
class MyEditor extends React.Component {
  // ...

  handleKeyCommand(command: string): DraftHandleValue {
    if (command === 'myeditor-save') {
      // Perform a request to save your contents, set
      // a new `editorState`, etc.
      return 'handled';
    }
    return 'not-handled';
  }

  render() {
    return (
      <Editor
        editorState={this.state.editorState}
        handleKeyCommand={this.handleKeyCommand.bind(this)}
        keyBindingFn={myKeyBindingFn}
        ...
      />
    );
  }
}
```

The `'myeditor-save'` command can be used for our custom behavior, and returning
`'handled'` instructs the editor that the command has been handled and no more work
is required.

这个`'myeditor-save'`命令可以被用来我们的自定义行为，并且返回`'handled'`指导编辑器命令已经被处理了，并且不再需要更多的东西了。

By returning `'not-handled'` in all other cases, default commands are able to fall
through to default handler behavior.

通过返回`'not-handled'`在其他所有的例子中，默认的命令对应默认的处理行为会失效。
