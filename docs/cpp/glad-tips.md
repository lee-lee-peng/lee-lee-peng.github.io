# Glad使用总结

和传统的GLEW不同，GLAD类似springboot starter，是一个开源的按需生成OpenGL函数Loader的WEB服务，GLAD2的官方地址为<https://gen.glad.sh/>，GLAD1的生成地址是<https://glad.dav1d.de/>，二者的API差异很大，本文以GLAD2的使用为例

使用WEB服务生成代码：

进入GLAD2的WEB页面后，可按Generator（编程语言），OpenGL各API版本，如OpenGL, OpenGL ES, GLX，WGL等，选了API版本后，Extensions可按需选择，或使用ADD ALL一键加入所有扩展（推荐）

Options中有几个重要选项：

alias - 默认情况下，glad的OpenGL函数带glad\_前缀，例如glEnable，需使用glad_glEnable，选中这个选项后，可直接使用glEnable，不需要带前缀

loader - 生成gladLoadXXX()这种不带参数的loader，如果不选，则需使用自己传入如glfwgetprocaddress这样的load function的版本，尤其是WGL，有大坑，这个选项非常推荐选上

点击generate后，会进入一个页面，包含include，src和glad.zip（下载后加入到项目中的代码），官方推荐的使用方式是将其中的\*.h，\*.c文件直接加入到自己项目中的源代码中，比如在add_execution/add_library时，如果需要编译成so/dll这样的动态链接库，需有以下几点要注意：

编译成动态链接库时，glad需定义GLAD_API_CALL_EXPORT_BUILD，否则不会导出函数，可使用target_add_definitions在CMakeLists.txt加入

使用glad动态链接库的程序，需定义GLAD_API_CALL_EXPORT，否则会链接失败，另外#include &lt;glad/gl.h>需放在所有其它有#include &lt;gl.h>之类包含了OpenGL的头文件之前的位置，否则会编译失败

动态链接时，程序中所有模块只需调用一次loader即可初始化OpenGL函数地址，可通过判断glad_glEnable函数指针是否为nullptr来判断是否需调用loader，WGL可通过判断相应的glad_wglXXX是否为nullptr来按需调用loader