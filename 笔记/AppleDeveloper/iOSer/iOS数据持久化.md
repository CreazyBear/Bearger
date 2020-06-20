# iOS数据持久化
#程序员/iOS/数据

# 一、持久化方案：
1. plist文件（属性列表）
2. preference（偏好设置）：NSUserDefaults
3. NSKeyedArchiver（归档）：NSKeyedArchiver 
4. SQLite 3
5. CoreData

- - - -

# 二、沙盒

```objc
      NSString *homePath = NSHomeDirectory();
    NSLog(@"homePath:\t%@",homePath);
    
    NSString * bundlePath = [[NSBundle mainBundle]bundlePath];
    NSLog(@"bundlePath:\t%@",bundlePath);
    
    NSString *documentPath = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject;
    NSLog(@"documentPath:\t%@", documentPath);
    
    NSString *cachePath = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES).firstObject;
    NSLog(@"cachePath:\t%@", cachePath);
    
    NSString *preferencePath = NSSearchPathForDirectoriesInDomains(NSPreferencePanesDirectory, NSUserDomainMask, YES).firstObject;
    NSLog(@"preferencePath:\t%@",preferencePath);
    
    NSString *libPath = NSSearchPathForDirectoriesInDomains(NSLibraryDirectory, NSUserDomainMask, YES).firstObject;
    NSLog(@"libPath:\t%@", libPath);
    
    NSString *tempPath = NSTemporaryDirectory();
    NSLog(@"tempPath:\t%@", tempPath);

```

- - - -

# 三、plist
plist文件是将某些特定的类，通过XML文件的方式保存在目录中。

