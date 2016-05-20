#NSOperation
- NSOperation的作用
 - 配合使用NSOperation和NSOperationQueue也能实现多线程编程

- NSOperation和NSOperationQueue实现多线程的具体步骤
 - 先将需要执行的操作封装到一个NSOperation对象中
 - 然后将NSOperation对象添加到NSOperationQueue中
 - 系统会自动将NSOperationQueue中的NSOperation取出来
 - 将取出的NSOperation封装的操作放到一条新线程中执行
 
###NSOperation的子类
- NSOperation是个抽象类，并不具备封装操作的能力，必须使用它的子类

- 使用NSOperation子类的方式有3种
 - NSInvocationOperation
 - NSBlockOperation
 - 自定义子类继承NSOperation，实现内部相应的方法

####NSInvocationOperation
- 创建NSInvocationOperation对象
`- (id)initWithTarget:(id)target selector:(SEL)sel object:(id)arg;`

- 调用start方法开始执行操作
`- (void)start;`
一旦执行操作，就会调用target的sel方法

- 注意
 - 默认情况下，调用了start方法后并不会开一条新线程去执行操作，而是在当前线程同步执行操作
 - 只有将NSOperation放到一个NSOperationQueue中，才会异步执行操作

实现代码如下

```objc
- (void)InvocationOperation
{
    //不加入队列中默认在主线程中
    NSInvocationOperation *op = [[NSInvocationOperation alloc]initWithTarget:self selector:@selector(run) object:nil];
    [op start];//必须得调用run方法，不然那不会执行
}
```
####NSBlockOperation
- 创建NSBlockOperation对象
`+ (id)blockOperationWithBlock:(void (^)(void))block;`

- 通过addExecutionBlock:方法添加更多的操作
`- (void)addExecutionBlock:(void (^)(void))block;`

注意：只要NSBlockOperation封装的操作数 > 1，就会异步执行操作,等于1的时候默认是同步的


```objc
- (void)BlockOperation
{
    //不加入队列中默认在主线程中
    NSBlockOperation *op = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"%111111@",[NSThread currentThread]);
    }];
    //addExecutionBlock加入的任务会开启一条新的线程
    [op addExecutionBlock:^{
        NSLog(@"2222222%@",[NSThread currentThread]);
    }];
    [op addExecutionBlock:^{
        NSLog(@"333333%@",[NSThread currentThread]);
    }];
    [op addExecutionBlock:^{
        NSLog(@"444444%@",[NSThread currentThread]);
    }];
    [op start];
}
```
###NSOperationQueue
- NSOperationQueue的作用
 - NSOperation可以调用start方法来执行任务，但默认是同步执行的
 - 如果将NSOperation添加到NSOperationQueue（操作队列）中，系统会自动异步执行NSOperation中的操作

- 添加操作到NSOperationQueue中

```objc
- (void)addOperation:(NSOperation *)op;
- (void)addOperationWithBlock:(void (^)(void))block;
```
具体操作代码如下

```objc
- (void)OperationQueue1
{
    //创建一个队列
    NSOperationQueue *queue = [[NSOperationQueue alloc]init];
    
    //创建一个任务
    NSInvocationOperation *op1 = [[NSInvocationOperation alloc]initWithTarget:self selector:@selector(download1) object:nil];
    
    NSInvocationOperation *op2 = [[NSInvocationOperation alloc]initWithTarget:self selector:@selector(download1) object:nil];
    
    NSBlockOperation *op3 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"block----%@",[NSThread currentThread]);
    }];
    [op3 addExecutionBlock:^{
        NSLog(@"block----%@",[NSThread currentThread]);
    }];
    
    //把任务添加到队列当中去，相当于调用了任务的start方法
    [queue addOperation:op1];
    [queue addOperation:op2];
    [queue addOperation:op3];
}
```
###最大并发数
- 什么是并发数
 - 同时执行的任务数
 - 比如，同时开3个线程执行3个任务，并发数就是3

- 最大并发数的相关方法
```objc
- (NSInteger)maxConcurrentOperationCount;
- (void)setMaxConcurrentOperationCount:(NSInteger)cnt;
```

如果想串行执行那么只需要设置maxConcurrentOperationCount=1即可
代码如下

