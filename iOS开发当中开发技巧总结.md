###iOS开发当中开发技巧总结
#####自定义了leftBarbuttonItem左滑返回手势失效了怎么办?
 
 	
 	self.navigationItem.leftBarButtonItem = [[UIBarButtonItem alloc]
                                         initWithImage:img
                                         style:UIBarButtonItemStylePlain
                                         target:self
                                         action:@selector(onBack:)];
	self.navigationController.interactivePopGestureRecognizer.delegate = (id<UIGestureRecognizerDelegate>)self;
	
	
#####ScrollView莫名其妙不能在viewController划到顶怎么办?

	self.automaticallyAdjustsScrollViewInsets = NO;
	
	
#####想在代码里改在xib里添加的layoutAttributes,但是怎么用代码找啊?
像拉button一样的拉你的约束.nslayoutattribute也是可以拉线的.

#####怎么像safari一样滑动的时候隐藏navigationbar?
	navigationController.hidesBarsOnSwipe = Yes
	
	
#####导航条返回键带的title太讨厌了,怎么让它消失!
	[[UIBarButtonItem appearance] setBackButtonTitlePositionAdjustment:UIOffsetMake(0, -60)
                                                     forBarMetrics:UIBarMetricsDefault];
                                                    
                                                 
                                                 
                                                 
#####本来我的statusbar是lightcontent的，结果用UIImagePickerController会导致我的statusbar的样式变成黑色，怎么办？

	- (void)navigationController:(UINavigationController *)navigationController willShowViewController:(UIViewController *)viewController animated:(BOOL)animated
	{
    	[[UIApplication sharedApplication] setStatusBarStyle:UIStatusBarStyleLightContent];
	}
	
	
#####怎么把我的navigationbar弄成透明的而不是带模糊的效果？
	[self.navigationBar setBackgroundImage:[UIImage new]
                         forBarMetrics:UIBarMetricsDefault];
	self.navigationBar.shadowImage = [UIImage new];
	self.navigationBar.translucent = YES;
	
	
#####怎么改变uitextfield placeholder的颜色和位置？

1. 重写方法 
	
		- (void) drawPlaceholderInRect:(CGRect)rect
		{
    		[[UIColor blueColor] setFill];
    		[self.placeholder drawInRect:rect withFont:self.font 	lineBreakMode:UILineBreakModeTailTruncation alignment:self.textAlignment];
		}

	
2. KVC
	 
   
 		UIColor *color = [UIColor whiteColor];  
    	_userName.attributedPlaceholder = [[NSAttributedString alloc] initWithString:@"用户名" attributes:@{NSForegroundColorAttributeName: color}];  
  
   
		[_userName setValue:[UIColor whiteColor] forKeyPath:@"_placeholderLabel.textColor"];  
	
	
	
#####应该使用FOUNDATION_EXPORT还是#define来定义常量?
一般iOS我们定义常量的方法有两种,来看下面例子
我的.h文件

	FOUNDATION_EXPORT NSString * const kMyConstantString;  
	FOUNDATION_EXPORT NSString * const kMyConstantString2;
.m文件是这样定义的

	NSString * const kMyConstantString = @"Hello";
	NSString * const kMyConstantString2 = @"World";
还有一种是常用的#define方法了

	#define kMyConstantString @"Hello"

有什么区别呢?
使用第一种方法在检测字符串的值是否相等的时候更快.对于第一种你可以直接使用`(stringInstance == MyFirstConstant)`来比较,而`define`则使用的是这种`.([stringInstance isEqualToString:MyFirstConstant])`
哪个效率高,显而易见了.第一种直接比较的是指针地址,而第二个则是一一比较字符串的每一个字符是否相等.

#####static inline function是干嘛的?
如果你的.m文件需要频繁调用一个函数,可以用static inline来声明,这相当于把函数体当做一个大号的宏定义.不过这也不是百分之百有效,到底能不能把函数体转换为大号宏定义来用要看编译器心情,它要是觉得你的方法太复杂,他就不转了.他直接调用函数.
类似这种简单函数他肯定是乐意的.

	static inline CGRect ScaleRect(CGRect rect, float n)

#####