可以被序列化的类型只有如下几种：
[Introduction to Property Lists](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PropertyLists/Introduction/Introduction.html#//apple_ref/doc/uid/10000048i)
* NSArray;
* NSMutableArray;
* NSDictionary;
* NSMutableDictionary;
* NSData;
* NSMutableData;
* NSString;
* NSMutableString;
* NSNumber;
* NSDate;

```objc
    NSString *path = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject;
    NSString *fileName = [path stringByAppendingPathComponent:@"123.plist"];
    
    NSArray *array = @[@"123", @"456", @"789"];
    [array writeToFile:fileName atomically:YES];

    NSArray *result = [NSArray arrayWithContentsOfFile:fileName];
    NSLog(@"%@", result);
```

![](iOS%E6%95%B0%E6%8D%AE%E6%8C%81%E4%B9%85%E5%8C%96/1A38046E-9279-4A86-BAC5-319A45F597B0.png)

- - - -

# 四、Preference
缺点：如果要存储其他类型，需要转换为前面的类型，才能用NSUserDefaults存储。
```objc
//1.获得NSUserDefaults文件
NSUserDefaults *userDefaults = [NSUserDefaults standardUserDefaults];
//2.向文件中写入内容
[userDefaults setObject:@"AAA" forKey:@"a"];
[userDefaults setBool:YES forKey:@"sex"];
[userDefaults setInteger:21 forKey:@"age"];
//2.1立即同步
[userDefaults synchronize];
//3.读取文件
NSString *name = [userDefaults objectForKey:@"a"];
BOOL sex = [userDefaults boolForKey:@"sex"];
NSInteger age = [userDefaults integerForKey:@"age"];
NSLog(@"%@, %d, %ld", name, sex, age);
```

- - - -

# 五、NSKeyedArchiver
缺点：只能一次性归档保存以及一次性解压。所以只能针对小量数据，对数据操作比较笨拙，如果想改动数据的某一小部分，需要解压或归档整个数据。

```objc
//.h
#import <Foundation/Foundation.h>

@interface Person : NSObject
@property (nonatomic, copy) NSString *name;
@property (nonatomic, assign) NSInteger age;
@property (nonatomic, assign) BOOL male;
@end

/**********************************************/
//.m
#import "Person.h"

@implementation Person


- (void)encodeWithCoder:(NSCoder *)coder
{
    [coder encodeObject:self.name forKey:@"name"];
    [coder encodeInteger:self.age forKey:@"age"];
    [coder encodeBool:self.male forKey:@"male"];
}

- (instancetype)initWithCoder:(NSCoder *)coder
{
    if (self) {
        self.name = [coder decodeObjectForKey:@"name"];
        self.age = [coder decodeIntegerForKey:@"age"];
        self.male = [coder decodeBoolForKey:@"male"];
    }
    return self;
}

- (NSString *)description
{
    return [NSString stringWithFormat:@"name:%@,age:%ld,male:%d", self.name,self.age,self.male];
}

@end

/**********************************************/
//use
    {
        NSString *file = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject stringByAppendingPathComponent:@"person.data"];
        Person *person = [[Person alloc] init];
        person.name = @"bear";
        person.age = 28;
        person.male = YES;
        [NSKeyedArchiver archiveRootObject:person toFile:file];
    }
    
    {
        NSString *file = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject stringByAppendingPathComponent:@"person.data"];
        Person *person = [NSKeyedUnarchiver unarchiveObjectWithFile:file];
        NSLog(@"%@",person);
    }

```

result:
![](iOS%E6%95%B0%E6%8D%AE%E6%8C%81%E4%B9%85%E5%8C%96/DD0A365E-721A-4324-8429-3873222094E0.png)

内容是被序列化的数据

- - - -

# 六、数据库
之前的所有存储方法，都是覆盖存储。如果想要增加一条数据就必须把整个文件读出来，然后修改数据后再把整个内容覆盖写入文件。所以它们都不适合存储大量的内容。

## FMDB(SQLite3)
[GitHub - ccgus/fmdb: A Cocoa / Objective-C wrapper around SQLite](https://github.com/ccgus/fmdb)

FMDB是iOS平台的SQLite数据库框架，它以OC的方式封装了SQLite的C语言API。FMDB的优点：
（1）使用起来更加面向对象，省去了很多麻烦、冗余的C语言代码。
（2）对比苹果自带的Core Data框架，更加轻量级和灵活。
（3）提供了多线程安全的数据库操作方法，有效地防止数据混乱。

简单的示例：
```objc
//pod 'FMDB'
#import <FMDB.h>

-(void)fmdbDemo{
    //打开数据库
    NSString *path = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject stringByAppendingPathComponent:@"person.db"];
    FMDatabase *database = [FMDatabase databaseWithPath:path];
    if (![database open]) {
        NSLog(@"数据库打开失败！");
    }
    
    
    /*
    update 常用方法有以下3种：
    - (BOOL)executeUpdate:(NSString*)sql, ...
    - (BOOL)executeUpdateWithFormat:(NSString*)format, ...
    - (BOOL)executeUpdate:(NSString*)sql withArgumentsInArray:(NSArray *)arguments
     */
    [database executeUpdate:@"CREATE TABLE IF NOT EXISTS t_person(id integer primary key autoincrement, name text, age integer)"];
    [database executeUpdate:@"INSERT INTO t_person(name, age) VALUES(?, ?)", @"Bourne", [NSNumber numberWithInt:42]];

    
    /*
     query 查询方法也有3种，使用起来相当简单：
     - (FMResultSet *)executeQuery:(NSString*)sql, ...
     - (FMResultSet *)executeQueryWithFormat:(NSString*)format, ...
     - (FMResultSet *)executeQuery:(NSString *)sql withArgumentsInArray:(NSArray *)arguments
     */
    //1.执行查询
    FMResultSet *result = [database executeQuery:@"SELECT * FROM t_person"];
    //2.遍历结果集
    while ([result next]) {
        NSString *name = [result stringForColumn:@"name"];
        int age = [result intForColumn:@"age"];
        NSLog(@"name:%@;\nage:%d",name,age);
    }
}
```


## CoreData
