# WPS JS插件实现多顶占矩形
```javascript
 function CreateDialog_CommandButton1_Click()

{

	let nodesAdd=CreateDialog.SpinButton1.Value-4;

    let linesAdd=nodesAdd==0?0:nodesAdd/2;

	let myDocument = Application.ActivePresentation.Slides.Item(1)

    let theShape=myDocument.Shapes.AddShape(msoShapeRectangle, 50, 50, 200, 20)

    theShape.TextFrame.TextRange.Text="New Shape"

    var subShapes=new Array();

    subShapes.push(theShape.Name);

    var subL=theShape.Left;

    var subT=theShape.Top;

    var subH=theShape.Height

    let subGap=theShape.Width/(linesAdd+1);

    for(i=1;i<linesAdd+1;i++){

    	subL+=subGap;

    	var shape1=myDocument.Shapes.AddShape(msoShapeRectangle,subL,subT,1,subH);

    	shape1.Fill.Transparency=1.0;

    	shape1.Line.Transparency=1.0;

    	subShapes.push(shape1.Name);

    }

    if(linesAdd>0){

    	myDocument.Shapes.Range(subShapes).Group();

    }

   CreateDialog.Close();

}

```