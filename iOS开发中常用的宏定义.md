###iOS开发中常用的宏定义
####尺寸宏
	#define StatusBar_HEIGHT 20

	#define NavigationBar_HEIGHT 44

	#define NavigationBarIcon 20

	#define TabBar_HEIGHT 49

	#define TabBarIcon 30

	#define SCREEN_WIDTH ([UIScreen mainScreen].bounds.size.width)

	#define SCREEN_HEIGHT ([UIScreen mainScreen].bounds.size.height) 
	
	
####打印宏

* 替换NSLog来使用，debug模式下可以打印很多方法名，行信息。

		#ifdef DEBUG
		#   define DLog(fmt, ...) NSLog((@"%s [Line %d] " fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);
		#else  
		#   define DLog(...)  
		#endif

* 直接替换NSLog

		#if DEBUG  
		#define NSLog(FORMAT, ...) 	fprintf(stderr,"\nfunction:%s line:%d content:%s\n", __FUNCTION__, __LINE__, [[NSString stringWithFormat:FORMAT, ##__VA_ARGS__] UTF8String]);  
		#else  
		#define NSLog(FORMAT, ...) nil  
		#endif
		
* DEBUG  模式下打印日志,当前行以及弹出一个警告

		#ifdef DEBUG
		#   define ULog(fmt, ...)  { UIAlertView *alert = [[UIAlertView alloc] initWithTitle:[NSString stringWithFormat:@"%s\n [Line %d] ", __PRETTY_FUNCTION__, __LINE__] message:[NSString stringWithFormat:fmt, ##__VA_ARGS__]  delegate:nil cancelButtonTitle:@"Ok" otherButtonTitles:nil]; [alert show]; }
		#else
		#   define ULog(...)
		#endif
		
####系统宏
* 获取版本

		#define IOS_VERSION [[[UIDevice currentDevice] systemVersion] floatValue]
		#define CurrentSystemVersion [[UIDevice currentDevice] systemVersion]
		
* 获取当前语言

		#define CurrentLanguage ([[NSLocale preferredLanguages] objectAtIndex:0])
		
* 判断是真机还是模拟器
		
		#if TARGET_OS_IPHONE  
		//iPhone Device  
		#endif  
  
		#if TARGET_IPHONE_SIMULATOR  
		//iPhone Simulator  
		#endif
		
	
* 检查系统版本

		#define SYSTEM_VERSION_EQUAL_TO(v)                  ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] == NSOrderedSame)
		#define SYSTEM_VERSION_GREATER_THAN(v)              ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] == NSOrderedDescending)
		#define SYSTEM_VERSION_GREATER_THAN_OR_EQUAL_TO(v)  ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] != NSOrderedAscending)
		#define SYSTEM_VERSION_LESS_THAN(v)                 ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] == NSOrderedAscending)
		#define SYSTEM_VERSION_LESS_THAN_OR_EQUAL_TO(v)     ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] != NSOrderedDescending)
		
		
* 内存宏

		//使用ARC和不使用ARC
		#if __has_feature(objc_arc)
		//compiling with ARC
		#else
		// compiling without ARC
		#endif

		#pragma mark - common functions
		#define RELEASE_SAFELY(__POINTER) { [__POINTER release]; __POINTER = nil; }

		//释放一个对象
		#define SAFE_DELETE(P) if(P) { [P release], P = nil; }

		#define SAFE_RELEASE(x) [x release];x=nil
		
* 图片宏
	
		//读取本地图片
		#define LOADIMAGE(file,ext) [UIImage imageWithContentsOfFile:[[NSBundle mainBundle]pathForResource:file ofType:ext]]

		//定义UIImage对象
		#define IMAGE(A) [UIImage imageWithContentsOfFile:[[NSBundle mainBundle] pathForResource:A ofType:nil]]

		//定义UIImage对象
		//#define ImageNamed(_pointer) [UIImage imageNamed:[UIUtil imageName:_pointer]]

		//前两种宏性能高，省内存
		//第三个没有必要使用，因为我们可以使用Xcode的插件
		
		
* 颜色宏

		// rgb颜色转换（16进制->10进制）
		#define UIColorFromRGB(rgbValue) [UIColor colorWithRed:((float)((rgbValue & 0xFF0000) >> 16))/255.0 green:((float)((rgbValue & 0xFF00) >> 8))/255.0 blue:((float)(rgbValue & 0xFF))/255.0 alpha:1.0]

		//带有RGBA的颜色设置
		#define COLOR(R, G, B, A) [UIColor colorWithRed:R/255.0 green:G/255.0 blue:B/255.0 alpha:A]

		// 获取RGB颜色
		#define RGBA(r,g,b,a) [UIColor colorWithRed:r/255.0f green:g/255.0f blue:b/255.0f alpha:a]
		#define RGB(r,g,b) RGBA(r,g,b,1.0f)

		//背景色
		#define BACKGROUND_COLOR [UIColor colorWithRed:242.0/255.0 green:236.0/255.0 blue:231.0/255.0 alpha:1.0]

		//清除背景色
		#define CLEARCOLOR [UIColor clearColor]

		#pragma mark - color functions
		#define RGBCOLOR(r,g,b) [UIColor colorWithRed:(r)/255.0f green:(g)/255.0f blue:(b)/255.0f alpha:1]
		#define RGBACOLOR(r,g,b,a) [UIColor colorWithRed:(r)/255.0f green:(g)/255.0f blue:(b)/255.0f alpha:(a)]
		
		
* 其他宏
	
		//方正黑体简体字体定义
		#define FONT(F) [UIFont fontWithName:@"FZHTJW--GB1-0" size:F]

		//定义一个API
		#define APIURL                @"http://xxxxx/"
		//登陆API
		#define APILogin              [APIURL stringByAppendingString:@"Login"]

		//设置View的tag属性
		#define VIEWWITHTAG(_OBJECT, _TAG)    [_OBJECT viewWithTag : _TAG]
		//程序的本地化,引用国际化的文件
		#define MyLocal(x, ...) NSLocalizedString(x, nil)

		//G－C－D
		#define BACK(block) dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), block)
		#define MAIN(block) dispatch_async(dispatch_get_main_queue(),block)

		//NSUserDefaults 实例化
		#define USER_DEFAULT [NSUserDefaults standardUserDefaults]


		//由角度获取弧度 有弧度获取角度
		#define degreesToRadian(x) (M_PI * (x) / 180.0)
		#define radianToDegrees(radian) (radian*180.0)/(M_PI)
		
		
* 单例宏

		#define SYNTHESIZE_SINGLETON_FOR_CLASS(classname) \
		\
		static classname *shared##classname = nil; \
		\
		+ (classname *)shared##classname \
		{ \
		@synchronized(self) \
		{ \
		if (shared##classname == nil) \
		{ \
		shared##classname = [[self alloc] init]; \
		} \
		} \
		\
		return shared##classname; \
		} \
		\
		+ (id)allocWithZone:(NSZone *)zone \
		{ \
		@synchronized(self) \
		{ \
		if (shared##classname == nil) \
		{ \
		shared##classname = [super allocWithZone:zone]; \
		return shared##classname; \
		} \
		} \
		\
		return nil; \
		} \
		\
		- (id)copyWithZone:(NSZone *)zone \
		{ \
		return self; \
		}