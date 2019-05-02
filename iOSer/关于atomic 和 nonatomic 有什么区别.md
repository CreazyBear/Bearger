# 关于atomic 和 nonatomic 有什么区别
#程序员/iOS/内存管理

引子
```
@property(nonatomic, retain) UITextField *userName;
@property(atomic, retain) UITextField *userName;
@property(retain) UITextField *userName;
```

[ios - What's the difference between the atomic and nonatomic attributes? - Stack Overflow](https://stackoverflow.com/questions/588866/whats-the-difference-between-the-atomic-and-nonatomic-attributes/589392#589392)

有好事者已经翻译过了

后两行是一样的，不写的话 _默认就是atomic。_

_atomic 和 nonatomic 的区别在于，系统自动生成的 getter_setter 方法不一样_ 。如果你自己写 getter_setter，那 atomic_nonatomic_retain_assign_copy 这些关键字只起提示作用，写不写都一样。

对于atomic的属性，系统生成的 getter/setter 会保证 get、set 操作的完整性，不受其他线程影响。比如，线程 A 的 getter 方法运行到一半，线程 B 调用了 setter：那么线程 A 的 getter 还是能得到一个完好无损的对象。

而nonatomic就没有这个保证了。所以，nonatomic的速度要比atomic快。

不过atomic可并不能保证线程安全。如果线程 A 调了 getter，与此同时线程 B 、线程 C 都调了 setter——那最后线程 A get 到的值，3种都有可能：可能是 B、C set 之前原始的值，也可能是 B set 的值，也可能是 C set 的值。同时，最终这个属性的值，可能是 B set 的值，也有可能是 C set 的值。

保证数据完整性——这个多线程编程的最大挑战之一——往往还需要借助其他手段。