```objc
- (void)addOperationWithBlock
{
    NSOperationQueue *queue = [[NSOperationQueue alloc]init];
    
    queue.maxConcurrentOperationCount = 2;//最小为1，等于1的时候串行队列，
    
    [queue addOperationWithBlock:^{
        NSLog(@"-------1------%@",[NSThread currentThread]);
    }];
    
    [queue addOperationWithBlock:^{
        NSLog(@"-------2------%@",[NSThread currentThread]);
    }];
    
    
    [queue addOperationWithBlock:^{
        NSLog(@"-------3------%@",[NSThread currentThread]);
    }];
    
    
    [queue addOperationWithBlock:^{
        NSLog(@"-------14------%@",[NSThread currentThread]);
    }];
}
```
###队列的取消、暂停、恢复
- 取消队列的所有操作
`- (void)cancelAllOperations;
提示：也可以调用NSOperation的- (void)cancel方法取消单个操作`

- 暂停和恢复队列
```
- (void)setSuspended:(BOOL)b; // YES代表暂停队列，NO代表恢复队列
- (BOOL)isSuspended;
```
- 当前正在执行一个耗时操作，比如循环一万次，如果你现在想要取消或者是暂停，系统会先把当前循环执行完比之后才会取消或者暂停

队列取消暂停恢复操作如下

```objc
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    //暂停任务，当调用suspended或者cancelAllOperations都会把当前任务执行完然后再暂停或者取消
    if (self.queue.isSuspended) {
        self.queue.suspended = NO;
    }else{
        self.queue.suspended = YES;
    }
//    [self.queue cancelAllOperations];
}
```

###NSOperation的其他用法
####操作依赖
- NSOperation之间可以设置依赖来保证执行顺序
 - 比如一定要让操作A执行完后，才能执行操作B，可以这么写
`[operationB addDependency:operationA]; // 操作B依赖于操作A`

- 可以在不同queue的NSOperation之间创建依赖关系
- 不可以相互依赖，a依赖于b，b就不能再继续依赖a了
- 设置依赖要放到把任务添加到队列之前

####操作的监听
- 可以监听一个操作的执行完毕
```
- (void (^)(void))completionBlock;
- (void)setCompletionBlock:(void (^)(void))block;
```

具体操作如下

```objc
- (IBAction)depend:(id)sender {
    
    NSOperationQueue *queue = [[NSOperationQueue alloc]init];
    
    NSBlockOperation *op1 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"________第一个任务%@____",[NSThread currentThread]);
    }];
    
    NSBlockOperation *op2 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"________第二个任务%@____",[NSThread currentThread]);
    }];
    
    NSBlockOperation *op3 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"________第三个任务%@____",[NSThread currentThread]);
    }];
    
    NSBlockOperation *op4 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"________第四个任务%@____",[NSThread currentThread]);
    }];
    
    //op4完成后回调
    op4.completionBlock = ^{
        
    };
    //设置依赖
    [op2 addDependency:op1];
    [op3 addDependency:op2];
    [op4 addDependency:op3];
    
    //添加任务到队列中
    [queue addOperation:op1];
    [queue addOperation:op2];
    [queue addOperation:op3];
    [queue addOperation:op4];
    
}

```
####自定义NSOperation

- 自定义NSOperation的步骤很简单
 - 重写- (void)main方法，在里面实现想执行的任务

- 重写- (void)main方法的注意点
 - 自己创建自动释放池（因为如果是异步操作，无法访问主线程的自动释放池）
 - 经常通过- (BOOL)isCancelled方法检测操作是否被取消，对取消做出响应

###NSOperation线程之前的通信
从网络上面获取两张图片，获取成功之后然后把两张图片合成一张图片，首先需要先把两张图片下载成功，需要总共创建三个任务，其中两条任务是下载图片，另一条任务是把图片合成一张图片，这时候我们需要设置依赖，合成的任务需要依赖于下载的那两个任务，期间创建几条线程是由系统决定的，合成之后我们需要回到主线程更新UI，具体实现如下所示

