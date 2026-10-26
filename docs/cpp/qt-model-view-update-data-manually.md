# Qt Model-View学习之手动更新Model数据

在使用基于QAbstractListModel的自定义Model时，遇到数据更新后Model不自动刷新数据的问题。通过手动发送更新信号即可解决。 方法，在更新数据时调用

```cpp
emit beginResetModel();
emit endResetModel();
```

注意reset model需要成对使用。