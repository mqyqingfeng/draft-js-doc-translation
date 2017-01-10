Managing text input focus can be a tricky task within React components. The browser
focus/blur API is imperative, so setting or removing focus via declarative means
purely through `render()` tends to feel awkward and incorrect, and it requires
challenging attempts at controlling focus state.

管理输入框的聚焦是一个棘手的任务在React组件中。浏览器的focus/blur API是必要的，所以设置和移除focus通过声明意味着单纯的通过`render()`会变得笨拙和不争取的。它必然会引来有挑战性的尝试在控制状态聚焦上。

With that in mind, at Facebook we often choose to expose `focus()` methods
on components that wrap text inputs. This breaks the declarative paradigm,
but it also simplifies the work needed for engineers to successfully manage
focus behavior within their apps.

处于上述的原因，在Facebook，我们经常选择暴露`focus()`方法在包裹输入框的组件上。它会破坏陈述式的范式，但是会简化工程师管理聚焦行为的工作量。

The `Editor` component follows this pattern, so there is a public `focus()`
method available on the component. This allows you to use a ref within your
higher-level component to call `focus()` directly on the component when needed.

`Editor`组件允许这种模式，所以在组件上有公共的`focus()`方法。这允许你在需要的时候，在高阶组件中使用ref来直接调用`focus()`方法。

The event listeners within the component will observe focus changes and
propagate them through `onChange` as expected, so state and DOM will remain
correctly in sync.

事件监听器会监控聚焦的变化并且传递他们通过`onChange`，所以state和DOM依然会保持正确的同步。

## Translating container clicks to focus

Your higher-level component will most likely wrap the `Editor` component in a
container of some kind, perhaps with padding to style it to match your app.

你的高阶组件也许会在一个容器中包裹`Editor`组件，也许还会为了适配你的app，带上padding的样式。

By default, if a user clicks within this container but outside of the rendered
`Editor` while attempting to focus the editor, the editor will have no awareness
of the click event. It is therefore recommended that you use a click listener
on your container component, and use the `focus()` method described above to
apply focus to your editor.

默认的，如果一个用户当尝试聚焦在这个编辑器的时候，在这个容器里点击了但是在渲染的`Editor`之外，编辑器是不会响应点击事件的。因为这样，所以推荐你用一个点击事件监听器在你的容器组件中，然后用上述的`focus()`方法聚焦到你的编辑器中。

The [plaintext editor example](https://github.com/facebook/draft-js/tree/master/examples/draft-0-9-1/plaintext),
for instance, uses this pattern.

[plaintext editor example]中就用了这种方式