```objc
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    NSOperationQueue *queue = [[NSOperationQueue alloc]init];
    
    __block UIImage *image1 = nil;
    NSBlockOperation *op1 = [NSBlockOperation blockOperationWithBlock:^{
        NSURL *url = [NSURL URLWithString:@"http://thumb.takefoto.cn/wp-content/uploads/2016/03/201603222328428366.png"];
        image1 = [UIImage imageWithData:[NSData dataWithContentsOfURL:url]];
        NSLog(@"---1----%@",[NSThread currentThread]);
    }];
    
    __block UIImage *image2 = nil;
    NSBlockOperation *op2 = [NSBlockOperation blockOperationWithBlock:^{
        NSURL *url = [NSURL URLWithString:@"http://www.52tq.net/uploads/allimg/160325/1339122J5-1.jpg"];
        image2 = [UIImage imageWithData:[NSData dataWithContentsOfURL:url]];
        NSLog(@"---12----%@",[NSThread currentThread]);
    }];
    
    NSBlockOperation *op3 = [NSBlockOperation blockOperationWithBlock:^{
        UIGraphicsBeginImageContext(CGSizeMake(100, 100));
        
        NSLog(@"---13----%@",[NSThread currentThread]);
        [image1 drawInRect:CGRectMake(0, 0, 50, 100)];
        image1 = nil;
        [image2 drawInRect:CGRectMake(50, 0, 50, 100)];
        image2 = nil;
        
        UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
        
        [[NSOperationQueue mainQueue]addOperationWithBlock:^{
            self.imageView.image = image;
        }];
        
        
        UIGraphicsEndImageContext();
        
    }];
    
    [op3 addDependency:op1];
    [op3 addDependency:op2];
    
    [queue addOperation:op1];
    [queue addOperation:op2];
    [queue addOperation:op3];
    
}

```

###使用NSOperation下载图片
实现思路如下所示

我们需要把下载的图片加载到内存而且保存至沙盒中去，这样当用户没网的时候也可以显示之前下载过的图片，这样还可以帮用户节省流量，一个好的APP应该从用户的角度出发去设计

1. 首先我们需要先根据图片的url去内存缓存中去取对应的图片，如果存在的话那么就将对应的图片显示到对应cell上面去
2. 如果内存缓存中不存在的话那么就检查沙盒中是否存在对应的图片，沙盒中如果存在的话那么就从沙盒中取出对应的图片然后显示到对应的cell上面去
3. 如果沙盒中不存在对应的图片那么我们需要先显示一个占位图(设置一个占位图，避免网络卡顿(主要是阻挡下载线程的操作)造成cell图片错误而非本行应该显示的图片，还有就是使用系统自带的cell的时候刚开始不显示图片，需要滑动才会显示的问题(造成该问题的原因是刚开始系统无法知道图片的大小尺寸从而无法正常的显示出来),不过这个占位图尺寸不要太大，不然会影响显示的图片)
4. 然后根据图片的url去我们的operations字典中查看是否存在这个下载操作，如果存在的话那么就让这个操作去执行下载任务
5. 如果图片的url在我们的operations字典中不存在这个下载操作，那么我们应该创建一个下载操作放到我们的operations这个字典中去
6. 下载之后我们把这个操作从我们的operations字典中移除(这里移除这个任务，是为了下次可以继续下载，如果不移除那么这个图片以后永远都不会再下载了),然后把图片加载到我们的内存缓存中去
7. 然后刷新表格
8. 最后把我们下载的图片存入到沙盒中去

