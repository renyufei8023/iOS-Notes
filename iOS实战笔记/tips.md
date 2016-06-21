# tips-开发小技巧
##### ********QQ自动回复**********
- 将自动回复快捷消息放入<关键字-快捷消息>字典，key是关键词，value是回复语
- 由于发送的关键词不确定是一个字或者多个字，所以需要遍历依次判断
```objc
-(NSString *)autoReplace:(NSString *)str{
    NSString *str2;//定义一个str2，用来临时存放从文本框依次取出的1、2、3...个字
    //遍历文本框输入的语句
    for (int i=0; i<str.length; i++) {
       str2=[str substringWithRange:NSMakeRange(i, 1)];
       //每得到一次str2就从字典取值，如果可以取成功，就直接返回对应的快捷语
        if (self.autoarray[str2]) {
            return self.autoarray[str2];
        }
    }//文本框里面的字没有和字典里面匹配的，直接返回“哈哈”
    return @"哈哈";
}
```
##### ******九宫格快速计算******

```objc
       #define COLUMN 8
       #define ROW 3
       #define MARGIN 10
       #define SCREENW [UIScreen mainScreen].bounds.size.width
       #define SCREENH [UIScreen mainScreen].bounds.size.height

        int col=i%COLUMN;
        int row=i/COLUMN;
        CGFloat  btnW=(self.optioView.frame.size.width-(COLUMN+1)*MARGIN)/COLUMN;
        CGFloat  btnH=(self.optioView.frame.size.height-(ROW+1)*MARGIN)/ROW;
        CGFloat btnX=MARGIN + col * (btnW + MARGIN);
        CGFloat btnY=MARGIN + row *(btnH + MARGIN);
        btn.frame=CGRectMake(btnX, btnY, btnW, btnH);

```

##### ******SVProgressHUD和MBProgressHUD的基本用法********
![](images/SV和MB的基本用法.png)
*************************************
![](images/显示进度值的下载指示器.png)

##### ******MJExtension.h的使用********
- 简单的字典转模型:

```objc
//(实质上responseObject是个字典，里面的key[@"statuses"]也是一个字典,所以responseObject[@"statuses"]也是字典)
//把responseObject[@"statuses"]这个字典数组,通过objectArrayWithKeyValuesArray这个方法转成LBstatus这个类型的模型。
self.statuses(模型数组)=[LBstatus(模型类) objectArrayWithKeyValuesArray:responseObject[@"statuses"]];
```
- 模型转字典： NSDictionary ＊dict＝［模型对象  keyValues］；

- MJExtension框架特别需要注意的地方：
 - 如果字典里面有id这个key，这样你会想着把你的模型里面的属性也命名为id，但是id属于oc的一个数据类型，所以为了不发生命名冲突的事情，您需要在模型的.m文件中实现replacedKeyFromPropertyName这个方法，在这个方法中添加 return @{@”ID”:@”id”};即可
 - 复杂的字典转模型
   -  人模型里面拥有自己的姓名name，有一个书本数组books，书本模型里面拥有自己的书名name以及价格price，如果一个字典中包含人姓名以及书本数组，书本数组里面包含书本，书本里面包含书名以及价格，
   -  这个时候想使用objectWithKey将字典转成模型之前，必须使用objectClassInArray这个方法说明书本数组里面将来想存放的是什么类型的模型，
   -  也就是想把书本数组按照什么类型转（return @{@”books”:[LBBook class]};）。
   -  这个方法需要在拥有书本数组的类里面实现

##### ******解档/归档的使用********
- 自定义的对象需要存入沙盒的话需要使用解档/归档比较好
 - 对象需要遵守NSCoding协议
 - 在对象所在的类，实现-(void)encodeWithCoder:和-(instancetype)initWithCoder:方法，说明对象的哪些属性需要怎样存，以及怎样取
 - 最好提供一个工具类，提供一个保存归档对象的类方法以及一个读取解档对象的类方法，一般开发中都是直接读取数组，数组里面是需要解档归档的对象
 - 注意点：通过数组去操作归档解档的对象，那么直接在懒加载中获取这个数组即可，但是需要多一个判断，如果数组首次进来为空，需要创建新的数组

```objc
-(void)encodeWithCoder:(NSCoder *)aCoder{
    [aCoder encodeObject:self.name forKey:@"name"];
}

-(instancetype)initWithCoder:(NSCoder *)aDecoder{
    if (self=[super init]) {
    self.name=[aDecoder decodeObjectForKey:@"name"];
    }
    return self;
}
```
---
- //点击就自动设置第0列第0行到textfield上，给textfield写一个分类，只定义setUpText方法，不用谢实现，在每一个子类textfield上实现，然后在控制器.m文件内部的键盘开始编辑代理方法中让父textfield调用分类方法即可
- //父类指针指向子类--多态
- // 父类调用子类方法 ---不是多态
---
- 比如只有2张图片，图片名有规律（name1，name2），想让一个imageView自动切换图片显示的话，可以对2取模再加1
- 数值转弧度
 - CGFloat angle=(10)/(180*M_PI);
---
- 设置随机色
 - CGFloat r = arc4random_uniform(256)/255.0;
