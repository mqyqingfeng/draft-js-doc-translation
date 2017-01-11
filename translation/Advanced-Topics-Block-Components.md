Draft is designed to solve problems for straightforward rich text interfaces
like comments and chat messages, but it also powers richer editor experiences
like [Facebook Notes](https://www.facebook.com/notes/).

Draft被设计为快速的解决像评论框和聊天框的富文本界面，但是也可以处理更复杂的富文本编辑效果比如[Facebook Notes](https://www.facebook.com/notes/).

Users can embed images within their Notes, either loading from their existing
Facebook photos or by uploading new images from the desktop. To that end,
the Draft framework supports custom rendering at the block level, to render
content like rich media in place of plain text.

用户可以在他们的Notes中嵌入图片，或者加载已经存在的Facebook图片。为了这个目的，Draft框架支持块层级的渲染，这样就可以支持富媒体而不是纯文字的渲染。

The [TeX editor](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/tex)
in the Draft repository provides a live example of custom block rendering, with
TeX syntax translated on the fly into editable embedded formula rendering via the
[KaTeX library](https://khan.github.io/KaTeX/).

[TeX editor](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/tex)提供了一个使用块渲染的生动的示例，通过[KaTeX library](https://khan.github.io/KaTeX/)，使用的TeX语法会被转成可编辑的嵌入公式。

A [media example](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/media) is also
available, which showcases custom block rendering of audio, image, and video.

[media example](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/media)也是可以用的，它展示了audio,image,和 video是如何自定义渲染的。

By using a custom block renderer, it is possible to introduce complex rich
interactions within the frame of your editor.

通过使用自定义块渲染器，我们就可以支持更加复杂的富文本操作。

## Custom Block Components

Within the `Editor` component, one may specify the `blockRendererFn` prop.
This prop function allows a higher-level component to define custom React
rendering for `ContentBlock` objects, based on block type, text, or other
criteria.

For instance, we may wish to render `ContentBlock` objects of type `'atomic'`
using a custom `MediaComponent`.

在`Editor`中，你可以指定`blockRendererFn`函数。这个函数允许一个高阶组价为一个`ContentBlock`对象自定义React渲染，建立在块类型、文字或者其他的条件。

```js
function myBlockRenderer(contentBlock) {
  const type = contentBlock.getType();
  if (type === 'atomic') {
    return {
      component: MediaComponent,
      editable: false,
      props: {
        foo: 'bar',
      },
    };
  }
}

// Then...
import {Editor} from 'draft-js';
class EditorWithMedia extends React.Component {
  ...
  render() {
    return <Editor ... blockRendererFn={myBlockRenderer} />;
  }
}
```

If no custom renderer object is returned by the `blockRendererFn` function,
`Editor` will render the default `DraftEditorBlock` text block component.

如果`blockRendererFn`函数没有返回自定义的渲染对象，`Editor`会渲染默认的`DraftEditorBlock`块渲染组件。

The `component` property defines the component to be used, while the optional
`props` object includes props that will be passed through to the rendered
custom component via the `props.blockProps` sub property object. In addition,
the optional `editable` property determines whether the custom component is
`contentEditable`.

`component`属性定义了要用的组件，可选的`props`属性包含了通过`props.blockProps`传递给自定义组件的渲染。另外，可选的`editable`属性决定了是否这个自定义组件是`contentEditable`

It is strongly recommended that you use `editable: false` if your custom
component will not contain text.

如果你的自定义组件不包含文字，非常推荐你用`editable: false`

If your component contains text as provided by your `ContentState`, your custom
component should compose a `DraftEditorBlock` component. This will allow the
Draft framework to properly maintain cursor behavior within your contents.

如果你的组件包含有你的`ContentState`提供的文字，你的自定义组件会构建一个`DraftEditorBlock`组件。它会让Draft框架在你的内容中正确的位置光标行为。

By defining this function within the context of a higher-level component,
the props for this custom component may be bound to that component, allowing
instance methods for custom component props.

通过在高阶组件的context中定义这个函数，这个自定义组件中的props会被绑定到这个组件中。允许实例方法自定义组件。

## Defining custom block components

Within `MediaComponent`, the most likely use case is that you will want to
retrieve entity metadata to render your custom block. You may apply an entity
key to the text within a `'atomic'` block during `EditorState` management,
then retrieve the metadata for that key in your custom component `render()`
code.

在`MediaComponent`中，你最可能遇到的情况是你希望获得实体的元数据。你可以在一个`'atomic'`块中使用一个实体的key，然后在自定义组件的`render()`获得这个key对应的元数据。

```js
import {Entity} from 'draft-js';
class MediaComponent extends React.Component {
  render() {
    const {block} = this.props;
    const {foo} = this.props.blockProps;
    const data = Entity.get(block.getEntityAt(0)).getData();
    // Return a <figure> or some other content using this data.
  }
}
```

The `ContentBlock` object is made available within the custom component, along
with the props defined at the top level. By extracting entity information from
the `ContentBlock` and the `Entity` map, you can obtain the metadata required to
render your custom component.

在自定义组件中`ContentBlock`对象是可以获得的，它和props都被定义在最高层。通过从`ContentBlock`和`Entity`中获得实体信息，你就可以获得你需要的元数据去渲染你的组件。

_Retrieving the entity from the block is admittedly a bit of an awkward API,
and is worth revisiting._

从块中获得实体是一个有点棘手的API，但是是值得获得的。

## Recommendations and other notes

If your custom block renderer requires mouse interaction, it is often wise
to temporarily set your `Editor` to `readOnly={true}` during this
interaction. In this way, the user does not trigger any selection changes within
the editor while interacting with the custom block. This should not be a problem
with respect to editor behavior, since interacting with your custom block
component is most likely mutually exclusive from text changes within the editor.

如果你的自定义组件需要获得鼠标的操作，比较明智的做法是在操作的时候，临时设置中你的`Editor`成`readOnly={true}`。在这种方式，当用户操作做自定义组件的时候不会触发任何选中内容的改变，因为操作自定义组件的时候最可能排斥文字的改变，所以这算是一种尊重编辑者行为的方法。

The recommendation above is especially important for custom block renderers
that involve text input, like the TeX editor example.

上述推荐非常的重要的，当你自定义快渲染时会涉及到文字输入框，比如TeX编辑器的例子。

It is also worth noting that within the Facebook Notes editor, we have not
tried to perform any special SelectionState rendering or management on embedded
media, such as rendering a highlight on an embedded photo when selecting it.
This is in part because of the rich interaction provided on the media
itself, with resize handles and other controls exposed to mouse behavior.

在Facebook便条编辑器中，我们也不需要尝试执行任何特殊的选择状态的渲染或者管理一个嵌入的媒体，比如当选中它的时候，渲染一个高亮或者一个嵌入的图片。这部分是因为鼠标操作中的调整大小操作或者其他操作，是媒体本身提供的富文本操作

Since an engineer using Draft has full awareness of the selection state
of the editor and full control over native Selection APIs, it would be possible
to build selection behavior on static embedded media if desired. So far, though,
we have not tried to solve this at Facebook, so we have not packaged solutions
for this use case into the Draft project at this time.

因为一个使用Draft的工程师能充分的意识到编辑器选择的状态并且能完全的控制本地的选择APIs，所以如果你想的话，控制选择的行为以及静态的嵌入媒体是可以实现的，所以至今为止，我们都没有试着去解决这个问题，所以我们也没有任何解决的包。
