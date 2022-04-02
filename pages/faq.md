---
layout: page
title: 常见问题
---


### 我有渲染问题或崩溃，我该如何解决？

请查看 C++ 手册中的 [疑难解答](cpp_manual/troubleshooting.html) 页面，也欢迎你加入[RmlUi的Gitter频道](https://gitter.im/RmlUi/community)与其他用户交流，或在 [主仓库]({{page.lib_site}}) 写一篇文章描述你的情况。


### 如何设置自定义光标？

You display custom cursors using your OS cursor facilities. See [here](cpp_manual/contexts.html#mouse-cursor) for details on responding to the `cursor`{:.prop} property through the system interface.


### RmlUi是线程安全的吗?

在线程安全方面不提供任何保证。 从多个线程对 RmlUi API 的任何访问都需要同步，例如使用互斥锁。


### 我可以从脚本中改变装饰器吗？

可以通过内联样式设置装饰器。 但是，出于性能原因，强烈建议更改元素的类，而不是影响对其应用哪些装饰器。


### 如何实现高 DPI 支持？ 

RmlUi has extensive support for making a scalable user interface. In order to get sizes and lengths scaled properly, the [`dp`{:.value} length unit](rcss/syntax.html#dp-unit) should be used extensively in RCSS. This unit can be scaled relative to `px`{:.value} units by setting the *dp-ratio* using the function `Context::SetDensityIndependentPixelRatio`.

In addition, RmlUi provides the following features which should make high DPI graphics a breeze:

- [Media queries](rcss/media_queries.html) support the `resolution` feature to toggle styles and sprites based on the dp-ratio.
- [Sprite sheets](rcss/sprite_sheets.html) can specify their desired scaling by using the `resolution` property.
- Sprites can be overrided by later `@spritesheet` rules, making it easy to define [high DPI versions of sprites](rcss/sprite_sheets.html#high-dpi).
- Decorators and `<img>` elements automatically update when the dp-ratio changes and new sprites are selected.
- Sprites in decorators and `<img>` elements scale according to the source scaling and targeted dp-ratio.

Clients are themselves responsible for querying the platform for the desired scaling ratio, and then setting the dp-ratio on the RmlUi context. Take a look at RmlUi's sample shell for Windows to see how this is done there.


### 可以实现文档的热重载吗？

当然，这是将 UI 文档声明与主应用程序逻辑分开的一大优势。

可以始终对文档进行完全重新加载. 

```cpp
Rml::ElementDocument* my_document = context->LoadDocument("main_menu.rml");
// ...
// Later, after RML source was changed
my_document->Close();
my_document = context->LoadDocument("main_menu.rml");
```
This could eg. automatically be done any time `.rml` documents are saved to have your changes be reflected instantly. Users are themselves responsible to implement this automation, look into how to do this on your platform. Note that style sheets and templates are automatically cached, you may want to clear these first by calling `Rml::Factory::ClearStyleSheetCache()` and `Rml::Factory::ClearTemplateCache()`, respectively.

Now, clearly any state or programmatically changed elements will be reset during this operation. Occasionally, it is desirable to keep the current state such as when only working on the style of the document. Then, users may call the following.

```cpp
my_document->ReloadStyleSheet();
```

This only reloads the style sheet(s) applied to the current document, while keeping the document structure and its state intact. This also updates styles declared in the header, but notably *not* styles declared inline using the element attribute `style`{:.attr}. You might want to call this automatically for visible documents any time `.rcss` files are changed. This call automatically clears any cache first.

Finally, the following function may be helpful while editing textures,
```cpp
Rml::ReleaseTextures();
```
which is defined in `<RmlUi/Core/Core.h>`{:.path}. This call forces the library to reload all textures in use.


### 如何绑定到来自 C++/脚本的事件？

If you are using data bindings, you can use the [`data-event` view](data_bindings/views_and_controllers.html#data-event) together with a callback function in C++.

Alternatively, use the `Element::AddEventListener` function, passing in the `Rml::EventId` or the name of the event you want to bind to (without the "on" prefix), the listener object to attach, and whether you want to bind in the capture phase or not, as in the following example.

```cpp
class MyListener : public Rml::EventListener {
public:
	void ProcessEvent(Rml::Event& event) {
		printf("Processing event %s", event.GetType().c_str());
	}
}

void main() {
	/* ... */
	auto my_listener = std::make_unique<MyListener>();
	element = document->GetElementById("my_button");
	element->AddEventListener(Rml::EventId::Click, my_listener.get(), false);
	/* ... */
}
```
有关详细信息，请参阅有关 [事件侦听器](cpp_manual/events.html#event-listeners) 的文档

最后，还可以响应内联事件，例如

```html
<button onclick="game.start()">开始游戏</button>
```
有关详细信息，请参阅有关 [内联事件](cpp_manual/events.html#inline-events) 的文档。
