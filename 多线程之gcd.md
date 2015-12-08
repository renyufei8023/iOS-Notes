##多线程之GCD
####任务
- 同步任务  同步任务不具备开启线程的能力`dispatch_sync`
- 异步任务 异步任务具备开启线程的能力`dispatch_async`

####队列
- 并发队列 多个任务可以同时执行
- 串行队列 一个任务执行完成后，再去执行下一个任务

		dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);//获得全局并发队
		dispatch_async(queue, ^{
        NSLog(@"-----下载图片1---%@", [NSThread currentThread]);
    	});
    	dispatch_async(queue, ^{
        	NSLog(@"-----下载图片2---%@", [NSThread currentThread]);
    	});
    	dispatch_async(queue, ^{
        	NSLog(@"-----下载图片3---%@", [NSThread currentThread]);
    	});
    	dispatch_async(queue, ^{
        	NSLog(@"-----下载图片4---%@", [NSThread currentThread]);
    	});
    	dispatch_async(queue, ^{
       		 NSLog(@"-----下载图片5---%@", [NSThread currentThread]);
    	});
    	
创建了5条线程，异步任务




