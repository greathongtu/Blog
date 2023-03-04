---
layout: '../../layouts/MarkdownPost.astro'
title: '总结Effective C++'
pubDate: 2023-03-14
description: '第一篇Blog，写一下Effective C++的笔记。'
author: 'Aaron'
cover:
    url: '/preview/effective_cpp.jpg'
    square: ''
    alt: 'cover'
tags: ["读书笔记", "C++"]
theme: 'light'
featured: false
---

# 1. View C++ as a federation of languages
  C++ == C && OOP && template && STL

# 2. Prefer consts, enums, and inlines to #defines

# 3. const 指针：
``` cpp
// * 前的const：data const
// * 后的const: pointer const
const char * const p = "Hello";

// mutable释放掉non-static成员变量的bitwise constness约束
// const函数不能调用non-const函数，反之可以
```

# 4. member initialization list
  不是赋值而是copy构造，效率高。
  成员初始化顺序：1. Base class 早于derived class。 2. class的成员变量以其声明顺序初始化
  ```cpp
// 为免除“跨编译单元之初始化次序”问题,请用local static对象替换non-local static对象
FileSystem& tfs() {
    static FileSystem fs;
    return fs;
}
```

# 5. C++ 默默编写的函数
  如果你打算在一个“内含reference成员/const成员”的class内支持assignment操作，你必须自己定义copy assignment操作符。（compiler不会做的）

# 6. 如果不想要compiler生成的函数：
    1. 将其设置成private （链接期报错）
    2. 继承自Uncopyable这样的base class
    3. =delete （c++11）
# 7. 只要有多态，析构函数必须是virtual的
  string不可以用作base class因为它的析构函数non-virtual

# 10. 令operator=返回一个reference to *this以实现连等

# 11. operator=注意自我赋值问题（如实现string类）

# 12. 如果class添加了一个成员变量，必须同时修改copying的函数（还有constructor、 operator=）

# 13. RAII

# 17. 以独立语句将newed对象置入智能指针
```cpp
// 此处虽然RAII了但仍可能泄露资源
// 因compiler执行顺序不确定，可能先new再抛出异常终止
processWidget(shared_ptr<Wight>(new Widget), priority());
```

# 21. 不要pointer或reference to local 对象（Golang可以） 

# 23. 用non-member、non-friend函数替换member函数以提高封装性

# 27. 4个cast

# 30. virtual函数不可能inline

# 34. 区分接口继承和实现继承
  声明一个pure virtual function是为了让derived classes只继承函数接口。
  Surprisingly，我们可以给pure virtual function提供定义
```cpp
Shape* ps1 = new Rectangle;
ps1->draw();
// 调用pure virtual function的唯一途径
ps1->Shape::draw();
```

# 35 考虑virtual function以外的其他选择
  1. 令客户通过public non-virtual 成员函数间接调用private virtual function。称为non-virtual interface (NVI)手法。它是Template Method设计模式的一个独特表现形式
  2. 藉由function pointer实现Strategy模式
  3. 藉由tr1::function实现Strategy模式
  4. 古典的Strategy模式

# 37 绝不重新定义继承而来的default parameter value
  因为virtual函数动态绑定；缺省参数静态绑定。

# 39 Private继承
  private继承是implemented-in-terms-of

# 40 尽量少用多重继承
  用途之一：public继承某个interface class，private继承某个协助实现的class

# 41 了解隐式接口和编译器多态

# 42了解typename的双重意义
  一般：想在template中指涉一个嵌套从属类型名称，就必须加上关键字typename。