# QTextEdit动态显示gif动画

原理：使用QMovie播放gif并更新当前帧到QTextEdit中Document的Resource。\
相关信号：QMovie::frameChanged(int frameNo)\
实例代码： 初始化QMovie对象：

```cpp
QMovie*movie=newQMovie(this);
movie->setFileName(img_path);
movie->setCacheMode(QMovie::CacheNone);
connect(movie,SIGNAL(frameChanged(int)),this,SLOT(frameChanged(int)));
movie->start();
```

槽函数:

```cpp
voidMyObject::frameChanged(intframeNumber)
if(frameNumber==-1)
return;

QMovie*movie=(QMovie*)this->sender();
QStringname=movie->fileName().toUtf8().toBase64();
ui->view->document()->addResource(QTextDocument::ImageResource,
name,
QVariant(movie->currentPixmap()));
ui->view->setLineWrapColumnOrWidth(ui->view->lineWrapColumnOrWidth());
```

关键点：

QMovie对象在播放gif动画时发送frameChanged信号至槽，此处使用QOjbect::sender()方法来得到具体的对象指针，然后调用document的addResource方法更新当前的帧到其图像资源，最后调用setLineWrapColumnOrWidth来刷新显示。这样就实现了gif图片的不断刷新。