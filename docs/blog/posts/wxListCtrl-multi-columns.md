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
# 多列wxListCtrl使用技巧

以官方Wiki的minimal example为例：

```cpp
// Example uses fictive class "Item" and fictive getters to access them. Adapt for your needs

class MyFrame : public wxFrame
{
    wxListCtrl* m_item_list;

public:
    MyFrame() : wxFrame(NULL, wxID_ANY,  wxT("Hello wxWidgets"), wxPoint(50,50), wxSize(800,600))
    {
        wxPanel* mainPane = new wxPanel(this);
        wxBoxSizer* sizer = new wxBoxSizer(wxHORIZONTAL);
        
        m_item_list = new wxListCtrl(mainPane, wxID_ANY, wxDefaultPosition, wxDefaultSize, wxLC_REPORT);
        
        // Add first column       
        wxListItem col0;
        col0.SetId(0);
        col0.SetText( _("Foo") );
        col0.SetWidth(50);
        m_item_list->InsertColumn(0, col0);
        
        // Add second column
        wxListItem col1;
        col1.SetId(1);
        col1.SetText( _("Name") );
        m_item_list->InsertColumn(1, col1);
        
        // Add third column     
        wxListItem col2;
        col2.SetId(2);
        col2.SetText( _("Comments") );
        m_item_list->InsertColumn(2, col2);
        
        
        const int item_amount = getItemAmount();
        for (int n=0; n<item_amount; n++)
        {
            Item* curritem = getItem(n);
            
            wxListItem item;
            item.SetId(n);
            item.SetText( curritem->getName() );
            
            m_item_list->InsertItem( item );
            
            // set value in first column
            if (!curritem->isFoo())
            {
                m_item_list->SetItem(n, 0, wxT("Foo"));
            }
            else
            {
                m_item_list->SetItem(n, 0, wxT("Bar"));
            }
            
            // set value in second column
            m_item_list->SetItem(n, 1, curritem->getName());
            
            // set value in third column
            m_item_list->SetItem(n, 2, curritem->getDescription());
            
        }
        
        sizer->Add(m_item_list,1, wxEXPAND | wxALL, 10);
        mainPane->SetSizer(sizer);
    }
};

class MyApp: public wxApp
{
    wxFrame* m_frame;
public:
    
    bool OnInit()
    {
        m_frame = new MyFrame();
        m_frame->Show();
        return true;
    } 
    
};

IMPLEMENT_APP(MyApp)
```

由于官方的API文档描述的比较简单，整理一下使用时的注意事项：

- 要使用带表头的多列List，初始化时，需有wxLC_REPORT的flag（注意不同flag会有冲突）
- 使用ClearAll清除表格时，表头（Columns）也会一起被清除，需要重新添加
- 添加表头有两种方式：

1. AppendColumn，简单方便
2. 构造一个wxListItem后，再InsertColumn（官方Minimal Example的方式）

- 添加数据的方式有点奇怪，需分为两步：

1. 先获取到索引（可用GetItemCount方法取当前Item的数量作为index），再使用InsertItem插入一个Item，这个方法会返回新Item的index，作为下一步使用
2. 插入Item后，使用SetItem(index,column,label,imageIndex=-1)方法，给刚才的Item设置各列显示的数据，如需设置自定义数据、图片、字体等高级功能，可用wxListItem的重载版本

新增Item的示例代码如下：

```cpp
int TrayThem::MainWidget::cbReloadPrograms(void *argument, int argc,
                                           char **argv, char **azColName) {
  if (argument == nullptr) {
    return -1;
  }
  auto mw = static_cast<TrayThem::MainWidget *>(argument);
  auto programs = mw->m_lvPrograms;
  int index = programs->GetItemCount();
  programs->InsertItem(index, "");
  for (int i = 0; i < argc; i++) {
    programs->SetItem(index, i, argv[i] == nullptr ? "" : argv[i]);
  }
  return 0;
}
```

关于事件，常用的事件主要有wxEVT_LIST_ITEM_SELECTED和wxEVT_LIST_ITEM_DESELECTED，分别对应选中和取消选中Item，对应的事件处理函数原型为：

```cpp
void handler(wxListEvent &event);
```

其中，event.GetItem可取到选中的Item，GetText可取到对应的Label，如果是单行选择模式的List（初始化时，设置了wxLC_SINGLE_SEL的flag），则GetText会返回第一列的Label

获取同一行中其它列的Text，可使用GetItemLabel方法，示例代码如下：

```cpp
void TrayThem::MainWidget::OnProgramSelected(wxListEvent &event) {
  wxListItem item = event.GetItem();
  this->m_SelectedId = item.GetText();
  this->m_SelectedProgram.program_id = this->m_SelectedId;
  int col = item.GetColumn();
  this->m_SelectedProgram.running_status =
      this->m_lvPrograms->GetItemText(item, ++col);
  this->m_SelectedProgram.program_name =
      this->m_lvPrograms->GetItemText(item, ++col);
  this->m_SelectedProgram.autostart =
      this->m_lvPrograms->GetItemText(item, ++col) == "1" ? true : false;
  this->m_SelectedProgram.execution =
      this->m_lvPrograms->GetItemText(item, ++col);
  this->m_SelectedProgram.working_directory =
      this->m_lvPrograms->GetItemText(item, ++col);
  this->m_SelectedProgram.parameters =
      this->m_lvPrograms->GetItemText(item, ++col);
}
```