# 不透明结构体/对象-C/C++隐藏结构体/类成员的方法

纯C实现隐藏结构体（实现不透明结构体）的方法：

[假设我们要开发一个库example.so](http://xn--example-4t3kgms8db8fxqo8h9aribnyym72ni7h.so)，但又不想把某结构体的内容开放到SDK的头文件中，那么可以这样处理：

1. 准备三个文件：example.h（SDK的头文件），example_internal.h（不开放，存放了结构体的真实定义），example.c
2. 各文件内容如下：

   example.h:

   ```c
   /* opaque types */
   struct example_struct;
   /* helper functions */
   __declspec( dllexport ) struct example_struct* create_example(const int id,const float rate);
   __declspec( dllexport ) const int get_example_id(struct example_struct *example);
   __declspec( dllexport ) const float get_example_rate(struct example_struct *example);
   __declspec( dllexport ) void free_example(struct example_struct *example);
   ```

   example_internal.h:

   ```c
   /* The real structure */
   struct example_struct {
   int id;
   float rate;
   int hiddenInt;
   float hiddenRate;
   };
   ```

   example.c:

   ```c
   #include "example.h"
   #include "example_internal.h"
   struct example_struct* create_example(const int id,const float rate){
       struct example_struct *example=(struct example_struct*)malloc(sizeof(struct example_struct));
       return example;
   }

   const int get_example_id(struct example_struct *example){
       if(!example){
           return 0;
       }
       return example->id;
   }

   /* Ohter helper functions ... */
   ```
3. 生成好库后，只需要把example.so和example.h打包进SDK里就可以被用来开发了

C++实现隐藏类成员的方法：

还是以example.so为例：

1. 在头文件中定义一个不透明结构体/类，实际的内容放在实现文件中，注意：由于编译时，不能明确知道它的构造及析构函孙，因此无法使用智能指针

   example.hpp

   ```cpp
   __declspec( dllexport ) class example {
   public:
   const int getId() const;
   const float getRate() const;
   private:
   class exampleImpl;
   //opaque pointer;
   exampleImpl *m_impl;
   };
   ```

   example.cpp

```cpp
   #include "example.hpp"
   class exampleImpl {
   public:
    int m_id;
    float m_rate;
    int m_hideId;
    float m_hideRate;
   };
   
   const int example::getId() const{
       return this->m_impl->m_id;
   }
   const float getRate() const{
       return this->m_impl->m_rate;
   }
```

