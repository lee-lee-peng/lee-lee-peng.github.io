# 如何将一个QImage对象插入QTextEdit并显示

**1\. Using html tag**

```cpp
QTextEdit *textEditor = new QTextEdit(0);
QTextDocumentFragment fragment;
fragment = QTextDocumentFragment::fromHtml("");
textEditor->textCursor().insertFragment(fragment);
textEditor->setVisible(true);
```

**2\. Using QTextDocument's resource**

```cpp
QString file = QFileDialog::getOpenFileName(this, tr("Select an image"),
".", tr("Bitmap Files (*.bmp)
"
"JPEG (*.jpg *jpeg)
"
"GIF (*.gif)
"
"PNG (*.png)
"));
QUrl Uri ( file );
QImage image = QImageReader ( file ).read();
QTextDocument * textDocument = m_textEdit->document();
textDocument->addResource( QTextDocument::ImageResource, Uri, QVariant ( image ) );
QTextCursor cursor = m_textEdit->textCursor();
QTextImageFormat imageFormat;
imageFormat.setWidth( image.width() );
imageFormat.setHeight( image.height() );
imageFormat.setName( Uri.toString() );
cursor.insertImage(imageFormat);
```

**3\. Subclass QTextEdit (The example below also enabled drag & drop)**

```cpp
class TextEdit : public QTextEdit {
public:
	bool canInsertFromMimeData( const QMimeData* source ) const
	{
		return(source->hasImage() );
	}


	void insertFromMimeData( const QMimeData* source )
	{
		if ( source->hasImage() )
		{
			static int	i = 1;
			QUrl		url( QString( "dropped_image_%1" ).arg( i++ ) );
			dropImage( url, qvariant_cast( source->imageData() ) );
		} else if ( source->hasUrls() )
		{
			foreach( QUrl url, source->urls() )
			{
				QFileInfo info( url.toLocalFile() );
				if ( QImageReader::supportedImageFormats().contains( info.suffix().toLower().toLatin1() ) )
				{
					dropImage( url, QImage( info.filePath() ) );
				}else  {
					dropTextFile( url );
				}
			}
		}else  {
			QTextEdit::insertFromMimeData( source );
		}
	}
}

private:
void dropImage( const QUrl & url, const QImage & image )
{
	if ( !image.isNull() )
	{
		document()->addResource( QTextDocument::ImageResource, url, image );
		textCursor().insertImage( url.toString() );
	}
}


void dropTextFile( const QUrl & url )
{
	textCursor().insertText( file.readAll() );
}
};
```