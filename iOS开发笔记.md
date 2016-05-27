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
###带有placeholder的UITextView
一个好的自定义控件最好有提供一些其他属性供其他人用，这里提供了一个设置placeholder颜色以及字体的属性，使用懒加载先设置一下placeholder的基本属性以及x，y以及设置默认的颜色，然后在initWithFrame方法里面监听`UITextViewTextDidChangeNotification`这个通知，通过UITextView是否有文字输入从而判断placeholder是否隐藏，重写setfont，settext以及setAttributedText方法，代码如下

```objc
- (UILabel *)placeholerLabel
{
    if (!_placeholerLabel) {
        _placeholerLabel = [[UILabel alloc]init];
        [self addSubview:_placeholerLabel];
        _placeholerLabel.numberOfLines = 0;
        _placeholerLabel.x = 5;
        _placeholerLabel.y = 5;
        _placeholerLabel.textColor = [UIColor lightGrayColor];
    }
    return _placeholerLabel;
}

- (instancetype)initWithFrame:(CGRect)frame
{
    if (self = [super initWithFrame:frame]) {
        self.alwaysBounceVertical = YES;
        self.font = [UIFont systemFontOfSize:15];
        
        [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(textChange) name:UITextViewTextDidChangeNotification object:nil];
    }
    return self;
}

- (void)textChange
{
    self.placeholerLabel.hidden = self.hasText;
}

- (void)setPlaceholer:(NSString *)placeholer
{
    _placeholer = placeholer;
    self.placeholerLabel.text = placeholer;
    [self setNeedsLayout];
}

- (void)setPlaceholerColor:(UIColor *)placeholerColor
{
    _placeholerColor = placeholerColor;
    self.placeholerLabel.textColor = placeholerColor;
    
}

- (void)setFont:(UIFont *)font
{
    [super setFont:font];
    self.placeholerLabel.font = font;
    [self setNeedsLayout];
}

- (void)setText:(NSString *)text
{
    [super setText:text];
    [self textChange];
}

- (void)setAttributedText:(NSAttributedString *)attributedText
{
    [super setAttributedText:attributedText];
    [self textChange];
}

- (void)layoutSubviews
{
    [super layoutSubviews];
    self.placeholerLabel.width = SCREENWIDTH - 2 * self.placeholerLabel.x;
    [self.placeholerLabel sizeToFit];
}
/**
 * 更新占位文字的尺寸
 */
- (void)updatePlaceholderLabelSize
{
    CGSize maxSize = CGSizeMake(SCREENWIDTH - 2 * self.placeholerLabel.x, MAXFLOAT);
    self.placeholerLabel.size = [self.placeholer boundingRectWithSize:maxSize options:NSStringDrawingUsesLineFragmentOrigin attributes:@{NSFontAttributeName : self.font} context:nil].size;
//    self.placeholerLabel.backgroundColor = [UIColor redColor];
}

- (void)dealloc
{
    [[NSNotificationCenter defaultCenter]removeObserver:self];
}
```
另一种是使用drawRect方法实现的

```objc
- (instancetype)initWithFrame:(CGRect)frame
{
    if (self = [super initWithFrame:frame]) {
        self.alwaysBounceVertical = YES;
        self.font = [UIFont systemFontOfSize:15];
        self.placeholerColor = [UIColor lightGrayColor];
        [[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(textChange) name:UITextViewTextDidChangeNotification object:nil];
    }
    return self;
}

- (void)textChange
{
    [self setNeedsDisplay];
}

- (void)drawRect:(CGRect)rect {
    
    //可以使用这种方式判断
//    if (self.text.length || self.attributedText.length) return;
    //或者是这种
    if (self.hasText) return;
    rect.origin.x = 5;
    rect.origin.y = 5;
    rect.size.width -= 2 * rect.origin.x;
    NSMutableDictionary *att = [NSMutableDictionary dictionary];
    att[NSFontAttributeName] = self.font;
    att[NSForegroundColorAttributeName] = self.placeholerColor;
    [self.placeholer drawInRect:rect withAttributes:att];
}

- (void)setPlaceholer:(NSString *)placeholer
{
    _placeholer = placeholer;
    [self setNeedsDisplay];
}

- (void)setPlaceholerColor:(UIColor *)placeholerColor
{
    _placeholerColor = placeholerColor;
    [self setNeedsDisplay];
}

- (void)setFont:(UIFont *)font
{
    [super setFont:font];
    [self setNeedsDisplay];
}

- (void)setText:(NSString *)text
{
    [super setText:text];
    [self setNeedsDisplay];
}

- (void)setAttributedText:(NSAttributedString *)attributedText
{
    [super setAttributedText:attributedText];
    [self setNeedsDisplay];
}
```

