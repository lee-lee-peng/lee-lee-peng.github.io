# C++中const对象引用的一些限制

先上示例代码：

```cpp
class Test {
public:
 void doConst() const {
//
}
void doNoConst() {
//
}
};

int main(int argc,char **argv){
Test t;
const &ct=t;
//error: passing xxx as 'this' argument of xxx discards qualifiers [-fpermissive]
ct.doNoConst();

//Correct;
ct.doConst();

return 0;
}
```

代码说明：

- const引用的值不可改变（废话）
- 只有声明为const的成员函数才能被const对象调用（重要，上面代码报错的原因）