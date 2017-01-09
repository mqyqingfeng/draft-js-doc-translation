
This article discusses the Entity system, which Draft uses for annotating
ranges of text with metadata. Entities enable engineers to introduce levels of
richness beyond styled text to their editors. Links, mentions, and embedded
content can all be implemented using entities.

这篇文章讨论实体系统，是Draft用来把元数据给一系列的文字添加注释的东西。实体允许工程师引入更多层级的富度在样式的文本之外。链接、mention即@功能，以及嵌入的内容都可以用实体实现

In the Draft repository, the
[link editor](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/link)
and
[entity demo](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/entity)
provide live code examples to help clarify how entities can be used, as well
as their built-in behavior.

在Draft的仓库中，[link editor](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/link)
和
[entity demo](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/entity)
都提供了可以参考的代码来让大家了解实体是怎么使用的，就像那些内置的功能一样。

The [Entity API Reference](/draft-js/docs/api-reference-entity.html) provides
details on the static methods to be used when creating, retrieving, or updating
entity objects.

[Entity API Reference](/draft-js/docs/api-reference-entity.html)提供了具体的内容在静态的方法被用于当创建、取回或者更新实体对象

## Introduction

An entity is an object that represents metadata for a range of text within a
Draft editor. It has three properties:

一个实体是一个描述了元数据如何注释一系列文字的对象。它有三个属性：

- **type**: A string that indicates what kind of entity it is, e.g. `'LINK'`,
`'MENTION'`, `'PHOTO'`.
- **mutability**: Not to be confused with immutability a la `immutable-js`, this
property denotes the behavior of a range of text annotated with this entity
object when editing the text range within the editor. This is addressed in
greater detail below.
- **data**: An optional object containing metadata for the entity. For instance,
a `'LINK'` entity might contain a `data` object that contains the `href` value
for that link.

