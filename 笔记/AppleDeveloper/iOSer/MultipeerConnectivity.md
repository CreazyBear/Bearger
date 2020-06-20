# MultipeerConnectivity



[官方文档](https://developer.apple.com/documentation/multipeerconnectivity?language=objc)





<img src="MultipeerConnectivity.assets/image-20200611235436293.png" alt="image-20200611235436293" style="zoom:33%;" />



根据官方文档中所描述的，连接中存在两个概念：（1）广播者；（2）扫描者。已经和蓝牙广播有些类似了。所以下面我就直接类比服务器和客户端来表述了。

## 服务器

服务器需要做的事情可以分两步：（1）创建对象；（2）实现协议。

1. 这里需要使用的有三个，一个广播器，一个任务管理器加上一个id来标识自己。
2. 协议是MCAdvertiserAssistantDelegate和MCSessionDelegate

### 创建对象

1. MCAdvertiserAssistant

   服务器向外发送广播，让客户端来扫描并连接。要发送广播就需要一个广播器MCAdvertiserAssistant

   ```objc
   @property (nonatomic, strong) MCAdvertiserAssistant * advertiser;
   
   -(MCAdvertiserAssistant *)advertiser {
       if (_advertiser) {
           return _advertiser;
       }
       _advertiser = [[MCAdvertiserAssistant alloc] initWithServiceType:@"clipber-123" discoveryInfo:nil session:self.session];
       _advertiser.delegate = self;
       return _advertiser;
   }
   ```

   

   在创造广播器的时候有两个是必须的：（1）serviceType；（2）session。

   discoveryInfo用来传递一些信息给扫描器。**如果把加密信息放在这里呢？**



2. MCSession	

   ```objc
   @property (nonatomic, strong) MCSession * session;
   
   -(MCSession *)session {
       if (_session) {
           return _session;
       }
       _session = [[MCSession alloc] initWithPeer:self.peerId];
       _session.delegate = self;
       return _session;
   }
   ```

   

3. MCPeerID

   根据官方的文档，说是建议把peerID进行持久化存储。

   ```objc
   @property (nonatomic, strong) MCPeerID *peerId;
   
   -(MCPeerID *)peerId {
       
       if (_peerId) {
           return _peerId;
       }
       NSData * peerIDData = [GVUserDefaults standardUserDefaults].peerIDData;
       MCPeerID *peerID;
       if (peerIDData) {
           NSError * error;
           peerID = [NSKeyedUnarchiver unarchivedObjectOfClass:MCPeerID.class fromData:peerIDData error:&error];
           if (error) {
               peerID = [[MCPeerID alloc] initWithDisplayName:ClipberManagerMCPearIdMaster];
               NSError * error;
               NSData *peerIDData = [NSKeyedArchiver archivedDataWithRootObject:peerID requiringSecureCoding:NO error:&error];
               [GVUserDefaults standardUserDefaults].peerIDData = peerIDData;
           }
       } else {
           peerID = [[MCPeerID alloc] initWithDisplayName:ClipberManagerMCPearIdMaster];
           NSError * error;
           NSData *peerIDData = [NSKeyedArchiver archivedDataWithRootObject:peerID requiringSecureCoding:NO error:&error];
           [GVUserDefaults standardUserDefaults].peerIDData = peerIDData;
       }
       _peerId = peerID;
       return _peerId;
   }
   ```

### 实现代理

以上三个就是我们创建服务器所需要的对象。然后就实现对应的代理就成了。

```objc
#pragma mark - MCAdvertiserAssistantDelegate
// An invitation will be presented to the user.
- (void)advertiserAssistantWillPresentInvitation:(MCAdvertiserAssistant *)advertiserAssistant {
}

// An invitation was dismissed from screen.
- (void)advertiserAssistantDidDismissInvitation:(MCAdvertiserAssistant *)advertiserAssistant {
}

#pragma mark - MCSessionDelegate
// Remote peer changed state.
- (void)session:(MCSession *)session peer:(MCPeerID *)peerID didChangeState:(MCSessionState)state {
    NSLog(@"didChangeState");
    switch (state) {
        case MCSessionStateConnected:
            NSLog(@"连接成功.");
            break;
        case MCSessionStateConnecting:
            NSLog(@"正在连接...");
            break;
        default:
            NSLog(@"连接失败.");
            break;
    }
}

// Received data from remote peer.
- (void)session:(MCSession *)session didReceiveData:(NSData *)data fromPeer:(MCPeerID *)peerID {
    
}

// Received a byte stream from remote peer.
- (void)    session:(MCSession *)session
   didReceiveStream:(NSInputStream *)stream
           withName:(NSString *)streamName
           fromPeer:(MCPeerID *)peerID {
    
}

// Start receiving a resource from remote peer.
- (void)                    session:(MCSession *)session
  didStartReceivingResourceWithName:(NSString *)resourceName
                           fromPeer:(MCPeerID *)peerID
                       withProgress:(NSProgress *)progress {
    
}

// Finished receiving a resource from remote peer and saved the content
// in a temporary location - the app is responsible for moving the file
// to a permanent location within its sandbox.
- (void)                    session:(MCSession *)session
 didFinishReceivingResourceWithName:(NSString *)resourceName
                           fromPeer:(MCPeerID *)peerID
                              atURL:(nullable NSURL *)localURL
                          withError:(nullable NSError *)error {
    
}

// Made first contact with peer and have identity information about the
// remote peer (certificate may be nil).
- (void)        session:(MCSession *)session
  didReceiveCertificate:(nullable NSArray *)certificate
               fromPeer:(MCPeerID *)peerID
     certificateHandler:(void (^)(BOOL accept))certificateHandler {
    if ([peerID.displayName isEqualToString:self.passport]) {
        !certificateHandler?:certificateHandler(YES);
    }
    else {
        !certificateHandler?:certificateHandler(NO);
    }
    
}
```

这里需要注意的就是最后这个方法`- (void)session: didReceiveCertificate: fromPeer: certificateHandler:`按文档中所说的，我们需要调用这个handler

> Your app should call this handler with a value of `YES` if the nearby peer should be allowed to join the session, or `NO`otherwise

是的，不调用的话就连接失败。我在这里简单加了一个校验，将密码放在了peer的displayName上了。当然，个人感觉不是这么玩的。后面再探索一下正规的方案。



### 开始你的表演

`[self.advertiser start];`

到此，我们的广播已经打开。



## 客户端

客户端和服务端所做的事件类似。大致可以分两步：（1）创建对象；（2）实现协议。

1. 这里需要使用的有三个，一个扫描器，一个任务管理器加上一个id来标识自己。
2. 协议是MCBrowserViewControllerDelegate和MCSessionDelegate



### 创建对象

```objc
@property (nonatomic, strong) MCBrowserViewController * browser;
@property (nonatomic, strong) MCSession * mcsession;
@property (nonatomic, strong) MCPeerID *peerId;


-(MCBrowserViewController *)browser {
    if (_browser) {
        return _browser;
    }
    _browser =  [[MCBrowserViewController alloc]initWithServiceType:@"clipber-123" session:self.mcsession];
    _browser.delegate = self;
    return _browser;
}

-(MCSession *)mcsession {
    if (_mcsession) {
        return _mcsession;
    }
    _mcsession = [[MCSession alloc] initWithPeer:self.peerId];
    _mcsession.delegate = self;
    return _mcsession;
}

-(MCPeerID *)peerId {
    
    if (_peerId) {
        return _peerId;
    }
    //最开始的时候不太清楚如何鉴权，所以，通过扫码的形式将密码放在displayName里，认服务器去鉴权。
    if (self.password == nil || self.password.length <= 0) {
        self.password = @"hello world";
    }
    _peerId = [[MCPeerID alloc] initWithDisplayName:self.password];
    return _peerId;
}

```



### 实现代理 

```objc

#pragma mark - MCBrowserViewControllerDelegate
// Notifies the delegate, when the user taps the done button.
- (void)browserViewControllerDidFinish:(MCBrowserViewController *)browserViewController {
    [self dismissViewControllerAnimated:YES completion:nil];
    self.browser = nil;
}

// Notifies delegate that the user taps the cancel button.
- (void)browserViewControllerWasCancelled:(MCBrowserViewController *)browserViewController {
    [self dismissViewControllerAnimated:YES completion:nil];
    self.browser = nil;
}


// Notifies delegate that a peer was found; discoveryInfo can be used to
// determine whether the peer should be presented to the user, and the
// delegate should return a YES if the peer should be presented; this method
// is optional, if not implemented every nearby peer will be presented to
// the user.
- (BOOL)browserViewController:(MCBrowserViewController *)browserViewController
      shouldPresentNearbyPeer:(MCPeerID *)peerID
            withDiscoveryInfo:(nullable NSDictionary<NSString *, NSString *> *)info {
    return YES;
}

#pragma mark - MCSessionDelegate
// Remote peer changed state.
- (void)session:(MCSession *)session peer:(MCPeerID *)peerID didChangeState:(MCSessionState)state {
    NSLog(@"didChangeState");
    switch (state) {
        case MCSessionStateConnected:
            NSLog(@"连接成功.");
            break;
        case MCSessionStateConnecting:
            NSLog(@"正在连接...");
            break;
        default:
            break;
    }
}

// Received data from remote peer.
- (void)session:(MCSession *)session didReceiveData:(NSData *)data fromPeer:(MCPeerID *)peerID {
}

// Received a byte stream from remote peer.
- (void)    session:(MCSession *)session
   didReceiveStream:(NSInputStream *)stream
           withName:(NSString *)streamName
           fromPeer:(MCPeerID *)peerID {
    
}

// Start receiving a resource from remote peer.
- (void)                    session:(MCSession *)session
  didStartReceivingResourceWithName:(NSString *)resourceName
                           fromPeer:(MCPeerID *)peerID
                       withProgress:(NSProgress *)progress {
    
}

// Finished receiving a resource from remote peer and saved the content
// in a temporary location - the app is responsible for moving the file
// to a permanent location within its sandbox.
- (void)                    session:(MCSession *)session
 didFinishReceivingResourceWithName:(NSString *)resourceName
                           fromPeer:(MCPeerID *)peerID
                              atURL:(nullable NSURL *)localURL
                          withError:(nullable NSError *)error {
    
}

// Made first contact with peer and have identity information about the
// remote peer (certificate may be nil).
- (void)        session:(MCSession *)session
  didReceiveCertificate:(nullable NSArray *)certificate
               fromPeer:(MCPeerID *)peerID
     certificateHandler:(void (^)(BOOL accept))certificateHandler {
    !certificateHandler?:certificateHandler(YES);
}

```



### 开始表演

```objc
[self presentViewController:self.browser animated:YES completion:^{
        
}];
```

本次示例中直接使用了 [`MCBrowserViewController`](https://developer.apple.com/documentation/multipeerconnectivity/mcbrowserviewcontroller?language=objc)了，按文档中所说的如果需要自定义内容可以使用 [`MCNearbyServiceBrowser`](https://developer.apple.com/documentation/multipeerconnectivity/mcnearbyservicebrowser?language=objc)。





## Hello World

```objc
    NSData * data = [@"HelloWorld" dataUsingEncoding:NSUTF8StringEncoding];
    [self.session sendData:data
                   toPeers:self.session.connectedPeers
                  withMode:(MCSessionSendDataReliable) error:nil];
```



> 简单的使用流程就上面这样子了。感觉封装一下，用来传输和同步数据还是很方便的。



## 官方Demo

[官方有一个demo用这个来实现聊天](https://developer.apple.com/library/archive/samplecode/MultipeerGroupChat/Introduction/Intro.html) 可以学习一下。

<img src="MultipeerConnectivity.assets/Screen%20Shot%202020-06-14%20at%2012.16.01%20PM.png" alt="Screen Shot 2020-06-14 at 12.16.01 PM" style="zoom: 25%;" />















