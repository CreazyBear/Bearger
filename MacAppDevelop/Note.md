1. 让自己的应用成为当前焦点

    ```objc
    [NSApp activateIgnoringOtherApps:YES];
    ```

2. 隐藏Dock上的图标

    修改info.plist文件,在dict标签内加入如下字段.

    ```xml
    <key>LSUIElement</key>
    <true/>
    ```

3. NSDockTile 显示消息小红点

    * 添加

    ```objc
    NSDockTile *dock = [NSApp dockTile];
    if (dock！= NULL) {
        [dock setBadgeLabel:@"4"];
        [dock setShowsApplicationBadge:YES];
    }
    ```

    * 移除

    ```
    [[NSApp dockTile] setBadgeLabel:nil];
    ```

4. 设置Dock上的图标

    * 方法一：指定为一个NSImage对象

    ```objc
    [NSApp setApplicationIconImage:[NSImage imageNamed:@"dock"]];
    ```


    * 方法二：用一个自定义的View，来显示为Dock图标

    ```objc
    NSImageView *imgView = [[NSImageView alloc]init];
    imgView.frame = NSMakeRect(10, 10, 44, 44);

    imgView.imageFrameStyle = NSImageFramePhoto; //图片边框的样式
    imgView.wantsLayer = YES;
    imgView.layer.backgroundColor = [NSColor cyanColor].CGColor;

    imgView.image = [NSImage imageNamed:@"dock"];
    [[NSApp dockTile] setContentView: imgView];
    [[NSApp dockTile] display];
    ```