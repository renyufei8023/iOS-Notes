##ReactiveCocoa
####ReactiveCocoa signal（RACSignal）
<br/>发送事件流给他的subscriber。目前总共有三种事件：next，error，completed。一个signal在因error终止或者完成前，可以发送任意数量的next事件。<br/>
<br/>ReactiveCocoa有很多操作来控制事件流。假设你只关心超过3个字符长度的用户名，那么你可以使用`filter`操作来实现这个目的。<br/>
		
	[[self.usernameTextField.rac_textSignal
	filter:^BOOL(id value){
  		 NSString*text = value;
   		return text.length > 3;
	}]
	subscribeNext:^(id x){
   		NSLog(@"%@", x);
  	}];//只有当长度大于3的时候才会输入x
  	
用图形来表达就是下面这样的：
  	 
![Alt text](http://cdn4.raywenderlich.com/wp-content/uploads/2014/01/FilterPipeline.png)
从上面的图中可以看到，`rac_textSignal`是起始事件。然后数据通过一个`filter`，如果这个事件包含一个长度超过3的字符串，那么该事件就可以通过。管道的最后一步就是`subscribeNex`t:，block在这里打印出事件的值。

<br/>`filter`操作的输出也是`RACSignal`，这点先放到一边。你可以像下面那样调整一下代码来展示每一步的操作。

	RACSignal *usernameSourceSignal =
    self.usernameTextField.rac_textSignal;
 
	RACSignal *filteredUsername =[usernameSourceSignal
  		filter:^BOOL(id value){
    	NSString*text = value;
    	return text.length > 3;
  	}];
 
	[filteredUsername subscribeNext:^(id x){
  		NSLog(@"%@", x);
	}];
	
`RACSignal`的每个操作都会返回一个`RACSignal`，这在术语上叫做连贯接口`（fluent interface）`。这个功能可以让你直接构建管道，而不用每一步都使用本地变量。
###类型转换
在上面的代码当中，是将id类型隐式转换为NSString，看不起不是太好看，但是signal传过来的时候就是传的NSString，可以直接修改参数类型。
	
	[[self.usernameTextField.rac_textSignal
  		filter:^BOOL(NSString*text){
    	return text.length > 3;
  	}]
  		subscribeNext:^(id x){
    	NSLog(@"%@", x);
  	}];
  	
###什么是事件

	[[[self.usernameTextField.rac_textSignal
  		map:^id(NSString*text){
    	return @(text.length);
  	}]
  	filter:^BOOL(NSNumber*length){
    	return[length integerValue] > 3;
  	}]
  	subscribeNext:^(id x){
    	NSLog(@"%@", x);
  	}];
  	
 这段代码输出的文本的长度二不再是文本的内容了
 <br/>新加的`map`操作通过block改变了事件的数据。`map`从上一个`next`事件接收数据，通过执行block把返回值传给下一个`next`事件。在上面的代码中，`map`以`NSString`为输入，取字符串的长度，返回一个`NSNumber`。
 ![<Alt text>](http://cdn2.raywenderlich.com/wp-content/uploads/2014/01/FilterAndMapPipeline.png)
 