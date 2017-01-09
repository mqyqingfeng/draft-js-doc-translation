Inline and block styles aren't the only kind of rich styling that we might
want to add to our editor. The Facebook comment input, for example, provides
blue background highlights for mentions and hashtags.

内联和块元素样式并不是唯一的富文本样式我们想添加进我们的编辑器。Facebook的评论输入框，举个例子提供对 mentions 和 hashtag 的蓝色高亮背景。

To support flexibility for custom rich text, Draft provides a "decorator"
system. The [tweet example](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/tweet)
offers a live example of decorators in action.

为了支持更复杂的富文本操作，Draft提供了“decorator”系统。[tweet example](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/tweet)提供了一个decorators的例子。

## CompositeDecorator

The decorator concept is based on scanning the contents of a given
[ContentBlock](/draft-js/docs/api-reference-content-block.html)
for ranges of text that match a defined strategy, then rendering them
with a specified React component.

装饰器的实现基于根据已经定义好的策略（比如一个正则）去匹配给定的[ContentBlock](/draft-js/docs/api-reference-content-block.html)中的内容，然后用特定的React组件渲染他们。

You can use the `CompositeDecorator` class to define your desired
decorator behavior. This class allows you to supply multiple `DraftDecorator`
objects, and will search through a block of text with each strategy in turn.

你可以使用`CompositeDecorator`类去定义你想要的装饰器行为。这个类允许你应用多个`DraftDecorator`对象，并且依此执行各自的策略。

Decorators are stored within the `EditorState` record. When creating a new
`EditorState` object, e.g. via `EditorState.createEmpty()`, a decorator may
optionally be provided.

装饰器被储存在`EditorState`中。当创建一个`EditorState`对象，比如说通过`EditorState.createEmpty()`，一个装饰器是可以选择性提供的。

```js
// tweet.html中的例子
const compositeDecorator = new CompositeDecorator([
  {
    strategy: handleStrategy,
    component: HandleSpan,
  },
  {
    strategy: hashtagStrategy,
    component: HashtagSpan,
  },
]);

this.state = {
  editorState: EditorState.createEmpty(compositeDecorator),
};
```

> Under the hood
> 额外说一点
> 
> When contents change in a Draft editor, the resulting `EditorState` object
> will evaluate the new `ContentState` with its decorator, and identify ranges
> to be decorated. A complete tree of blocks, decorators, and inline styles is
> formed at this time, and serves as the basis for our rendered output.
> 
> 当内容改变的时候，最终的`EditorState`对象会用它的装饰器重新计算新的`ContentState`
> ，并且定义要装饰的部分。渲染输出的时候会形成一个完整的有块元素、装饰器、内联元素的树
>
> In this way, we always ensure that as contents change, rendered decorations
> are in sync with our `EditorState`.
> 
> 用这种方式，我们能确保当内容改变的时候，被渲染的装饰器跟`EditorState`实时同步。
> 

In the "Tweet" editor example, for instance, we use a `CompositeDecorator` that
searches for @-handle strings as well as hashtag strings:

在这个“Tweet”编辑器的例子中，举个例子，我们用一个`CompositeDecorator`搜索@-handle字符串和hashtag字符串

```js
const compositeDecorator = new CompositeDecorator([
  {
    strategy: handleStrategy,
    component: HandleSpan,
  },
  {
    strategy: hashtagStrategy,
    component: HashtagSpan,
  },
]);
```

This composite decorator will first scan a given block of text for @-handle
matches, then for hashtag matches.

这个复合的装饰器会首先扫描给定区域内的文字，找完@-handle后会找匹配matches的内容。

```js
// Note: these aren't very good regexes, don't use them!
const HANDLE_REGEX = /\@[\w]+/g;
const HASHTAG_REGEX = /\#[\w\u0590-\u05ff]+/g;

function handleStrategy(contentBlock, callback) {
  findWithRegex(HANDLE_REGEX, contentBlock, callback);
}

function hashtagStrategy(contentBlock, callback) {
  findWithRegex(HASHTAG_REGEX, contentBlock, callback);
}

function findWithRegex(regex, contentBlock, callback) {
  const text = contentBlock.getText();
  let matchArr, start;
  while ((matchArr = regex.exec(text)) !== null) {
    start = matchArr.index;
    callback(start, start + matchArr[0].length);
  }
}
```

