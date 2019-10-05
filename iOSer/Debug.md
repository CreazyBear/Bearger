Debug

#程序员/iOS/其它

1. 输出自动释放池信息
```
po _objc_autoreleasePoolPrint()
```

2. 查看第一响应者
```objective-c
po [[[UIApplication sharedApplication] keyWindow] performSelector:@selector(firstResponder)]
```

3. 布局断点
```
UIViewAlertForUnsatisfiableConstraints
```

4. 命令
```
po
expression
bt
info malloc-history
```

5. watchpoint
```sh
(lldb) watchpoint set v _state
Watchpoint created: Watchpoint 1: addr = 0x1439f5ec8 size = 8 state = enabled type = w
    watchpoint spec = '_state'
    new value: WebSocketsState_Disconnected
(lldb) watchpoint list
Number of supported hardware watchpoints: 4
Current watchpoints:
Watchpoint 1: addr = 0x1439f5ec8 size = 8 state = enabled type = w
    watchpoint spec = '_state'
    new value: WebSocketsState_Disconnected
(lldb) watchpoint delete 1
1 watchpoints deleted.
(lldb) watchpoint list
Number of supported hardware watchpoints: 4
No watchpoints currently set.
```