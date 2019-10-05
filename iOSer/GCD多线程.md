# GCD多线程
#程序员/iOS/线程

参考文章：
[iOS多线程--彻底学会多线程之『GCD』](http://www.jianshu.com/p/2d57c72016c6)
[Swift 3.0 GCD和DispatchQueue 使用解析](http://www.jianshu.com/p/737233208d40)

这哥们已经写得很好了，代码这东西，不敲一下，总觉得就不是自己的。再者，swift GDC的接口真是改得那个揪心啊~！


#### 并行队列+同步执行
不会创建新的线程，而是在当前线程立即执行
```swift
let queue = DispatchQueue.init(label: "com.bear.queue", attributes: .concurrent)

queue.sync {
    print("1-----\(Thread.current)")
}

queue.sync {
    print("2-----\(Thread.current)")
}

queue.sync {
    print("3-----\(Thread.current)")
}

queue.sync {
    print("4-----\(Thread.current)")
}
```
output:
```
1-----<NSThread: 0x100a062d0>{number = 1, name = main}
2-----<NSThread: 0x100a062d0>{number = 1, name = main}
3-----<NSThread: 0x100a062d0>{number = 1, name = main}
4-----<NSThread: 0x100a062d0>{number = 1, name = main}
```

#### 并行队列+异步执行
会创建新的线程
```swift
let queue = DispatchQueue.init(label: "com.bear.queue", attributes: .concurrent)

let sem = DispatchSemaphore.init(value: 0)

queue.async {
    print("1-----\(Thread.current)")
}

queue.async {
    print("2-----\(Thread.current)")
}

queue.async {
    print("3-----\(Thread.current)")
}

queue.async {
    print("4-----\(Thread.current)")
}
```
output:
```
3-----<NSThread: 0x100a03570>{number = 3, name = (null)}
2-----<NSThread: 0x100a03650>{number = 4, name = (null)}
1-----<NSThread: 0x100b00160>{number = 2, name = (null)}
4-----<NSThread: 0x100c041d0>{number = 5, name = (null)}
```

#### 串行队列+异步执行
```swift
let queue = DispatchQueue.init(label: "com.bear.queue")

let sem = DispatchSemaphore.init(value: 0)

queue.async {
    print("1-----\(Thread.current)")
}

queue.async {
    print("2-----\(Thread.current)")
}

queue.async {
    print("3-----\(Thread.current)")
}

queue.async {
    print("4-----\(Thread.current)")
}
```
output:
```
1-----<NSThread: 0x100c02ee0>{number = 2, name = (null)}
2-----<NSThread: 0x100c02ee0>{number = 2, name = (null)}
3-----<NSThread: 0x100c02ee0>{number = 2, name = (null)}
4-----<NSThread: 0x100c02ee0>{number = 2, name = (null)}
```

#### 串行队列+同步执行
```swift
let queue = DispatchQueue.init(label: "com.bear.queue")

queue.sync {
    print("1-----\(Thread.current)")
}

queue.sync {
    print("2-----\(Thread.current)")
}

queue.sync {
    print("3-----\(Thread.current)")
}

queue.sync {
    print("4-----\(Thread.current)")
}

```
output:
```
1-----<NSThread: 0x100b06b50>{number = 1, name = main}
2-----<NSThread: 0x100b06b50>{number = 1, name = main}
3-----<NSThread: 0x100b06b50>{number = 1, name = main}
4-----<NSThread: 0x100b06b50>{number = 1, name = main}
```
#### 主队列+同步执行（这个会死锁）
#### 主队列+异步执行
虽然是异步的，但由于分配到主队列中，所以不会创建新的线程。
```swift
        DispatchQueue.main.async {
            print("1-----\(Thread.current)")
        }
        
        DispatchQueue.main.async {
            print("2-----\(Thread.current)")
        }
        
        DispatchQueue.main.async {
            print("3-----\(Thread.current)")
        }
        
        DispatchQueue.main.async {
            print("4-----\(Thread.current)")
        }

```
output:
```
1-----<NSThread: 0x60800006b4c0>{number = 1, name = main}
2-----<NSThread: 0x60800006b4c0>{number = 1, name = main}
3-----<NSThread: 0x60800006b4c0>{number = 1, name = main}
4-----<NSThread: 0x60800006b4c0>{number = 1, name = main}
```


>异步一般情况下会创建新的线程来执行任务。
>同步队列需要停止当前正在执行的任务立即执行任务
>串行队列就是一个接一个的执行
>并发队列就是一起塞进队列中，何时执行，创建多少线程执行收系统决定
>主队列中的异步任务不会创建新的线程（是不是可以说主队列只一个主线程）

- - - -

#### 线程间通信
说得好高大上，其实就是GDC嵌套。
```swift
        let queue = DispatchQueue.init(label: "com.bear.queue")
        queue.async {
            print("\(Thread.current)")
            DispatchQueue.main.async {
                print("\(Thread.current)")
            }
        }

```

#### 任务分组
当多个任务进行并发异步执行的时候，任务的执行顺序是随机的。此时可以通过barrier将任务进行分组。分组后组的执行顺序按代码顺序执行。而组内的顺序依旧是随机的。
```swift
        let queue = DispatchQueue.init(label: "com.bear.thread", attributes: .concurrent)
        queue.async {
            print("1----\(Thread.current)")
        }
        queue.async {
            print("2----\(Thread.current)")
        }
        queue.async {
            print("3----\(Thread.current)")
        }
        queue.async {
            print("1----\(Thread.current)")
        }
        queue.async {
            print("2----\(Thread.current)")
        }
        queue.async {
            print("3----\(Thread.current)")
        }
        
        queue.async(flags: .barrier) { 
            print("----barrier----")
        }
        
        queue.async {
            print("4----\(Thread.current)")
        }
        queue.async {
            print("5----\(Thread.current)")
        }
        queue.async {
            print("4----\(Thread.current)")
        }
        queue.async {
            print("5----\(Thread.current)")
        }

        queue.async(flags: .barrier) {
            print("----barrier----")
        }

        queue.async {
            print("6----\(Thread.current)")
        }
        queue.async {
            print("7----\(Thread.current)")
        }
        queue.async {
            print("6----\(Thread.current)")
        }
        queue.async {
            print("7----\(Thread.current)")
        }
        
```
output:
```
1----<NSThread: 0x60800026a480>{number = 3, name = (null)}
3----<NSThread: 0x600000264ac0>{number = 6, name = (null)}
2----<NSThread: 0x600000264a80>{number = 5, name = (null)}
1----<NSThread: 0x60800026a340>{number = 4, name = (null)}
2----<NSThread: 0x60800026a480>{number = 3, name = (null)}
3----<NSThread: 0x600000264ac0>{number = 6, name = (null)}
----barrier----
4----<NSThread: 0x600000264ac0>{number = 6, name = (null)}
5----<NSThread: 0x60800026a480>{number = 3, name = (null)}
4----<NSThread: 0x600000264ac0>{number = 6, name = (null)}
5----<NSThread: 0x60800026a340>{number = 4, name = (null)}
----barrier----
6----<NSThread: 0x60800026a340>{number = 4, name = (null)}
6----<NSThread: 0x60800026a340>{number = 4, name = (null)}
7----<NSThread: 0x60800026a340>{number = 4, name = (null)}
7----<NSThread: 0x600000264ac0>{number = 6, name = (null)}

```

#### 延时任务
```swift
        let queue = DispatchQueue.init(label: "com.bear.thread", attributes: .concurrent)
        queue.asyncAfter(deadline: DispatchTime.now() + 3) {
            print("hello")
        }

```

#### Group(等待任务结束后执行)
```swift
        let queue = DispatchQueue.init(label: "com.bear.thread", attributes: .concurrent)
        let group = DispatchGroup.init()
        let sem = DispatchSemaphore.init(value: 0)
        
        queue.async(group: group) { 
            sem.signal()
            print("group 1")
        }
        queue.async(group: group) {
            sem.signal()
            print("group 2")
        }
        queue.async(group: group) {
            sem.signal()
            print("group 3")
        }
        queue.async(group: group) {
            sem.signal()
            print("group 4")
        }
        
        
        group.notify(queue: queue) { 
            sem.wait()
            sem.wait()
            sem.wait()
            sem.wait()
            
            print("end")
        }
```
output:
```
group 1
group 4
group 3
group 2
end

```

#### DispatchWorkItem
类似dispatch_block_t
```swift
        let queue = DispatchQueue.init(label: "com.bear.thread", attributes: .concurrent)
        let dispatchWorkItem : DispatchWorkItem = DispatchWorkItem.init { 
            print("hello")
        }
        dispatchWorkItem.perform()
        dispatchWorkItem.notify(queue: queue) { 
            print("world")
        }
```

#### DispatchSource
这个和Timer有啥区别呢？-。-
```swift
    func dispatchSource() {
        var count = 0
        let queue = DispatchQueue.init(label: "com.bear.thread", attributes: .concurrent)
        let timer = DispatchSource.makeTimerSource(queue: queue)
        timer.setEventHandler(handler: DispatchWorkItem{
            print("hello world")
            count += 1
            if count >= 5 {
                timer.cancel()
            }
        })
//        timer.scheduleOneshot(deadline: .now())
        timer.scheduleRepeating(deadline: .now() + 3, interval: .seconds(1))
        timer.resume()
    }
```