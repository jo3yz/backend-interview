## 某种类型的可能有的四种swap
1. std提供的标准版本（基于副本的交换）
2. 用户自己全特化的std版本（基于impl的交换）
3. 针对于类型的member版本
4. 针对于类型的non-member版本（可能处于自己的命名空间里，调用member版本的swap）

## 手法如何应用？
```
namespace ns
{
    // 提供一个non-member的版本
    void swap(Bar &x, Bar &y)
    {
        x.swap(y);
    }
}
```
```
void doSomething(Foo &x, Foo &y)
{
    using std::swap;
    swap(x, y); // 如果指定命名空间内没有自己提供的non-member swap，就默认调用std的版本
}
```

## 异常安全
- 基本承诺
所有对象都处于内部前后一致的状态

- 强烈保证
<<<<<<< HEAD
 如果调用某函数后异常发生，则程序会回到调用函数之前的状态（就像怀孕一样，要么生要么打）
=======
 如果调用某函数后异常发生，则程序会回到调用函数之前的状态
>>>>>>> 982c8fd4ecefe37ea0c3294711c4c2ed9a3e9b64

- noexcept
保证不抛异常

## copy-and-swap技巧：swap必须noexcept
- 这个技巧大致是这个意思：如果函数需要对某个变量进行修改，或者输出某些变量，则在一个副本上进行修改或者构造（因为这个过程可能会抛出异常），最后将这个副本和指定对象（成员或者输出）进行swap；
<<<<<<< HEAD
- 因为swap是不抛异常的（noexcept修饰），所以如果前面的副本构造失败，swap不会执行，提供了上述提到的“强烈保证”
- 此外，要实现异常安全，需要使用RAII手法实现自动回滚
=======
- 因为swap是不抛异常的（noexcept修饰），所以如果前面的副本构造失败，swap不会执行，提供了上述提到的“强烈保证”
>>>>>>> 982c8fd4ecefe37ea0c3294711c4c2ed9a3e9b64
