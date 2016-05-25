###判断是否是第一次打开应用程序
先获取CFBundleShortVersionString这个key，然后从沙盒中取这个key，如果两个相等，则表示不是第一次打开该应用程序，不相等的时候应该把沙盒中中的key保存起来，下次启动时候再进行判断
```objc
 NSString * key = @"CFBundleShortVersionString";
    
    NSString *currentVersion = [NSBundle mainBundle].infoDictionary[key];
    NSString *sandboxVersion = [[NSUserDefaults standardUserDefaults] stringForKey:key];
    if (![currentVersion isEqualToString:sandboxVersion]) {
        [[NSUserDefaults standardUserDefaults]setObject:currentVersion forKey:key];
        
    }else{
        
    }
    



###修改tabbariterm选中以及未选中颜色

我们可以调用UIBarItem的这个`- (void)setTitleTextAttributes:(nullable NSDictionary<NSString *,id> *)attributes forState:(UIControlState)state NS_AVAILABLE_IOS(5_0) UI_APPEARANCE_SELECTOR;`方法，这个方法支持在appearance的时候设置，为什么设置UIBarItem呢，因为UITabBarItem继承自UIBarItem

```objc
 	NSMutableDictionary *attrs = [NSMutableDictionary dictionary];
    attrs[NSFontAttributeName] = [UIFont systemFontOfSize:12];
    attrs[NSForegroundColorAttributeName] = [UIColor grayColor];
    
    NSMutableDictionary *selectedAttrs = [NSMutableDictionary dictionary];
    selectedAttrs[NSFontAttributeName] = attrs[NSFontAttributeName];
    selectedAttrs[NSForegroundColorAttributeName] = [UIColor darkGrayColor];
    
    UITabBarItem *item = [UITabBarItem appearance];
    [item setTitleTextAttributes:attrs forState:UIControlStateNormal];
    [item setTitleTextAttributes:selectedAttrs forState:UIControlStateSelected];
```

###替换系统自带的tabbar
我们可以使用KVC把系统的tabbar替换成我们自己的
在TabBarController的viewDidLoad中进行设置即可

```objc
[self setValue:[[RYFTabBar alloc]init] forKey:@"tabBar"];
```
###自定义tabbar
现在很多APP中都要求中间有一个加号按钮或者什么的，这个时候就需要我们自定义tabbar了，当然网上有很多自定义tabbar的demo，不过这个方式实现比较简单，我们需要声明一个协议，然后监听按钮的点击，我们先在`- (instancetype)initWithFrame:(CGRect)frame`中创建中间的按钮，然后在`layoutSubviews`中设置frame，因为吧按钮添加到tabbar上面的时候会触发`layoutSubviews`，在这个方法里面拿到的frame比较准确。代码如下

```objc
- (instancetype)initWithFrame:(CGRect)frame
{
    if (self = [super initWithFrame:frame]) {
        _plusButton = [UIButton buttonWithType:UIButtonTypeCustom];
        [_plusButton setBackgroundImage:[UIImage imageNamed:@"tabBar_publish_icon"] forState:UIControlStateNormal];
        [_plusButton setBackgroundImage:[UIImage imageNamed:@"tabBar_publish_click_icon"] forState:UIControlStateSelected];
        [_plusButton addTarget:self action:@selector(plusSignClick) forControlEvents:UIControlEventTouchUpInside];
        [self addSubview:_plusButton];
    }
    return self;
}
- (void)layoutSubviews
{
    [super layoutSubviews];
    
    _plusButton.bounds = CGRectMake(0, 0, _plusButton.currentBackgroundImage.size.width, _plusButton.currentBackgroundImage.size.height);
    _plusButton.center = CGPointMake(self.frame.size.width * 0.5, self.frame.size.height * 0.5);
    
    NSInteger index = 0;

    CGFloat buttonY = 0;
    CGFloat buttonW = self.frame.size.width / 5;
    CGFloat buttonH = self.frame.size.height;
    for (UIView *view in self.subviews) {
        if ([view isKindOfClass:NSClassFromString(@"UITabBarButton")]) {
            CGFloat buttonX = buttonW * ((index > 1) ? (index + 1) : index);
            view.frame = CGRectMake(buttonX, buttonY, buttonW, buttonH);
            index ++;
        }
    }
}

