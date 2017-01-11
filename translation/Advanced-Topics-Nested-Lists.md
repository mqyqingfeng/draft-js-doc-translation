The Draft framework provides support for nested lists, as demonstrated in the
Facebook Notes editor. There, you can use `Tab` and `Shift+Tab` to add or remove
depth to a list item.

Draft提供一个嵌套列表，就像Facebook Notes 编辑器中展示的一样。而且，你可以使用`Tab` 和 `Shift+Tab`添加或删除一个层级。

The [`RichUtils`](/draft-js/docs/api-reference-rich-utils.html) module provides a handy `onTab` method that manages this
behavior, and should be sufficient for most nested list needs. You can use
the `onTab` prop on your `Editor` to make use of this utility.

[`RichUtils`](/draft-js/docs/api-reference-rich-utils.html) 模块提供了一个可以便利的`onTab`方法可以管理这个行为。并且对于大部分的嵌套列表需求都是足够的。你可以给你的`Editor`传递一个`onTab`函数使用这个功能。

By default, styling is applied to list items to set appropriate spacing and
list style behavior, via `DraftStyleDefault.css`.

默认的，在`DraftStyleDefault.css`中，给列表设置了合适的空格和列表样式行为。

Note that there is currently no support for handling depth for blocks of any type
except `'ordered-list-item'` and `'unordered-list-item'`.

注意现在还不支持除`'ordered-list-item'` 和 `'unordered-list-item'`之外的块级元素深度的处理。