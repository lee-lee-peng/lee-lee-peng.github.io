# wxWidgets实现托盘图标动态右键菜单

首先，按标准流程，基于wxTaskbarIcon实现一个类

```cpp
namespace TrayThem {
class TrayIcon : public wxTaskBarIcon {
protected:
  virtual wxMenu *CreatePopupMenu();

public:
  explicit TrayIcon(wxTaskBarIconType iconType = wxTBI_DEFAULT_TYPE);
  ~TrayIcon();
};
}; 
```

然后，重载它的CreatePopupMenu方法，每次对它点击右键时，就会调用这个方法返回的wxMenu指针，菜单关闭时，这个wxMenu对象就会被销毁

示例代码如下：

```cpp
wxMenu *TrayThem::TrayIcon::CreatePopupMenu() {
  wxMenu *cm = new wxMenu();
  wxMenuItem *menuShow = cm->Append(wxID_ANY, "Show Program");
  this->Bind(wxEVT_MENU, &TrayThem::TrayIcon::OnMenuShow, this,
             menuShow->GetId());
  wxMenu *mp = new wxMenu();
  cm->AppendSubMenu(mp, "Programs");
  BizBus::GetInstance()->TraverseProgram(
      [](void *opaque, std::shared_ptr<ProgramInfo> info, bool isRunning) {
        wxMenu *mp = static_cast<wxMenu *>(opaque);
        wxMenuItem *item = mp->AppendCheckItem(wxID_ANY, info->program_name);
        item->Check(isRunning);
      },
      mp);
  cm->AppendSeparator();
  wxMenuItem *menuExit = cm->Append(wxID_ANY, "Exit");
  this->Bind(wxEVT_MENU, &TrayThem::TrayIcon::OnMenuExit, this,
             menuExit->GetId());
  return cm;
}
```

注意，由于每次使用右键菜单后，菜单就会被销毁，因此菜单项的选中状态不会保存，需要自己另外存储，下次创建菜单时，再调用Check方法选中