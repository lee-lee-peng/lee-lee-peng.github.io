# 解决因OpenGL渲染窗口高宽比导致图形变形

在 OpenGL 中，由于窗口的 **宽高比（aspect ratio）** 与绘制内容的坐标系统不一致，图像会出现拉伸、压缩等变形现象。为了解决这个问题，可根据窗口的大小调整投影矩阵，确保图像在视觉上保持原始比例。

先通过glViewPort调整视口

```c
void resizeGL(int w, int h) {
    glViewport(0, 0, w, h);
}
```

再根据窗口高宽比，计算出合适的投影矩阵，这样体现出来的样式，就是截掉了宽高比之外的内容，显示的内容不变形，正圆就是正圆，不会被拉伸成椭圆

```c
float aspect=(float)w/h;
glm::mat4 projection;
if(aspect>=1.0f){
    projection = glm::ortho(-aspect, aspect, -1.0f, 1.0f, -0.1f, 1.0f);
}else{
    projection = glm::ortho(-1.0f, 1.0f, -1.0f/aspect, 1.0f/aspect, -0.1f, 1.0f);
}
```

参考OBS的实现，先计算显示窗口和渲染内容的比例：

```C++
static inline void GetScaleAndCenterPos(int baseCX, int baseCY, int windowCX,
					int windowCY, int &x, int &y,
					float &scale)
{
	double windowAspect, baseAspect;
	int newCX, newCY;

	windowAspect = double(windowCX) / double(windowCY);
	baseAspect = double(baseCX) / double(baseCY);

	if (windowAspect > baseAspect) {
		scale = float(windowCY) / float(baseCY);
		newCX = int(double(windowCY) * baseAspect);
		newCY = windowCY;
	} else {
		scale = float(windowCX) / float(baseCX);
		newCX = windowCX;
		newCY = int(float(windowCX) / baseAspect);
	}

	x = windowCX / 2 - newCX / 2;
	y = windowCY / 2 - newCY / 2;
}
```

正交投影时，设置偏移 顶点着色器就按标准方式乘以对应的矩阵

```c
#version 330 core
layout (location = 0) in vec3 aPos;
...
uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

void main()
{
    // 注意乘法要从右向左读
    gl_Position = projection * view * model * vec4(aPos, 1.0);
    ...
}
```