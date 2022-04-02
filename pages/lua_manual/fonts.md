---
layout: page
title: Loading fonts
parent: lua_manual
next: attaching_to_events
---

Lua 插件安装了一个全局表 `rmlui`，它提供了一种从 Lua 加载支持的字体文件的方法

该函数用单个字符串作为参数，即要加载的字体的文件名

```python
rmlui.LoadFontFace('../assets/LatoLatin-Regular.ttf')
rmlui.LoadFontFace('../assets/LatoLatin-Bold.ttf')
```

请参阅 RmlUi [Lua API 参考](api_reference.html#rmlui) 了解与此全局相关的其他内容
