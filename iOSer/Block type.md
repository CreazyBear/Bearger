# Block type
#程序员/iOS/Block

## MRC 

**类** 				**对象的存储域**
NSStackBlock		栈
NSGlobalBlock		程序的数据区域(.data区)
NSMallocBlock		堆

> 截获了自动变量的Block是NSStackBlock类型，没有截获自动变量的Block则是NSGlobalStack类型  
> NSStackBlock类型的Block进行copy操作之后其类型变成了NSMallocBlock类型  


**Block的类型**		**副本的配置存储域**		**复制效果**
NSStackBlock		栈						从栈复制到堆
NSGlobalStack		程序的数据区域			什么也不做
NSMallocBlock		堆						引用计数增加

```objc
-(void)start {
    
    int age = 10;
    void (^testBlock)(void) = ^{
        NSLog(@"Hello World!");
        NSLog(@"Hello World!----%d",age);
    };
    NSLog(@"%@\n %@\n %@\n %@", [testBlock class], 
									[[testBlock class] superclass], 
									[[[testBlock class] superclass] superclass], 
									[[[[testBlock class] superclass] superclass] superclass]);

    testBlock();
}
```

Output:
```
__NSStackBlock__
__NSStackBlock
NSBlock
NSObject
```

如果将`age`干掉
```objc
-(void)start {
    
//    int age = 10;
    void (^testBlock)(void) = ^{
        NSLog(@"Hello World!");
//        NSLog(@"Hello World!----%d",age);
    };
    NSLog(@"%@\n %@\n %@\n %@", [testBlock class], [[testBlock class] superclass], [[[testBlock class] superclass] superclass], [[[[testBlock class] superclass] superclass] superclass]);

    testBlock();
}

```

Output
```
__NSGlobalBlock__
__NSGlobalBlock
NSBlock
NSObject
```

对栈block进行copy会变成堆block。这个大家可以自己试一下。

- - - -

## ARC
总结一下ARC环境下自动进行copy操作的情况一共有以下几种：
	* **block作为函数返回值时。**
	* **将block赋值给__strong指针时。**
	* **block作为Cocoa API中方法名含有usingBlock的方法参数时。**
	* **GCD中的API。**

















