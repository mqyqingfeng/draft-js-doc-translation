Within `Editor`, some block types are given default CSS styles to limit the amount
of basic configuration required to get engineers up and running with custom
editors.

在`Editor`中, 一些block样式会被赋予默认的样式，这是为了减少开发者做的必要的基础配置。

By defining a `blockStyleFn` prop function for an `Editor`, it is possible
to specify classes that should be applied to blocks at render time.

通过为`Editor`定义一个`blockStyleFn`函数，可以在渲染的时候为块元素加上指定的类名。

## DraftStyleDefault.css

The Draft library includes default block CSS styles within
[DraftStyleDefault.css](https://github.com/facebook/draft-js/blob/master/src/component/utils/DraftStyleDefault.css). _(Note that the annotations on the CSS class names are
artifacts of Facebook's internal CSS module management system.)_

These CSS rules are largely devoted to providing default styles for list items,
without which callers would be responsible for managing their own default list
styles.

在[DraftStyleDefault.css](https://github.com/facebook/draft-js/blob/master/src/component/utils/DraftStyleDefault.css)中，包含了默认的块元素样式。注意CSS类名的注释是Facebook内部CSS模块管理系统的上古神器。

这些CSS样式大部分都用于提供列表的默认样式，如果没有它的话，开发者就需要自己管理他们的默认列表样式了。

## blockStyleFn

The `blockStyleFn` prop on `Editor` allows you to define CSS classes to
style blocks at render time. For instance, you may wish to style `'blockquote'`
type blocks with fancy italic text.

`Editor`的`blockStyleFn`允许你在渲染的时候，定义给块元素定义的类名，比如，你可能期望给`'blockquote'`再加上一个花哨的斜体效果

```js
function myBlockStyleFn(contentBlock) {
  const type = contentBlock.getType();
  if (type === 'blockquote') {
    return 'superFancyBlockquote';
  }
}

// Then...
import {Editor} from 'draft-js';
class EditorWithFancyBlockquotes extends React.Component {
  render() {
    return <Editor ... blockStyleFn={myBlockStyleFn} />;
  }
}
```

Then, in your own CSS:
然后，在你自己的CSS文件中：

```css
.superFancyBlockquote {
  color: #999;
  font-family: 'Hoefler Text', Georgia, serif;
  font-style: italic;
  text-align: center;
}
```