- **type**: 一个字符串表明这是什么类型的实体。
- **mutability**: 不要跟`immutable-js弄混了，这个属性只是表明了在编辑文字时实体对象的行为，下面有更详细的解释。
- **data**: 一个可选的参数包含了这个实体的元数据，举个例子，一个`'LINK'`实体包含了一个`data`对象，而data对象又包含了这个link的`href`值。


All entities are stored in a single object store within the `Entity` module,
and are referenced by key within `ContentState` and React components used to
decorate annotated ranges. _(We are considering future changes to bring
the entity store into `EditorState` or `ContentState`.)_

所有的实体都被存储在`Entity`模块下的一个单独的对象，并且可以在`ContentState`中使用key来引用，并且React组件可以用来装饰被注释的范围。

Using [decorators](/draft-js/docs/advanced-topics-decorators.html) or
[custom block components](/draft-js/docs/advanced-topics-block-components.html), you can
add rich rendering to your editor based on entity metadata.

使用 [decorators](/draft-js/docs/advanced-topics-decorators.html)或者[custom block components](/draft-js/docs/advanced-topics-block-components.html)，你可以在实体的元数据基础上更加丰富的渲染到你的编辑器中。

## Creating and Retrieving Entities

Entities should be created using `Entity.create`, which accepts the three
properties above as arguments. This method returns a string key, which can then
be used to refer to the entity.

实体必须用`Entity.create`创建，接收上述的三个参数。这个方法返回一个字符串格式的key，可以用来引用到这个实体。

This key is the value that should be used when applying entities to your
content. For instance, the `Modifier` module contains an `applyEntity` method:

这个key应该被用来当需要将实体应用到内容的时候，比如 `Modifier`模块包含了一个`applyEntity`方法

```js
const key = Entity.create('LINK', 'MUTABLE', {href: 'http://www.zombo.com'});
const contentStateWithLink = Modifier.applyEntity(
  contentState,
  selectionState,
  entityKey
);
```

For a given range of text, then, you can extract its associated entity key by using
the `getEntityAt()` method on a `ContentBlock` object, passing in the target
offset value.

对于一个给定范围的文字，你可以用`ContentBlock`对象的`getEntityAt()`方法，传一个下标值，精确地找出它的实体key。

```js
const blockWithLinkAtBeginning = contentState.getBlockForKey('...');
const linkKey = blockWithLinkAtBeginning.getEntityAt(0);
const linkInstance = Entity.get(linkKey);
const {href} = linkInstance.getData();
```

## "Mutability"

Entities may have one of three "mutability" values. The difference between them
is the way they behave when the user makes edits to them.

实体的"mutability"值三选一，它们之间的区别体现在当用户编辑他们的时候的行为。

Note that `DraftEntityInstance` objects are always immutable Records, and this
property is meant only to indicate how the annotated text may be "mutated" within
the editor. _(Future changes may rename this property to ward off potential
confusion around naming.)_

注意`DraftEntityInstance`对象一直都是持久化的记录，并且这个属性意味着只能表明带注释的文字在编辑器里是如何突变的。

### Immutable

This text cannot be altered without removing the entity annotation
from the text. Entities with this mutability type are effectively atomic.

如果不从文字中删除整个实体，文字是不能被改变的。带这个属性的实体都是一个完整的粒子。
For instance, in a Facebook input, add a mention for a Page (i.e. Barack Obama).
Then, either add a character within the mentioned text, or try to delete a character.
Note that when adding characters, the entity is removed, and when deleting character,
the entire entity is removed.

举个例子，在Facebook的输入框中，添加一个@功能，比如(@ mqyqingfeng)。然后，增加一个字符或者删掉一个字符，注意每当增加字符的时候，实体被删除，当删除字符的时候 ，整个实体被删除。

This mutability value is useful in cases where the text absolutely must match
its relevant metadata, and may not be altered.

这个属性值非常有用当文字必须完全匹配它的相关的元数据，而且不能被改变。

### Mutable

This text may be altered freely. For instance, link text is
generally intended to be "mutable" since the href and linkified text are not
tightly coupled.

文字可以自由的改变。举个例子，链接的文字是可以设置成"mutable"的，因为链接的地址和文字不是强关联的。

### Segmented

Entities that are "segmented" are tightly coupled to their text in much the
same way as "immutable" entities, but allow customization via deletion.

实体分段像"mutable"实体一样强关联对应的文字，但是允许自定义的删除

For instance, in a Facebook input, add a mention for a friend. Then, add a
character to the text. Note that the entity is removed from the entire string,
since your mentioned friend may not have their name altered in your text.

举个例子，在Facebook的输入框中，@一个朋友，然后，给这个名字添加一个字符。注意，实体会被从整个字符串中移除，因为你@的朋友们没有你改后的名字。

Next, try deleting a character or word within the mention. Note that only the
section of the mention that you have deleted is removed. In this way, we can
allow short names for mentions.

接下来，在@的字符串中，尝试删除一个字符或者一个单词。注意你删除的那一部分会被删除，用这样方式。我们可以允许缩写名。

## Modifying Entities

Since `DraftEntityInstance` records are immutable, you may not update the `data`
property on an instance directly.

因为`DraftEntityInstance`记录是持久化的，你也许不能直接更新`data`属性。

Instead, two `Entity` methods are available to modify entities: `mergeData` and
`replaceData`. The former allows updating data by passing in an object to merge,
while the latter completely swaps in the new data object.

相应的，两个`Entity`方法可以用来修改实体：`mergeData` 和 `replaceData`。之前允许通过传一个对象给merge更改data，以后会在新的data对象中完全替换。

## Using Entities for Rich Content

The next article in this section covers the usage of decorator objects, which
can be used to retrieve entities for rendering purposes.

下一篇文章会讲到装饰器(decorator)的用，可以被用来获取实体。

The [link editor example](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/link)
provides a working example of entity creation and decoration in use.

[link editor example](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/link)提供了一个实体创建和使用decoration的示例。