####当点击一个按钮就发送一次请求的时候，如果不想连着点两下发两次网络请求，这时候我们可以把请求参数弄一个property，然后比较两次是否相同，相同就不发请求

####当网络比较慢的时候，我们从当前页面返回到了上一个页面，这时候可能后来返回数据的时候造成崩溃，我们可以重写dealloc方法，在dealloc方法中把这次请求给取消掉

###cell选中颜色

```objc
//highlightedTextColor可以设置选中时候字体的颜色
self.textLabel.highlightedTextColor = RGBCOLOR(215, 27, 26);
```
###改变cell的大小
我们可以在layoutSubviews中设置

```objc
- (void)layoutSubviews
{
    [super layoutSubviews];
    
    //如果想改变系统中某一个控件的位置可以在layoutSubviews重新设置控件的位置
    self.textLabel.height = self.height - 2;
}
```
###cell选择一个标记视图
我们可以在- (void)setSelected:(BOOL)selected animated:(BOOL)animated 这个方法中设置
当选中cell的时候如果设置了selectionStyle为none的话会造成self.textLabel.highlightedTextColor这个颜色无法正常显示，解决这个问题的方法就是在- (void)setSelected:(BOOL)selected animated:(BOOL)animated这个方法中设置选中时字体颜色以及普通状态下的文字颜色,cell被选中时内部子控件不会进入高亮状态

```objc
- (void)setSelected:(BOOL)selected animated:(BOOL)animated {
    [super setSelected:selected animated:animated];

    /**
     *  当选中cell的时候如果设置了selectionStyle为none的话会造成self.textLabel.highlightedTextColor这个颜色无法正常显示，解决这个问题的方法就是在- (void)setSelected:(BOOL)selected animated:(BOOL)animated这个方法中设置选中时字体颜色以及普通状态下的文字颜色,cell被选中时内部子控件不会进入高亮状态
     *
     *
     */
    _selectView.hidden = !selected;
    self.textLabel.textColor = !selected ? RGBCOLOR(74, 74, 74) : RGBCOLOR(215, 27, 26);
}
```

###改变UITextField提示文字颜色

```objc
static NSString * const PlacerholderColorKeyPath = @"_placeholderLabel.textColor";

- (void)drawPlaceholderInRect:(CGRect)rect
{
    [super drawPlaceholderInRect:rect];
    //需要设置位置
    [self.placeholder drawInRect:CGRectMake(0, 5, 0, 0) withAttributes:@{NSForegroundColorAttributeName:[UIColor whiteColor]}];
}

- (void)awakeFromNib
{
    //设置光标颜色和文字颜色一样
    self.tintColor = self.textColor;
    
    [self resignFirstResponder];
}

- (BOOL)becomeFirstResponder
{
    [self setValue:[UIColor whiteColor] forKeyPath:PlacerholderColorKeyPath];
    return [super becomeFirstResponder];
}

- (BOOL)resignFirstResponder
{
    [self setValue:[UIColor grayColor] forKeyPath:PlacerholderColorKeyPath];
    return [super resignFirstResponder];
}
```
###判断一个控件是否在主窗口上面
首先把当前控件坐标转换成其父控件所在坐标系的rect，然后判断转换后的rect是否在主窗口的bounds之内，然后只有当前控件显示以及透明度大于0.01 以及当前控件所在的窗口为主窗口以及转换后的rect是否在主窗口的bounds之内，只有这些条件都成立的时候这个控件才在主窗口上面显示，代码如下
```objc
- (BOOL)isShowOnWindow
{
    //主窗口
    UIWindow *keyWindow = [UIApplication sharedApplication].keyWindow;
    
    //相对于父控件转换之后的rect
    CGRect newRect = [keyWindow convertRect:self.frame fromView:self.superview];
    //主窗口的bounds
    CGRect winBounds = keyWindow.bounds;
    //判断两个坐标系是否有交汇的地方，返回bool值
    BOOL isIntersects =  CGRectIntersectsRect(newRect, winBounds);
    if (self.hidden != YES && self.alpha >0.01 && self.window == keyWindow && isIntersects) {
        return YES;
    }else{
        return NO;
    }
}
```
###在XIB或者SB中设置控件圆角或者边框
我们可以写一个UIView的分类，然后在分类中加入IB_DESIGNABLE，以及property(borderWidth,borderColor,cornerRadius)这些property前面需要使用IBInspectable修饰才会有效果，然后重写set方法即可

