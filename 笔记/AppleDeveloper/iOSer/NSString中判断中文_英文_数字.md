# NSString中判断中文_英文_数字
#程序员/iOS/数据

```objective-c
NSString *testString = @"春1mianBU觉晓";
NSString *perchar;
    int alength = [testString length];
    for (int i = 0; i<alength; i++) {
        char commitChar = [testString characterAtIndex:i];
        NSString *temp = [testString substringWithRange:NSMakeRange(i,1)];
        const char *u8Temp = [temp UTF8String];
        if (3==strlen(u8Temp)){
            NSLog(@"字符串中含有中文");
        }else if((commitChar>64)&&(commitChar<91)){
    NSLog(@"字符串中含有大写英文字母");
        }else if((commitChar>96)&&(commitChar<123)){
            NSLog(@"字符串中含有小写英文字母");
        }else if((commitChar>47)&&(commitChar<58)){
            NSLog(@"字符串中含有数字");
        }else{
    NSLog(@"字符串中含有非法字符");
}
}

```


```objective-c
NSString *str = @"i'm a 苹果。...";
for(int i=0; i< [str length];i++){
int a = [str characterAtIndex:i];
if( a > 0x4e00 && a < 0x9fff) NSLog(@"汉字");
}
```