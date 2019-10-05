# NSOperation

> [iOS多线程--彻底学会多线程之『NSOperation』](http://www.jianshu.com/p/4b1d77054b35)  



#### 一. 基本用法

如果不创建BlockOperation，那么任务直接被分配到start被调用的线程。

addExecutionBlock的异步并发执行，可能在主线程也可能在别的线程。

```swift
        let blockOperation = BlockOperation.init {
            print("Simple Demo----\(Thread.current)\n")
        }
        
        blockOperation.addExecutionBlock {
            print("ExecutionBlock----0----\(Thread.current)\n")
        }
        blockOperation.addExecutionBlock {
            print("ExecutionBlock----1----\(Thread.current)\n")
        }
        blockOperation.addExecutionBlock {
            print("ExecutionBlock----2----\(Thread.current)\n")
        }
        blockOperation.addExecutionBlock {
            print("ExecutionBlock----3----\(Thread.current)\n")
        }
        blockOperation.addExecutionBlock {
            print("ExecutionBlock----4----\(Thread.current)\n")
        }
        blockOperation.addExecutionBlock {
            print("ExecutionBlock----5----\(Thread.current)\n")
        }
        blockOperation.addExecutionBlock {
            print("ExecutionBlock----6----\(Thread.current)\n")
        }
        blockOperation.addExecutionBlock {
            print("ExecutionBlock----7----\(Thread.current)\n")
        }
        blockOperation.start()
	
```



output:

```
ExecutionBlock----2----<NSThread: 0x60000026f480>{number = 4, name = (null)}

Simple Demo----<NSThread: 0x60800007e780>{number = 1, name = main}

ExecutionBlock----0----<NSThread: 0x608000267ec0>{number = 5, name = (null)}

ExecutionBlock----4----<NSThread: 0x60800007e780>{number = 1, name = main}

ExecutionBlock----6----<NSThread: 0x60800007e780>{number = 1, name = main}

ExecutionBlock----3----<NSThread: 0x608000267ec0>{number = 5, name = (null)}

ExecutionBlock----5----<NSThread: 0x60000026f480>{number = 4, name = (null)}

ExecutionBlock----7----<NSThread: 0x60800007e780>{number = 1, name = main}

ExecutionBlock----1----<NSThread: 0x608000267e80>{number = 3, name = (null)}



```



#### 二. 任务队列

如果创建OperationQueue.main，那么所有任务将在主线程中串行异步执行。

如果是非主线程队列，那么任务并发异步执行。

addExecutionBlock是并发异步，不受控制

为了实现在非主线程队列的串行异步执行，需要设置`queue.maxConcurrentOperationCount`

```swift
        let queue = OperationQueue.init()
//        let queue = OperationQueue.main
        queue.maxConcurrentOperationCount = 1
        print("start-------")
        
        queue.addOperation { 
            print("addOperation----1----\(Thread.current)\n")
        }
        queue.addOperation {
            print("addOperation----11----\(Thread.current)\n")
        }
        queue.addOperation {
            print("addOperation----111----\(Thread.current)\n")
        }
        queue.addOperation {
            print("addOperation----1111----\(Thread.current)\n")
        }
        queue.addOperation {
            print("addOperation----11111----\(Thread.current)\n")
        }
        queue.addOperation {
            print("addOperation----111111----\(Thread.current)\n")
        }
        queue.addOperation {
            print("addOperation----1111111----\(Thread.current)\n")
        }
        queue.addOperation {
            print("addOperation----11111111----\(Thread.current)\n")
        }
        queue.addOperation {
            print("addOperation----111111111----\(Thread.current)\n")
        }
        
        print("end-------")
```



#### 三. 操作依赖

```swift

        let queue = OperationQueue.init()
        print("start-------")

        let operation1 = BlockOperation.init { 
            print("operation---1---\(Date.init())---\(Thread.current)")
        }
        let operation2 = BlockOperation.init {
            print("operation---2---\(Date.init())---\(Thread.current)")
            sleep(3)
        }
        let operation3 = BlockOperation.init {
            print("operation---3---\(Date.init())---\(Thread.current)")
        }
        
        operation2.addDependency(operation1)
        operation3.addDependency(operation2)
        
        queue.addOperation(operation1)
        queue.addOperation(operation2)
        queue.addOperation(operation3)
        print("end-------")

```



output:

```
start-------
end-------
operation---1---2017-08-10 05:15:49 +0000---<NSThread: 0x6000000767c0>{number = 3, name = (null)}
operation---2---2017-08-10 05:15:49 +0000---<NSThread: 0x6000000767c0>{number = 3, name = (null)}
operation---3---2017-08-10 05:15:52 +0000---<NSThread: 0x60800007bdc0>{number = 4, name = (null)}

```



#### 四. KVO

官方文档中可以查到更多的支持KVO的属性。当自己写Operation的子类时，需要注意处理各状态

```swift
    
    //MARK: - Operation
    func operationDemo() {
        
        let queue = OperationQueue.init()
        print("start-------")

        let operation1 = BlockOperation.init { 
            print("operation---1---\(Date.init())---\(Thread.current)")
        }
        let operation2 = BlockOperation.init {
            print("operation---2---\(Date.init())---\(Thread.current)")
            sleep(3)
        }
        let operation3 = BlockOperation.init {
            print("operation---3---\(Date.init())---\(Thread.current)")
        }
        
        operation2.addDependency(operation1)
        operation3.addDependency(operation2)
        
        
        operation2.addObserver(self, forKeyPath: "isFinished", options: .new, context: nil)
        
        
        queue.addOperation(operation1)
        queue.addOperation(operation2)
        queue.addOperation(operation3)
        print("end-------")
        
    }
    
    
    override func observeValue(forKeyPath keyPath: String?, of object: Any?, change: [NSKeyValueChangeKey : Any]?, context: UnsafeMutableRawPointer?) {
        if keyPath == "isFinished"
        {
            print("\(String(describing: change?[NSKeyValueChangeKey.newKey]))")
        }
    }

```

output:

```
start-------
end-------
operation---1---2017-08-10 05:33:26 +0000---<NSThread: 0x60800007bd00>{number = 3, name = (null)}
operation---2---2017-08-10 05:33:26 +0000---<NSThread: 0x60800007bd00>{number = 3, name = (null)}
operation---3---2017-08-10 05:33:29 +0000---<NSThread: 0x608000072e80>{number = 4, name = (null)}
Optional(1)

```