The strategy functions execute the provided callback with the `start` and
`end` values of the matching range of text.

这个策略函数会执行提供的回调有着匹配文字的`start`值和`end`值。

## Decorator Components

For your decorated ranges of text, you must define a React component to use
to render them. These tend to be simple `span` elements with CSS classes or
styles applied to them.

为了要装饰部分文字，你必须定义一个用于渲染他们的React component。这些可以是一个带着CSS类或者样式的`span`标签。

In our current example, the `CompositeDecorator` object names `HandleSpan` and
`HashtagSpan` as the components to use for decoration. These are just basic
stateless components:

在我们这个例子中，`CompositeDecorator`对象声明了`HandleSpan` 和 `HashtagSpan` 用于装饰，这些事基础的无状态组件。

```js
const HandleSpan = (props) => {
  return <span {...props} style={styles.handle}>{props.children}</span>;
};

const HashtagSpan = (props) => {
  return <span {...props} style={styles.hashtag}>{props.children}</span>;
};
```

Note that `props.children` is passed through to the rendered output. This is
done to ensure that the text is rendered within the decorated `span`.

注意`props.children`被传递给渲染的输出。这样做是为了确保文字会被装饰后`span`内渲染。

You can use the same approach for links, as demonstrated in our
[link example](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/link).

你可以用相同的方法处理链接，就像[link example](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/link)中那样做的。

### Beyond CompositeDecorator

The decorator object supplied to an `EditorState` need only match the expectations
of the
[DraftDecoratorType](https://github.com/facebook/draft-js/blob/master/src/model/decorators/DraftDecoratorType.js)
Flow type definition, which means that you can create any decorator classes
you wish, as long as they match the expected type -- you are not bound by
`CompositeDecorator`.

装饰器对象被用于`EditorState`，它仅仅需要匹配[DraftDecoratorType](https://github.com/facebook/draft-js/blob/master/src/model/decorators/DraftDecoratorType.js)的类型定义，这意味着你可以创建任何一个你期望的装饰器类，只要他们匹配期望的类型，你将不会被`CompositeDecorator`束缚。

## Setting new decorators

Further, it is acceptable to set a new `decorator` value on the `EditorState`
on the fly, during normal state propagation -- through immutable means, of course.

未来，我们将可以在`EditorState`中设置新的`decorator`值，通过持久化的方法，在正常的状态传递的时候。

This means that during your app workflow, if your decorator becomes invalid or
requires a modification, you can create a new decorator object (or use
`null` to remove all decorations) and `EditorState.set()` to make use of the new
decorator setting.

这意味着在你的app工作流中，如果你的decorator成为无效或者需要一个修改，你可以创建一个新的装饰器对象或者用`null`删除所有的装饰器，并且`EditorState.set()`使用新的装饰器设置。

For example, if for some reason we wished to disable the creation of @-handle
decorations while the user interacts with the editor, it would be fine to do the
following:

举个例子，因为某些原因，我们希望当用户操作编辑器的时候，禁用@-handle装饰器，它会变得很简单，就像下面这样做：
```js
function turnOffHandleDecorations(editorState) {
  const onlyHashtags = new CompositeDecorator([{
    strategy: hashtagStrategy,
    component: HashtagSpan,
  }]);
  return EditorState.set(editorState, {decorator: onlyHashtags});
}
```

The `ContentState` for this `editorState` will be re-evaluated with the new
decorator, and @-handle decorations will no longer be present in the next
render pass.

`editorState`中的`ContentState`会用新的装饰器重新计算，并且@-handle准时器不会在下个渲染中呈现出来。

Again, this remains memory-efficient due to data persistence across immutable
objects.

要再一次说明一下，因为使用持久化数据，它依然会是高效的。