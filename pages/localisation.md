---
layout: page
title: 本地化翻译
---

RmlUi 完全支持通过下面描述的接口进行本地化。

### 字符串编码

RmlUi 假定它给出的所有数据，无论是从 RML 读入还是通过程序提供，都是 UTF-8 编码。 这意味着如果您使用 8 位 ASCII，则无需更改任何内容，但允许您在需要时指定多字节 Unicode 字符。

### 翻译

RmlUi 在解析 RML 时读取的所有原始文本（即除 XML 标记之外的所有内容）都通过 [系统接口](cpp_manual/interfaces/system.html) 上的 `TranslateString()` 函数发送。 该函数被赋予读取的原始字符串，应用程序可以在将翻译后的字符串（以及所做的替换次数）返回给 RmlUi 之前进行任何必要的修改。

通过翻译器将执行以下操作：

```cpp
#include <RmlUi/Core/SystemInterface.h>

class SampleSystemInterface : public Rml::SystemInterface
{
	int TranslateString(Rml::String& translated, const Rml::String& input)
	{
		translated = input;
		return 0;
	}
```

#### 字符串表

`TranslateString()`方法可以与应用程序的字符串表结合使用，以对文档的文本进行文本替换。 例如，以 _Rocket Invaders_ 示例中的 pause.rml 文件为例：

```html
<rml>
	<head>
		<title>退出?</title>
	</head>
	<body>
		<p>你确定要结束游戏吗?</p>
		<button>是</button>
		<button>否!</button>
	</body>
</rml>
```

如果我们要本地化 _Rocket Invaders_，我们希望将所有英文字符串从 RML 中移出并放入字符串表中。 然后，RML 中的原始文本将被字符串表标签替换：

```html
<rml>
	<head>
		<title>[退出标题]</title>
	</head>
	<body>
		<p>[退出确认]</p>
		<button>[确认]</button>
		<button>[拒绝]</button>
	</body>
</rml>
```

假设应用程序有一个 `StringTable` 类，该类已经为该语言加载了适当的字符串表，那么我们的示例翻译器将变为：

```cpp
	int TranslateString(Rml::String& translated, const Rml::String& input)
	{
		// 尝试在字符串表中查找翻译。
		if (StringTable::GetString(translated, input))
			return 1;

		// 没有翻译; 返回原始输入字符串。
		translated = input;
		return 0;
	}
```

现在字符串对于我们指定字符串表的任何语言都是有效的。 在实践中，您可能需要更复杂的翻译器来替换字符串中的多个标记。

注意，您可以将 RML 放入已翻译的字符串中，它将被适当地解析，例如, 你可以用一个 `<img>`{:.tag} 标签做替换标签，以呈现控制器按钮的图标。