- (void)plusSignClick
{
    if ([self.tabBarDelegate respondsToSelector:@selector(plusBtnClick)]) {
        [self.tabBarDelegate plusBtnClick];
    }
}
```


###设置导航栏UIBarButtonItem颜色以及字体

```objc
/**
 *  当第一次使用这个类的时候会调用一次
 */
+ (void)initialize
{
    UINavigationBar *navigationBar = [UINavigationBar appearance];
    [navigationBar setBackgroundImage:[UIImage imageNamed:@"navigationbarBackgroundWhite"] forBarMetrics:UIBarMetricsDefault];
    
    //更改title文字可以设置self.navigationItem.titleView也可以设置setTitleTextAttributes
    NSMutableDictionary *dict = [NSMutableDictionary dictionary];
    dict[NSFontAttributeName] = [UIFont systemFontOfSize:15];
    dict[NSForegroundColorAttributeName] = [UIColor blackColor];
    [navigationBar setTitleTextAttributes:dict];//UI_APPEARANCE_SELECTOR设置一次就行了

    //设置barbuttonitem属性
    UIBarButtonItem *item = [UIBarButtonItem appearance];
    NSMutableDictionary *itemAttrs= [NSMutableDictionary dictionary];
    itemAttrs[NSForegroundColorAttributeName] = [UIColor blackColor];
    itemAttrs[NSFontAttributeName] = [UIFont systemFontOfSize:14];
    [item setTitleTextAttributes:itemAttrs forState:UIControlStateNormal];
    
    // UIControlStateDisabled
    NSMutableDictionary *itemDisabledAttrs = [NSMutableDictionary dictionary];
    itemDisabledAttrs[NSForegroundColorAttributeName] = [UIColor lightGrayColor];
    [item setTitleTextAttributes:itemDisabledAttrs forState:UIControlStateDisabled];
    
}
```
###设置导航栏title颜色以及字体

```objc
UINavigationBar *bar = self.navigationController.navigationBar；
[bar setTitleTextAttributes:@{NSForegroundColorAttributeName :[UIColor whiteColor]}];
```

###导航栏透明
设置UINavigationBar全透明, 此处随便设置一张图片即可，重要的是BarMetrics属性决定了bar的样式

```objc
UINavigationBar *bar = self.navigationController.navigationBar；
[bar setBackgroundImage:[UIImage imageNamed:@"bg.png"] forBarMetrics:UIBarMetricsCompact];
```

###push到下一个控制器的时候隐藏tabbar
这时候我们可以重写`- (void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated`这个方法，当然我们也可以在这个方法里面设置返回按钮

```objc
- (void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated
{
    if (self.viewControllers.count > 0) {
        UIButton *btn = [UIButton buttonWithType:UIButtonTypeCustom];
        [btn setImage:[UIImage imageNamed:@"navigationButtonReturn"] forState:UIControlStateNormal];
        [btn setImage:[UIImage imageNamed:@"navigationButtonReturnClick"] forState:UIControlStateHighlighted];
        [btn setTitle:@"返回" forState:UIControlStateNormal];
        [btn setTitle:@"返回" forState:UIControlStateHighlighted];
        [btn setTitleColor:[UIColor blackColor] forState:UIControlStateNormal];
        [btn setTitleColor:[UIColor redColor] forState:UIControlStateHighlighted];
        btn.size = CGSizeMake(70, 30);
        btn.contentHorizontalAlignment = UIControlContentHorizontalAlignmentLeft;
        btn.contentEdgeInsets = UIEdgeInsetsMake(0, -10, 0, 0);
        [btn addTarget:self action:@selector(back) forControlEvents:UIControlEventTouchUpInside];
        viewController.navigationItem.leftBarButtonItem = [[UIBarButtonItem alloc]initWithCustomView:btn];
        viewController.hidesBottomBarWhenPushed = YES;
    }
    [super pushViewController:viewController animated:animated];
}
```
###解决重写返回按钮的时候可能会导致系统自带的左侧滑动返回实现
可以使用孙源大神写的那个`FDFullscreenPopGesture`，也可以设置` self.interactivePopGestureRecognizer.delegate = nil;`，在导航控制器中设置即可


###自定义UIBarButtonItem
我们可以给UIBarButtonItem添加一个分类，这样用起来也比较方便

```objc
+ (instancetype)itemWithimage:(NSString *)image heighlitImage:(NSString *)heighlitImage target:(id)target ation:(SEL)action
{
    UIButton *btn = [UIButton buttonWithType:UIButtonTypeCustom];
    [btn setBackgroundImage:[UIImage imageNamed:image] forState:UIControlStateNormal];
    [btn setBackgroundImage:[UIImage imageNamed:heighlitImage] forState:UIControlStateHighlighted];
    [btn addTarget:target action:action forControlEvents:UIControlEventTouchUpInside];
    btn.size = btn.currentBackgroundImage.size;
    return [[self alloc]initWithCustomView:btn];
}
```
###状态栏的设置
//iOS7以后状态栏由controller进行控制，如果不需要控制器控制的话需要在info.plist设置View controller-based status bar appearancer然后通过[UIApplication sharedApplication].statusBarStyle = UIStatusBarStyleDefault;设置
如果不想设置plist，我们可以重写`- (UIStatusBarStyle)preferredStatusBarStyle`

```objc
- (UIStatusBarStyle)preferredStatusBarStyle
{
    return UIStatusBarStyleLightContent;
}
```

###修改UITextField的placeholder颜色
 1. 修改Placeholder的方法有好几种，可以设置textField的attributedPlaceholder属性，这是一个富文本属性，
 2. 也可以自定义一个TextField，重写- (void)drawPlaceholderInRect:(CGRect)rect这个方法[self.placeholder drawInRect:rect withAttributes:@{NSForegroundColorAttributeName:[UIColor whiteColor]}];
 3. 就是通过Runtime运行时进行修改
 
- 使用属性

```objc
@property(nonatomic,copy)   NSAttributedString     *attributedPlaceholder;

// 文字属性
NSMutableDictionary *attrs = [NSMutableDictionary dictionary];
attrs[NSForegroundColorAttributeName] = [UIColor grayColor];

// NSAttributedString : 带有属性的文字(富文本技术)
NSAttributedString *placeholder = [[NSAttributedString alloc] initWithString:@"手机号" attributes:attrs];
self.phoneField.attributedPlaceholder = placeholder;

NSMutableAttributedString *placehoder = [[NSMutableAttributedString alloc] initWithString:@"手机号"];
[placehoder setAttributes:@{NSForegroundColorAttributeName : [UIColor whiteColor]} range:NSMakeRange(0, 1)];
[placehoder setAttributes:@{
                            NSForegroundColorAttributeName : [UIColor yellowColor],
                            NSFontAttributeName : [UIFont systemFontOfSize:30]
                            } range:NSMakeRange(1, 1)];
[placehoder setAttributes:@{NSForegroundColorAttributeName : [UIColor redColor]} range:NSMakeRange(2, 1)];
self.phoneField.attributedPlaceholder = placehoder;
```

- 重写方法

```objc
- (void)drawPlaceholderInRect:(CGRect)rect
{
    [self.placeholder drawInRect:CGRectMake(0, 10, rect.size.width, 25) withAttributes:@{
                                                       NSForegroundColorAttributeName : [UIColor grayColor],
                                                       NSFontAttributeName : self.font}];
}
```

- 使用KVC

```objc
[self setValue:[UIColor grayColor] forKeyPath:@"_placeholderLabel.textColor"];
```


###图片在上面文字在下面的UIButton
我们可以自定义一个UIButton
我们在`awakeFromNib`以及`initWithFrame`设置文字的居中，在`layoutSubviews`设置UIButton的图片位置大小以及lable的位置大小

```objc
- (void)awakeFromNib
{
    [self setup];
}

- (instancetype)initWithFrame:(CGRect)frame
{
    if (self = [super initWithFrame:frame]) {
        [self setup];
    }
    return self;
}

- (void)setup
{
    self.titleLabel.textAlignment = NSTextAlignmentCenter;
}

- (void)layoutSubviews
{
    [super layoutSubviews];
    
    self.imageView.x = 0;
    self.imageView.y = 0;
    self.imageView.width = self.imageView.width;
    self.imageView.height = self.imageView.width;
    
    self.titleLabel.x = 0;
    self.titleLabel.y = self.imageView.height + 10;
    self.titleLabel.width = self.imageView.width;
    self.titleLabel.height = self.titleLabel.height;
    
}
```
###SDWebImage获取缓存图片大小
SDWebImage已经提供了方法获取图片占据内存大小了，我们调用即可

```objc
[[SDImageCache sharedImageCache]getSize]
```

- 清空缓存

```objc
[[SDImageCache sharedImageCache]cleanDisk];
```


