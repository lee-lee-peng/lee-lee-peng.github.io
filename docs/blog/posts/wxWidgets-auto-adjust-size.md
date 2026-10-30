---
date: 2026-10-30
authors:
  - lee
categories:
  - C++
  - 编程
tages:
  - wxWidgets
---
# wxDialog/wxWindow根据控件自适应大小

在使用wxSizer时，用Add (wxWindow *window, int proportion=0, int flag=0, int border=0, wxObject* userData=NULL)方法，将flag设置为wxALL，如果使用wxEXPAND，则会出现额外的空间，导致控件之间的间隔变的很大，效果很难看

在Add完所有控件后，调用wxSizer的SetSizeHints (wxWindow \*window)方法，window参数传入窗口指针