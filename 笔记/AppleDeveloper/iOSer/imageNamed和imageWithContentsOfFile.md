# imageNamed  && imageWithContentsOfFile:
#程序员/iOS/其它

imageNamed会缓存图片：将图片资源从硬盘缓存到内存中。
imageWithContentsOfFile会每次都从硬盘加载图片，不会缓存。


```objective-c

   
    __block UIImage * image1;
    __block UIImage * image2;
    __block UIImage * image3;
    __block UIImage * image4;
    __block UIImage * image5;

    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        
        NSDate* tmpStartData = [NSDate date];
        image1 = [UIImage imageNamed:@"config"];
        double deltaTime = [[NSDate date] timeIntervalSinceDate:tmpStartData];
        NSLog(@"cost time = %f", deltaTime);
        
        
    });
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        
        
        NSDate* tmpStartData = [NSDate date];
        image2 = [UIImage imageNamed:@"config"];
        double deltaTime = [[NSDate date] timeIntervalSinceDate:tmpStartData];
        NSLog(@"cost time = %f", deltaTime);
    });
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        NSDate* tmpStartData = [NSDate date];
        image3 = [UIImage imageNamed:@"config"];
        double deltaTime = [[NSDate date] timeIntervalSinceDate:tmpStartData];
        NSLog(@"cost time = %f", deltaTime);
    });
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(4 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        NSDate* tmpStartData = [NSDate date];
        image4 = [UIImage imageNamed:@"config"];
        double deltaTime = [[NSDate date] timeIntervalSinceDate:tmpStartData];
        NSLog(@"cost time = %f", deltaTime);
    });
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        NSDate* tmpStartData = [NSDate date];
        image5 = [UIImage imageNamed:@"config"];
        double deltaTime = [[NSDate date] timeIntervalSinceDate:tmpStartData];
        NSLog(@"cost time = %f", deltaTime);
    });
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(6 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        NSLog(@"%@",image1);
        NSLog(@"%@",image2);
        NSLog(@"%@",image3);
        NSLog(@"%@",image4);
        NSLog(@"%@",image5);
        
    });
    

```

```
2017-11-10 17:08:50.728916+0800 OCAppLab[16371:232330] cost time = 0.000210
2017-11-10 17:08:51.893047+0800 OCAppLab[16371:232330] cost time = 0.000179
2017-11-10 17:08:53.000807+0800 OCAppLab[16371:232330] cost time = 0.000170
2017-11-10 17:08:54.106883+0800 OCAppLab[16371:232330] cost time = 0.000135
2017-11-10 17:08:55.182578+0800 OCAppLab[16371:232330] cost time = 0.000162
2017-11-10 17:08:56.288286+0800 OCAppLab[16371:232330] <UIImage: 0x6000000b2cc0>, {64, 64}
2017-11-10 17:08:56.288536+0800 OCAppLab[16371:232330] <UIImage: 0x6040000b3260>, {64, 64}
2017-11-10 17:08:56.288774+0800 OCAppLab[16371:232330] <UIImage: 0x6000000b2d20>, {64, 64}
2017-11-10 17:08:56.288967+0800 OCAppLab[16371:232330] <UIImage: 0x6000000b2d80>, {64, 64}
2017-11-10 17:08:56.289307+0800 OCAppLab[16371:232330] <UIImage: 0x6000000b2de0>, {64, 64}


```

结果每个对象的都有不同的地址。
经过几次试验，第一次所用的时间总比后面的长。
所以，猜测imageNamed只是将图片从硬盘加载到内存，需要的时候进行复制操作，而不是引用计数加一。
所以，一个图片在不同的页面经常用到，可以用直接用imageNamed而不用自己缓存。而如果在同一个页面多次用到，就需要自己缓存 了。例如：聊天页面的表情~！

imageNamed被系统缓存，并且由系统自己管理。在需要的时候进行释放吧~！？

