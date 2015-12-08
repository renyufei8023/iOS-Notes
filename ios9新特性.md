###iOS9新特性_更灵活的后台定位
【iOS9在定位的问题上，有一个坏消息一个好消息】坏消息：如果不适配iOS9，就不能偷偷在后台定位（不带蓝条，见图）！好消息：将允许出现这种场景：同一App中的多个location manager：一些只能在前台定位，另一些可在后台定位，并可随时开启或者关闭特定location manager的后台定位。

如果没有请求后台定位的权限，也是可以在后台定位的，不过会带蓝条：
<br/>如何偷偷在后台定位：请求后台定位权限：
		
	// 1. 实例化定位管理器
	_locationManager = [[CLLocationManager alloc] init];
	// 2. 设置代理
	_locationManager.delegate = self;
	// 3. 定位精度
	[_locationManager setDesiredAccuracy:kCLLocationAccuracyBest];
	// 4.请求用户权限：分为：⓵只在前台开启定位⓶在后台也可定位，
	//注意：建议只请求⓵和⓶中的一个，如果两个权限都需要，只请求⓶即可，
	//⓵⓶这样的顺序，将导致bug：第一次启动程序后，系统将只请求⓵的权限，⓶的权限系统不会请求，只会在下一次启动应用时请求⓶
	if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 8) {
    	//[_locationManager requestWhenInUseAuthorization];//⓵只在前台开启定位
    	[_locationManager requestAlwaysAuthorization];//⓶在后台也可定位
	}
	// 5.iOS9新特性：将允许出现这种场景：同一app中多个location manager：一些只能在前台定位，另一些可在后台定位（并可随时禁止其后台定位）。
	if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 9) {
    _locationManager.allowsBackgroundLocationUpdates = YES;
	}
	// 6. 更新用户位置
	[_locationManager startUpdatingLocation];
	
	
但是如果照着这种方式尝试，而没有配置Info.plist，100%你的程序会崩溃掉，并报错：

	*** Assertion failure in -[CLLocationManager setAllowsBackgroundLocationUpdates:], /BuildRoot/Library/Caches/com.apple.xbs/Sources/CoreLocationFramework_Sim/CoreLocation-1808.1.5/Framework/CoreLocation/CLLocationManager.m:593

这个问题，有两种方式可以解决：

1. ![<Alt text>](https://camo.githubusercontent.com/0fa5c00013a32b4a0d37b53912296a21ec175cce/68747470733a2f2f692e696d6775722e636f6d2f4d416f4b6255652e706e67)
2. 在对应 target 的 Capabilities -> Background Modes -> 开启 Location Updates
![Alt text](https://camo.githubusercontent.com/77ea43af9dc61ea1c404ecfb3dda55f4c7a7edb1/687474703a2f2f63646e322e72617977656e6465726c6963682e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031342f31322f6261636b67726f756e645f6d6f6465732e706e67)
