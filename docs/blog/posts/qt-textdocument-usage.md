---
date: 2026-10-30
authors:
  - lee
categories:
  - C++
  - 编程
tages:
  - Qt
---
# Qt TextDocument内容组织方式

Qt的QTextEdit等控件在存储内容的时候是以QTextDocument & QTextBlock & QTextFragment组织内容的。

包含关系为QTextDocument QTextBlock QTextFragment

一般一个控件（如QTextEdit）只包含一个QTextDocument，文档对象可以很容易的保存为HTML或者ODF文件。

其中，QTextBlock是以换行符分隔的，遇到一个换行符就新建一个QTextBlock，因此，一行内容就是一个QTextBlock。

而QTextBlock中包含了一个或多个QTextFragment，这些fragment可以是文字，图片或其他多媒体元素，fragment以内容的类型区分。

例如如下内容：

\[文本\]\[图片\]\[文本\]

上面的内容生成了一个block，这个block包含了3个fragment。

而内容：

\[文本\]

\[图片\]

\[文本\]

则生成了3个block，因为一行内容生成一个block。

这个概念在使用网络发送图文混排的内容时尤其重要，如果不能正确理解document/block/fragment的组织方式，则会丢失原始格式。