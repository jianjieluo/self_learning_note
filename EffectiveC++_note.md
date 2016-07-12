
《Effective C++》中文版阅读摘要 （不定时更新）
===


30. Understand the ins and outs of inlining.
    - inline函数背后的整体观念是：将“对此函数的每一个调用”都以函数本体替换之
    - inline只是对编译器的一个申请，不是一个强制命令
    - inline的声明可以隐喻也可以显式
        - 隐喻的方式是将函数定义于class的定义式内
        - 显式的方式是直接在前面加上 inline 的字段
    - inline 可以减少函数调用所招致的额外开销，但是也可能造成代码的膨胀，使得程序体积太大
    - 大部分的编译器是拒绝将virtual 函数设置为inline的，因为virtual需要等待，而inline则是在生成之前就要插入的
    - 构造函数和析构函数往往是inlining的糟糕候选
        - **尤其是在代码有继承的情况下**
            - 因为可能在构造函数里面，插入大量的基类的构造代码
        - 这和我们大学上课时打的简单代码题有所不同
    - 必须评估“将函数声明为inline”所带来的冲击，因为其无法随程序库的升级而升级
    - 大部分的调试器对于inline的函数束手无策，**因为不能在一个并不存在的函数内设置断点**
    - 在开发时，一开始先不要将任何函数声明为inline，或至少将inlining施行的范围局限。我们应该要做的是开发完成后，再应用程序的80-20经验法则，对花费时间多的那20%的代码上用inline或其他方法来瘦身。

1. Accustoming Yourself to C++
    - View C++ as a federation of languages
    - 将c++看成4个次语言
        - C
        - Object-Oriented C++
        - Template C++
        - STL

2. Prefer consts, enums, and inlines to #defines
    - 对于单纯常量，最好以const对象或enums替换#defines
    - 对于形似函数的宏(macros), 最好改用inline-template 函数替换#defines

3. Use const whenever possible.
    - const std::vector<int>::iterator iter = vec.begin();  // iter的作用像个T* const
    - std::vector<int>::const_iterator cIter = vec.begin();  // iter的作用像个const T*
    - const 成员函数内是不能修改成员变量的
    - mutable关键字使得被修饰的成员变量可能总是会被更改，即使在const成员函数内
    - const 和 non-const 成员函数中避免重复
        - 当两者有着实质等价的实现时，令 non-const 版本调用const版本可以避免代码重复
    - cosnt 可被施加于任何作用域内的对象，函数参数，函数返回类型，成员函数本体

4. Make sure that objects are initialized before they're unsigned
    - 为内置型对象进行手工初始化，因为c++不保证初始化它们
    - 过哦早函数最好使用 member initialization list ，而不要在构造函数本体内使用赋值操作(assignment).初始值列出的成员变量，其排列次序应该和它们在class中的声明次序相同