```objc
//
//  ViewController.m
//  图片下载
//
//  Created by 任玉飞 on 16/5/9.
//  Copyright © 2016年 任玉飞. All rights reserved.
//

#import "ViewController.h"
#import "AppModel.h"

@interface ViewController ()<UITableViewDelegate,UITableViewDataSource>
@property (nonatomic, strong) NSArray *datas;
/**
 *  内存中的图片
 */
@property (nonatomic, strong) NSMutableDictionary *imageCahe;

/**
 *  所有操作的对象
 */
@property (nonatomic, strong) NSMutableDictionary *operations;

/**
 *  队列对象
 */
@property (nonatomic, strong) NSOperationQueue *queue;

@end

@implementation ViewController

- (NSOperationQueue *)queue
{
    if (!_queue) {
        _queue = [[NSOperationQueue alloc]init];
        _queue.maxConcurrentOperationCount = 3;
    }
    return _queue;
}

- (NSMutableDictionary *)operations
{
    if (_operations == nil) {
        _operations = [NSMutableDictionary dictionary];
    }
    return _operations;
}

- (NSMutableDictionary *)imageCahe
{
    if (!_imageCahe) {
        _imageCahe = [NSMutableDictionary dictionary];
    }
    return _imageCahe;
}

- (NSArray *)datas
{
    if (!_datas) {
        NSArray *apps = [NSArray arrayWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"apps.plist" ofType:nil]];
        NSMutableArray *appArray = [NSMutableArray array];
        for (NSDictionary *dict in apps) {
            [appArray addObject:[AppModel appWithDict:dict]];
        }
        _datas = appArray;
    }
    return _datas;
}

- (void)viewDidLoad {
    [super viewDidLoad];
    _tableView.delegate = self;
    _tableView.dataSource = self;
    
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    return self.datas.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    static NSString *ID = @"appCell";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:ID];
    AppModel *model = self.datas[indexPath.row];
    cell.textLabel.text = model.name;
    cell.detailTextLabel.text = model.download;
    
    //先从内存缓存中取出图片
    UIImage *image = self.imageCahe[model.icon];
    if (image) {//内存缓存中有图片的情况
        
        cell.imageView.image = image;
        
    }else{//内存缓存中没有图片的情况
        
        //获得Library/Caches文件夹
        NSString *cachesPath = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject];
        //获得url中图片名字
        NSString *filename = [model.icon lastPathComponent];
        //拼接路径
        NSString *file = [cachesPath stringByAppendingPathComponent:filename];
        //将沙盒里面的图片转换为data
        //NSData *data = nil;
        [NSData dataWithContentsOfFile:file];
        
        if (data) {//沙盒中有的话会执行这里
            UIImage *image = [UIImage imageWithData:data];
            cell.imageView.image = image;
            self.imageCahe[model.icon] = image;
            
        }else{//沙盒中没有图片，需要进行下载
            /**
               先设置一个占位图，避免网络卡顿(主要是阻挡下载线程的操作)造成cell图片错误而非本行应该显示的图片，还有就是使用系统自带的cell的时候刚开始不显示图片，需要滑动才会显示的问题(造成该问题的原因是刚开始系统无法知道图片的大小尺寸从而无法正常的显示出来),不过这个占位图尺寸不要太大，不然会影响显示的图片
             */
            cell.imageView.image = [UIImage imageNamed:@"猫.jpg"];
            
            //从内存中取出当前所对应的任务
            NSOperation *operation = self.operations[model.icon];
            
            if (operation == nil) {//任务不存在
                operation = [NSBlockOperation blockOperationWithBlock:^{
                    //下载图片
                    NSData *imageData = [NSData dataWithContentsOfURL:[NSURL URLWithString:model.icon]];
                    
                    if (imageData == nil) {//这里判断从网络上面下载失败的时候，如果不进行判断的话后面可能会崩溃
                        [self.operations removeObjectForKey:model.icon];//这里移除这个任务，是为了下次可以继续下载，如果不移除那么这个图片以后永远都不会再下载了
                        return ;
                    }
                    UIImage *image = [UIImage imageWithData:imageData];
                    
                    //存到字典中去
                    self.imageCahe[model.icon] = image;
                    
                    [[NSOperationQueue mainQueue]addOperationWithBlock:^{
//                        cell.imageView.image = image;
                        [tableView reloadRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationNone];//使用这个方法而不使用cell.imageView.image = image;是为了让他重新调用- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath从而不会造成cell从缓存池中取造成的缓存问题(就是当这一行从屏幕上离开进入缓存池的时候，然后最下面的一行就是刚放入缓存池的那个图片的问题)
                    }];
                    
                    //写入沙盒中
                    [imageData writeToFile:file atomically:YES];
                    
                    //移除操作(优化性能)，因为写入之后这个操作就没什么用了，移除是为了内存着想
                    [self.operations removeObjectForKey:model.icon];
                    
                }];
                
                //添加到队列中去
                [self.queue addOperation:operation];
                
                //存放到字典中
                self.operations[model.icon] = operation;
            }
        }
       
    }
    
    
    return cell;
}
- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    self.imageCahe = nil;//这里是收到内存警告的时候我们需要把内存缓存清空掉，并且把operations字典置空，取消队列的所有任务
    self.operations = nil;
    [self.queue cancelAllOperations];
    // Dispose of any resources that can be recreated.
}

@end

```

