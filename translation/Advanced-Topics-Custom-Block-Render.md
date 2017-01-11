This article discusses how to customize Draft default block rendering.
The block rendering is used to define supported block types and their respective
renderers, as well as converting pasted content to known Draft block types.

这个文章讨论如何自定义Draft默认块渲染。块渲染被用来定义支持的块类型和他们各自的渲染器，还可以转换粘贴的内容成已知的Draft块类型。

When pasting content or when using the
[convertFromHTML](https://facebook.github.io/draft-js/docs/api-reference-data-conversion.html#convertfromhtml)
Draft will then convert the pasted content to the respective block rendering type
by matching the Draft block render map with the matched tag.

当粘贴内容时或者当用[convertFromHTML](https://facebook.github.io/draft-js/docs/api-reference-data-conversion.html#convertfromhtml)，Draft会转换粘贴的内容成各自的块渲染类型通过匹配Draft块渲染和匹配的标签。

## Draft default block render map

|  HTML element   |            Draft block type             |
| --------------- | --------------------------------------- |
|     `<h1/>`     |               header-one                |
|     `<h2/>`     |               header-two                |
|     `<h3/>`     |              header-three               |
|     `<h4/>`     |               header-four               |
|     `<h5/>`     |               header-five               |
|     `<h6/>`     |               header-six                |
| `<blockquote/>` |               blockquote                |
|    `<pre/>`     |               code-block                |
|   `<figure/>`   |                 atomic                  |
|     `<li/>`     | unordered-list-item,ordered-list-item** |
|    `<div/>`     |               unstyled***               |

\*\* - Block type will be based on the parent `<ul/>` or `<ol/>`

\*\*\* -  Any block that is not recognized by the block redering mapping will be treated as unstyled

\*\* -  块类型会建立在父元素`<ul/>` 或者 `<ol/>`

\*\*\* -  任何一个不会被识别的区块会被认为是unstyled

## Configuring block render map

Draft default block render map can be overwritten, by passing an
[Immutable Map](http://facebook.github.io/immutable-js/docs/#/Map) to
the editor blockRender props.

默认的块渲染可以通过传递[Immutable Map](http://facebook.github.io/immutable-js/docs/#/Map)给编辑器块渲染函数覆盖掉。


*example of overwritting default block render map:*

覆盖默认区块的例子

```js
// The example below deliberatly only allows
// 'heading-two' as the only valid block type and
// updates the unstyled element to also become a h2.
const blockRenderMap = Immutable.Map({
  'header-two': {
   element: 'h2'
  },
  'unstyled': {
    element: 'h2'
  }
});

class RichEditor extends React.Component {
  render() {
    return (
      <Editor
        ...
        blockRenderMap={blockRenderMap}
      />
    );
  }
}
```

There are cases where instead of overwriting the defaults we just want to add new block types;
this can be done by using the DefaultDraftBlockRenderMap reference to create a new blockRenderMap

下面一个例子，与重写默认的不同，我们仅仅是想添加一个新的块类型。

我们可以使用DefaultDraftBlockRenderMap创建一个新的blockRenderMap

*example of extending default block render map:*

扩展默认块渲染的例子

```js
const blockRenderMap = Immutable.Map({
  'section': {
    element: 'section'
  }
});

// Include 'paragraph' as a valid block and updated the unstyled element but
// keep support for other draft default block types
// 段落 会被作为一个有效的区块，并且其他的默认块类型依然会被支持
const extendedBlockRenderMap = Draft.DefaultDraftBlockRenderMap.merge(blockRenderMap);

class RichEditor extends React.Component {
  render() {
    return (
      <Editor
        ...
        blockRenderMap={extendedBlockRenderMap}
      />
    );
  }
}
```

When Draft parses pasted HTML, it maps from HTML elements back into
Draft block types. If you want to specify other HTML elements that map to a
particular block type, you can add an array `aliasedElements` to the block config.

当Draft转义粘贴过来的HTML时，它会把HTMl元素变成Draft块类型。如果你想指定其他的HTMl元素变成一个特有的块类型，你可以在块配置中加一个数据`aliasedElements`

*example of unstyled block type alias usage:*

unstyled的块类型别名的用法

```
'unstyled': {
  element: 'div',
  aliasedElements: ['p'],
}
```

## Custom block wrappers

自定义块的包裹层

By default the html element is used to wrap block types however a react component
can also be provided to the _blockRenderMap_ to wrap the EditorBlock.

默认情况下，html元素会被块类型包裹，尽管一个React元素也会提供一个_blockRenderMap_去包裹EditorBlock

During pasting or when using the
[convertFromHTML](https://facebook.github.io/draft-js/docs/api-reference-data-conversion.html#convertfromhtml)
the html will be scanned for matching tag elements. A wrapper will be used when there is a definition for
it on the _blockRenderMap_ to wrap that particular block type. For example:

当粘贴或者用[convertFromHTML](https://facebook.github.io/draft-js/docs/api-reference-data-conversion.html#convertfromhtml)，html会被搜索其中匹配的标签元素。当有针对某个标签元素特定的块类型，就会使用一个包裹层。举个例子：

Draft uses wrappers to wrap `<li/>` inside either `<ol/>` or `<ul/>` but wrappers can also be used
to wrap any other custom block type

Draft会用wrappers在`<ol/>` 或者 `<ul/>`包裹一个`<li/>`，但是包裹层也可以被用来包裹任何一个自定义的块类型。

*example of extending default block render map to use a react component for a custom block:*

用一个针对自定义块的React组件拓展默认的块渲染映射的例子

```js
class MyCustomBlock extends React.Component {
  constructor(props) {
    super(props);
  }

  render() {
    return (
      <div className='MyCustomBlock'>
        {/* here, this.props.children contains a <section> container, as that was the matching element */}
        {this.props.children}
      </div>
    );
  }
}

const blockRenderMap = Immutable.Map({
  'MyCustomBlock': {
    // element is used during paste or html conversion to auto match your component;
    // it is also retained as part of this.props.children and not stripped out
    element: 'section',
    wrapper: <MyCustomBlock {...this.props} />
  }
});

// keep support for other draft default block types and add our myCustomBlock type
const extendedBlockRenderMap = Draft.DefaultDraftBlockRenderMap.merge(blockRenderMap);

class RichEditor extends React.Component {
  ...
  render() {
    return (
      <Editor
        ...
        blockRenderMap={extendedBlockRenderMap}
      />
    );
  }
}
```
