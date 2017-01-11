Within your editor, you may wish to provide a wide variety of inline style
behavior that goes well beyond the bold/italic/underline basics. For instance,
you may want to support variety with color, font families, font sizes, and more.
Further, your desired styles may overlap or be mutually exclusive.

The [Rich Editor](http://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/rich) and
[Colorful Editor](http://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/color)
examples demonstrate complex inline style behavior in action.

在你的编辑器中，除了基本的加粗、斜体、下划线样式之外，你也许希望提供更多类型的内联样式。举个例子，你也许希望支持颜色、字体、字体大小或者更多。更多的需求，比如你期望实现的样式是重叠或者互相排斥。

### Model

Within the Draft model, inline styles are represented at the character level,
using an immutable `OrderedSet` to define the list of styles to be applied to
each character. These styles are identified by string. (See [CharacterMetadata](/draft-js/docs/api-reference-character-metadata.html)
for details.)

在Draft模型中，内联样式是以字符串的级别呈现，使用一个持久化的`OrderedSet`定义应用到每个字符串上的各种样式。这些样式以字符串的形式被定义。

For example, consider the text "Hello **world**". The first six characters of
the string are represented by the empty set, `OrderedSet()`. The final five
characters are represented by `OrderedSet.of('BOLD')`. For convenience, we can
think of these `OrderedSet` objects as arrays, though in reality we aggressively
reuse identical immutable objects.

举个例子，比如文字"Hello **world**"。前6个字符的`OrderedSet()`被设置为空，剩下的5个字符串被设置为`OrderedSet.of('BOLD')`。为了方便，我们可以认为这些`OrderedSet`对象是一个数组，尽管实际上我们侵略性的重复使用了一样的持久化对象。

In essence, our styles are:

本质上，我们的样式是：

```js
[
  [], // H
  [], // e
  ...
  ['BOLD'], // w
  ['BOLD'], // o
  // etc.
]
```

### Overlapping Styles

Now let's say that we wish to make the middle range of characters italic as well:
"He_llo **wo**_**rld**". This operation can be performed via the
[Modifier](/draft-js/docs/api-reference-modifier.html) API.

现在我们希望让中间的文字变成斜体，这个操作可以通过[Modifier](/draft-js/docs/api-reference-modifier.html) API实现。

The end result will accommodate the overlap by including `'ITALIC'` in the
relevant `OrderedSet` objects as well.

最终的结果是会在对应的`OrderedSet`对象中加入`'ITALIC'`实现样式的相容。

```js
[
  [], // H
  [], // e
  ['ITALIC'], // l
  ...
  ['BOLD', 'ITALIC'], // w
  ['BOLD', 'ITALIC'], // o
  ['BOLD'], // r
  // etc.
]
```

When determining how to render inline-styled text, Draft will identify
contiguous ranges of identically styled characters and render those characters
together in styled `span` nodes.

当决定如何渲染内联样式的文字，Draft会确定相邻的一样样式的字符串，然后渲染这些字符串在一个存在样式的`span`节点中。

### Mapping a style string to CSS

By default, `Editor` provides support for a basic list of inline styles:
`'BOLD'`, `'ITALIC'`, `'UNDERLINE'`, and `'CODE'`. These are mapped to simple CSS
style objects, which are used to apply styles to the relevant ranges.

默认中，`Editor`为一些基础的内联样式提供支持，比如`'BOLD'`, `'ITALIC'`, `'UNDERLINE'`, 和 `'CODE'`。这些会被映射为普通的CSS样式对象，他们会被用于给对应的文字添加样式。

For your editor, you may define custom style strings to include with these
defaults, or you may override the default style objects for the basic styles.

为了你的编辑器，你可以在默认样式上添加新的自定义字符串样式，也可以覆盖以前的默认样式。

Within your `Editor` use case, you may provide the `customStyleMap` prop
to define your style objects. (See
[Colorful Editor](http://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/color)
for a live example.)

在你开发编辑器的时候，你可以提供一个`customStyleMap`来定义你的样式对象。看[Colorful Editor](http://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/color)

For example, you may want to add a `'STRIKETHROUGH'` style. To do so, define a
custom style map:

举个例子，如果你想加一个`'STRIKETHROUGH'`样式，为了这样做，定义一个自定义样式映射：

```js
import {Editor} from 'draft-js';

const styleMap = {
  'STRIKETHROUGH': {
    textDecoration: 'line-through',
  },
};

class MyEditor extends React.Component {
  // ...
  render() {
    return (
      <Editor
        customStyleMap={styleMap}
        editorState={this.state.editorState}
        ...
      />
    );
  }
}
```

When rendered, the `textDecoration: line-through` style will be applied to all
character ranges with the `STRIKETHROUGH` style.

当渲染的时候，`textDecoration: line-through`的样式就会被应用到带`STRIKETHROUGH`样式的字符串中。
