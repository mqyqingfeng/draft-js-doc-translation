Facebook supports dozens of languages, which means that our text inputs need
to be flexible enough to handle considerable variety.

Facebook支持很多种语言，这意味着我们的文字输入框需要足够的灵活才能处理大量的变化。

For example, we want input behavior for RTL languages such as Arabic and Hebrew
to meet users' expectations. We also want to be able to support editor contents
with a mixture of LTR and RTL text.

举个例子，我们希望支持RTL(Right-to-left)的语言比如阿拉伯语和希伯来语来满足用户的期望。我们也希望支持LTR和RTL混合的编辑内容。

To that end, Draft uses a bidi algorithm to determine appropriate
text alignment and direction on a per-block basis.

为了这个目的，Draft使用了一个双向算法来决定合适的文字对齐方式和基于每个基础部分的方向。

Text is rendered with an LTR or RTL direction automatically as the user types.
You should not need to do anything to set direction yourself.

当用户输入的时候，文字会自动决定LTR和RTL的方向。你不需要做任何设置方向的事情。

## Text Alignment

While languages are automatically aligned to the left or right during composition,
as defined by the content characters, it is also possible for engineers to
manually set the text alignment for an editor's contents.

尽管文字能自动向左或者向右对齐，就行文字内容定义的一样，工程师也是可以手动设置文字方向的。

This may be useful, for instance, if an editor requires strictly centered
contents, or needs to keep text aligned flush against another UI element.

这个也许有用的，举个例子，如果一个编辑器需要一个严格居中的内容或者需一个文字同另外一个UI元素居中对齐。

The `Editor` component therefore provides a `textAlignment` prop, with a
simple set of values: `'left'`, `'center'`, and `'right'`. Using these values,
the contents of your editor will be aligned to the specified direction regardless
of language and character set.

`Editor`组件所以提供一个`textAlignment`,可以设置一系列的值如`'left'`, `'center'`, 和 `'right'`。用这些值的时候，内容会向指定的方向对齐不管语言和文字是什么。