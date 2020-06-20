# Nav重复push vc的crash

由于特殊原因VC被做成单例了。当然一般不推荐这么干，但特殊需求没办法。一般情况下也不会发生崩溃。我在几个手机上试过了都没有问题。但线上就是有偶现的crash。日志如下。

```objective-c
Application Specific Information:
*** Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: 'Pushing the same view controller instance more than once is not supported ()'

Last Exception Backtrace:
0   CoreFoundation                       ___exceptionPreprocess
1   libobjc.A.dylib                      _objc_exception_throw
2   UIKit                                -[UINavigationController loadView]
3   UIKit                                -[UINavigationController pushViewController:animated:]
20  UIKit                                -[UIApplication sendAction:to:from:forEvent:]
21  UIKit                                -[UIControl sendAction:to:forEvent:]
22  UIKit                                -[UIControl _sendActionsForEvents:withEvent:]
23  UIKit                                -[UIControl touchesEnded:withEvent:]
24  UIKit                                __UIGestureEnvironmentSortAndSendDelayedTouches
25  UIKit                                __UIGestureEnvironmentUpdate
26  UIKit                                -[UIGestureEnvironment _deliverEvent:toGestureRecognizers:usingBlock:]
27  UIKit                                -[UIGestureEnvironment _updateGesturesForEvent:window:]
28  UIKit                                -[UIWindow sendEvent:]
29  UIKit                                -[UIApplication sendEvent:]
30  UIKit                                ___dispatchPreprocessedEventFromEventQueue
31  UIKit                                ___handleEventQueue
32  UIKit                                ___handleEventQueue
33  CoreFoundation                       ___CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__
34  CoreFoundation                       ___CFRunLoopDoSources0
35  CoreFoundation                       ___CFRunLoopRun
36  CoreFoundation                       _CFRunLoopRunSpecific
37  GraphicsServices                     _GSEventRunModal
40  libdyld.dylib                        _start
```

当然，我在手机是上无法复现的。包括代码里写死强制push，都没有问题。网上能搜索到的结果就是说在push前判断一下topvc是不是当前的类。然并卵。
最后，我在自己的iPod上强制push时得到一个类似的crash。当然只是类似，都崩在`[UINavigationController pushViewController]`这里。
下面这个crash崩溃的原因确定就是vc是单例。向栈里push两个同一个对象时，在iPod上直接崩掉了。


``` objective-c
warning: could not execute support code to read Objective-C class data in the process. This may reduce the quality of type information available.
(lldb) bt
* thread #1, queue = 'com.apple.main-thread', stop reason = breakpoint 1.4
    frame #0: 0x00000001a3bea9c0 libobjc.A.dylib`objc_exception_throw
    frame #1: 0x00000001cfc171f0 UIKitCore`-[UINavigationController pushViewController:transition:forceImmediate:] + 2312
    frame #2: 0x00000001cfc16784 UIKitCore`-[UINavigationController pushViewController:animated:] + 664
    frame #7: 0x000000010ec60c74 libdispatch.dylib`_dispatch_client_callout + 16
    frame #8: 0x000000010ec63ffc libdispatch.dylib`_dispatch_continuation_pop + 524
    frame #9: 0x000000010ec76610 libdispatch.dylib`_dispatch_source_invoke + 1444
    frame #10: 0x000000010ec6e56c libdispatch.dylib`_dispatch_main_queue_callback_4CF + 960
    frame #11: 0x00000001a49a0ec0 CoreFoundation`__CFRUNLOOP_IS_SERVICING_THE_MAIN_DISPATCH_QUEUE__ + 12
    frame #12: 0x00000001a499bdf8 CoreFoundation`__CFRunLoopRun + 1924
    frame #13: 0x00000001a499b354 CoreFoundation`CFRunLoopRunSpecific + 436
    frame #14: 0x00000001a6b9b79c GraphicsServices`GSEventRunModal + 104
    frame #17: 0x00000001a44618e0 libdyld.dylib`start + 4

(lldb) bt
* thread #1, queue = 'com.apple.main-thread', stop reason = signal SIGABRT
    frame #0: 0x00000001a45ad0dc libsystem_kernel.dylib`__pthread_kill + 8
    frame #1: 0x00000001a4626094 libsystem_pthread.dylib`pthread_kill$VARIANT$mp + 380
    frame #2: 0x00000001a4506ea8 libsystem_c.dylib`abort + 140
    frame #3: 0x00000001a3bd3788 libc++abi.dylib`abort_message + 132
    frame #4: 0x00000001a3bd3934 libc++abi.dylib`default_terminate_handler() + 308
    frame #5: 0x00000001a3beae00 libobjc.A.dylib`_objc_terminate() + 124
    frame #6: 0x00000001a3bdf838 libc++abi.dylib`std::__terminate(void (*)()) + 16
    frame #7: 0x00000001a3bdf8c4 libc++abi.dylib`std::terminate() + 84
    frame #8: 0x00000001a3bead5c libobjc.A.dylib`objc_terminate + 12
    frame #9: 0x000000010ec60c88 libdispatch.dylib`_dispatch_client_callout + 36
    frame #10: 0x000000010ec63ffc libdispatch.dylib`_dispatch_continuation_pop + 524
    frame #11: 0x000000010ec76610 libdispatch.dylib`_dispatch_source_invoke + 1444
    frame #12: 0x000000010ec6e56c libdispatch.dylib`_dispatch_main_queue_callback_4CF + 960
    frame #13: 0x00000001a49a0ec0 CoreFoundation`__CFRUNLOOP_IS_SERVICING_THE_MAIN_DISPATCH_QUEUE__ + 12
    frame #14: 0x00000001a499bdf8 CoreFoundation`__CFRunLoopRun + 1924
    frame #15: 0x00000001a499b354 CoreFoundation`CFRunLoopRunSpecific + 436
    frame #16: 0x00000001a6b9b79c GraphicsServices`GSEventRunModal + 104
    frame #19: 0x00000001a44618e0 libdyld.dylib`start + 4
```

最后解决方法只是把vc的单例干掉，把需要单例的部分拿出来。=。=

引以为戒吧~！