##Pthread
####使用pthread必须盗用头文件#import <pthread.h>
可以使用`[NSThread currentThread]`来获取当前在哪条线程上面 num = 1为主线程
####创建线程
	
	pthread_t myRestrict;
	pthread_create(&myRestrict, NULL, run, NULL);run为一个void的函数


##NSThread
###创建线程
1. 第一种方式

		NSThread *thread = [NSThread alloc]initWithTarget:self selector:@selector(download:) object:@"哈哈"];//创建一个线程去调用download：方法并把哈哈传给这个方法，也可以不传值，设置为nil即可
		thread.name = @"下载线程";//可以设置线程的名称，设置与否不会造成什么影响的
		[thread start];//启动线程，必须加上，不然无法开启线程
		
	
2. 第二种方式

		[NSThread detachNewThreadSelector:@selector(download:) toTarget:self withObject:@"http://a.jpg"];
3. 第三种方式

		[self performSelectorInBackground:@selector(download:) withObject:@"http://c.gif"];
		[self performSelector:@selector(download:) withObject:@"http://c.gif"];(不会开辟新线程)
	    [self download:@"http://c.gif"];(不会开辟新线程)

###互斥锁(@synchronized)
火车票以及银行取钱等情况下需要使用互斥锁来对线程进行加锁，避免造成混乱等情况
比如三个人进行买火车票，如果不用互斥锁的话那么一张票可能被两个人或者三个人同时买到，显然这是不符合逻辑的，每张票只能允许同一个人购买。这时候就需要用到互斥锁来对火车票进行加锁了，如果某一张火车票被人购买了，那么别人就无法继续买这张火车票了。
	
	- (void)saleTicket
	{
    	while (1) {
        	// ()小括号里面放的是锁对象（一般self即可，不要重复生成对象）
        	@synchronized(self) { // 开始加锁
            	int count = self.leftTicketCount;
            	if (count > 0) {
                	[NSThread sleepForTimeInterval:0.05];
                
                	self.leftTicketCount = count - 1;
                
                	NSLog(@"%@卖了一张票, 剩余%d张票", [NSThread currentThread].name, self.leftTicketCount);
            	} else {
                	return; // 退出循环
            	}
        	} // 解锁
    	}
	}
	
###线程间通信
当处理耗时操作的时候需要开辟一条线程，比如从网络上面请求数据的时候，网络请求应该新开辟一条线程，放到子线程里面去，然后下载完成之后通知主线程进行更新UI。(这样也是为了线程安全着想)

	[self performSelectorOnMainThread:@selector(downloadFinished:) withObject:image waitUntilDone:NO];//回到主线程
	
####小结
1. -(id)init //这个方法是NSThread的指定初始化方法
2. -(id)initWithTarget:(id)target selector:(SEL)seletoc object:(id)argument//初始化一个线程，应该注意到这个SEL方法只能有一个参数，并且选择的方法不能有返回值，而且这个方法只是创建了一个线程，并没有开始这个线程，需要调用start方法开始线程
3. +(void)detachNewThreadSelector:(SEL)aSelector toTarget:(id)aTarget withObject:(id)anArgument//创建并开始一个线程
4. -(void)start//开始一个线程
5. +(void)sleepUntilDate:(NSDate *)aDate//阻塞当前线程，直到指定时间
6. +(void)sleepForTimeInterval:(NSTimeInterval)time//使线程睡眠一段时间
7. -(BOOL)isExecuting//判断线程是否正在执行
8. -(BOOL)isCancelled//判断线程是否已经取消
9. -(BOOL)isFinished//判断线程是否执行完
10. +(BOOL)isMainThread//判断当前线程是否是主线程
11. +(BOOL)isMultiThreaded//判断是否是多线程的
12. +(NSThread *)currentThread//返回当前线程
13. +(NSThread *)mainThread//返回主线程对象
14. +(NSArray *)callStackReturnAddresses//返回的是这个线程在栈中所占的地址所组成的数组
15. +(NSArray *)callStackSymbols//返回栈空间的符号
16. – threadDictionary //返回线程对象的字典
17. – name //返回线程名字
18. – setName: //设置线程名字
19. – stackSize //返回线程所占栈的空间大小
20. – setStackSize: //设置线程所占栈的空间大小
21. +threadPriority //返回当前线程的优先权，其实返回的是一个double型数字，
//从0.0 到1.0 其中1.0最高