```objc
IB_DESIGNABLE

@interface UIView (RYF)
@property (nonatomic, assign)CGFloat x;
@property (nonatomic, assign)CGFloat y;
@property (nonatomic, assign)CGFloat width;
@property (nonatomic, assign)CGFloat height;
@property (nonatomic, assign)CGFloat centerX;
@property (nonatomic, assign)CGFloat centerY;
@property (nonatomic, assign)CGSize size;
@property(nonatomic, assign) IBInspectable CGFloat borderWidth;
@property(nonatomic, assign) IBInspectable UIColor *borderColor;
@property(nonatomic, assign) IBInspectable CGFloat cornerRadius;



- (void)setBorderWidth:(CGFloat)borderWidth
{
    if (borderWidth < 0) {
        return;
    }
    self.layer.borderWidth = borderWidth;
}

- (void)setBorderColor:(UIColor *)borderColor
{
    self.layer.borderColor = borderColor.CGColor;
}

- (void)setCornerRadius:(CGFloat)cornerRadius
{
    self.layer.cornerRadius = cornerRadius;
    self.layer.masksToBounds = YES;
}
```

###判断当前view所属的控制器
UIView的分类里面写的一个对象方法
```objc
- (UIViewController *)parentController
{
    UIResponder *responder = [self nextResponder];
    while (responder) {
        if ([responder isKindOfClass:[UIViewController class]]) {
            return (UIViewController *)responder;
        }
        responder = [responder nextResponder];
    }
    return nil;
}
```

####当设置某一个控件为tableview的tableHeaderView时候，当滑动tableview的时候会一直调用找个控件的setframe方法

###连续点击tabbar刷新tableView或者返回至最上面
首先我们现在AppDelegate当中监听tabbar的点击，通过`- (void)tabBarController:(UITabBarController *)tabBarController didSelectViewController:(UIViewController *)viewController`,然后发出通知，在需要的地方监听，然后判断当前view是否在主窗口上面以及设置一个index记录上次selectedIndex，只有显示在主窗口以及不等于上次的selectedIndex的时候进入刷新

```objc
- (void)tabbarClick
{
    /**
     *  先判断是否显示在窗口上了，如果没有显示在窗口就不刷新，然后判断当前选中的和上次是否一致，如果不一致的话才会刷新
     */
    if ([self.view isShowOnWindow] && self.tabSelectIndex != self.tabBarController.selectedIndex) {
        [self.tableView.mj_header beginRefreshing];
    }
    self.tabSelectIndex =  self.tabBarController.selectedIndex;
    
}
```
###当UITableView是plain的样式时候，两个cell之间需要有一定的间隙，这时候我们可以重写cell的setFramefangfa

```objc
/**
 *  修改父类中的东西可以在这个方法中进行设置，这个方法会覆盖之前所有的frame
 *
 *  @param frame <#frame description#>
 */
- (void)setFrame:(CGRect)frame
{
    frame.origin.x = 10;
    frame.size.width -= 2 * frame.origin.x;
    frame.size.height -= 1;
    
    [super setFrame:frame];
}
```

####当一个控件从XIB创建的时候，如果显示出来的效果和你设置的不一样，建议你把autoresizingMask设置为UIViewAutoresizingNone


###当一个很长的图片在一个固定大小的区域内我们想显示图片的最上方内容
我们可以裁剪图片然后再显示到UIImageView上面

```objc
 //在这里设置长图片显示最上方的一部分
        UIGraphicsBeginImageContextWithOptions(jokesModel.picF.size, YES, 0.0);
        
        CGFloat width = jokesModel.picF.size.width;
        CGFloat height = width * image.size.height / image.size.width;
        
        [image drawInRect:CGRectMake(0, 0, width, height)];
        
        _imageView.image = UIGraphicsGetImageFromCurrentImageContext();
        
        UIGraphicsEndImageContext();

```
###UIImage裁剪

```objc
- (UIImage *)circleImage
{
    //NO代表透明
    UIGraphicsBeginImageContextWithOptions(self.size, NO, 0.0);
    
    CGContextRef ref = UIGraphicsGetCurrentContext();
    
    CGRect rect = CGRectMake(0, 0, self.size.width, self.size.height);
    CGContextAddEllipseInRect(ref, rect);
    
    CGContextClip(ref);
    //把圆画上去
    [self drawInRect:rect];
    
    UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
    return image;
}
```

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


