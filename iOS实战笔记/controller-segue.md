##控制器跳转
控制器的跳转，控制器之间数据的顺传
- 手动执行segue那根线,segue必须由来源控制器调用
- 手动型segue
  - 控制器拖向控制器
  - 可以进行判断，符合条件才会跳转(比如登录，密码正确才会跳转到下一个控制器)
  - 手动型segue我们需要给它的identifier命名
     - 一般都是前一个控制器和下一个控制器名称简写,比如”login2detail”
  - 跳转的时候执行performSegueWithIdentifier:@"login2detail" sender:nil方法完成跳转
  - 跳转过程(执行跳转命令后，需要完成以下过程后才可以跳转)
     -  根据identifier去storyboard中找到对应的线，创建一个UIStoryboardSegue对象，设置当前控制器为来源控制器，创建并且设置需要跳转到的控制器为目标控制器
     - 准备好segue线之后,跳转之前会调用prepareForSegue方法做准备工作
     - 执行prepareForSegue方法，做跳转前的准备工作，因为在执行这个方法的时候segue对象已经创建完毕了，所以可以拿到目标控制器，给目标控制器的一些属性设置
     - 最后执行performSegue方法完成跳转
        - 该方法中的sender参数可以用来传值，它会把值传递给prepareForSegue方法中的sender，然后拿到sender给目标控制器一些属性设值
- 自动型segue
  - 控件拖向控制器
  - 无法进行判断，直接进行跳转

##父子控制器
#### 控制器父子关系的建立原则
- 如果2个控制器的view是父子关系(不管是直接还是间接的父子关系)，那么这2个控制器也应该为父子关系

```objc
[a.view addSubview:b.view];
[a addChildViewController:b];
// 或者
[a.view addSubview:otherView];
[otherView addSubbiew.b.view];
[a addChildViewController:b];
```

#### 获得所有的子控制器
```objc
@property(nonatomic,readonly) NSArray *childViewControllers;
```

#### 添加一个子控制器
```objc
//XMGOneViewController成为了self的子控制器
//self成为了XMGOneViewController的父控制器
[self addChildViewController:[[XMGOneViewController alloc] init]];
// 通过addChildViewController添加的控制器都会存在于childViewControllers数组中
```

#### 获得父控制器
```objc
@property(nonatomic,readonly) UIViewController *parentViewController;
```

#### 将一个控制器从它的父控制器中移除
```objc
// 控制器a从它的父控制器中移除
[a removeFromParentViewController];
```
####获得一个控件或者控制器在其父控件中的索引位置
```objc
 // 获得控制器的位置（索引）
    NSUInteger index = [button.superview.subviews indexOfObject:button];

// 当前控制器的索引
    NSUInteger oldIndex = [self.childViewControllers indexOfObject:self.showingVc];
```

##Modal
- CGRect使用技巧<br>
  //在self.containerView.bounds这个范围内左右各切10，上下各切20的间距，self.containerView就是容纳子控件的view
 - CGRectInset(self.containerView.bounds,10,20)


 ```objc
 1、创建需要modal出来的控制器
 //一般都是点击一个按钮后modal出来，所以在按钮点击监听方法里面创建并设置也是以及代理
 //设置展示样式是自定义，以及它的代理
  //弹出菜单(控制器)
    popVC.transitioningDelegate = self;
    popVC.modalPresentationStyle = UIModalPresentationCustom;
   [self presentViewController:popVc animated:YES completion:nil];
 2、代理的.m文件实现以下方法
 //代理需要遵守UIViewControllerTransitioningDelegate和UIViewControllerAnimatedTransitioning
 //该代理方法用于返回负责转场的控制器对象,负责控制展示
 //一般情况下都是自定义一个继承自UIPresentationController的负责转场的控制器对象，这里就是XMGPresentationController
  - (nullable UIPresentationController *)presentationControllerForPresentedViewController:(UIViewController *)presented presentingViewController:(UIViewController *)presenting sourceViewController:(UIViewController *)source {
    return [[XMGPresentationController alloc] initWithPresentedViewController:presented presentingViewController:presenting];
     }
 3、//下面2个方法有区别，就是WillBegin先调用，在里面添加一个蒙版，在里面直接设置presentedView.frame不准确，把设置presentedView尺寸的代码放在WillLayoutSubviews是准确的
 //如果代码都放在WillBegin里面，位置不准确，代码都放在WillLayoutSubviews里面位置准确了，但是城市下拉没有反应
 //自我感觉是需要一个蒙版挡着，modal出来的页面就自动在最前面，蒙版消失之前，它就可以接收事件，就可以被滑动
 //当负责转场的控制器对象(XMGPresentationController)监听到你将要展示的时候通过以下方法把你加进去
 //在XMGPresentationController里面实现
/// 给需要modal的小视图设置frame
////在这个里面添加那个titleview的弹框视图最合适
 /// 即将布局存放在容器视图上的子控件时调用
-(void)containerViewWillLayoutSubviews
{
/*
     通过当前对象的containerView属性可以获取容器视图
     通过当前对象的presentedView()方法, 可以获取被弹出的控制器的view
     */
//    self.presentedViewController;//modal弹出的Vc
//    self.presentingViewController;//modal出来的Vc后面的大Vc
    self.presentedView.frame = CGRectMake(30, 110, 200, 150);

}
 /// 自定义转场即将展示时调用，即将调用转场modal之前调用
- (void)presentationTransitionWillBegin {
    [super presentationTransitionWillBegin];

    // 1.添加蒙版
    self.coverButton.backgroundColor = [UIColor redColor];
    //核心内容
    self.coverButton.frame = self.containerView.bounds;
    [self.coverButton addTarget:self action:@selector(coverBtnClick:) forControlEvents:UIControlEventTouchUpInside];
    //核心内容
    [self.containerView addSubview:self.coverButton];
}
4、1、然后就会调用我们返回的这个动画对象的代理方法去完成动画效果
/// 该代理方法用于告诉系统谁来负责控制器如何弹出
//普通情况下写在当前控制器就可以，特殊情况下如果我们自定了一个遵守了<UIViewControllerTransitioningDelegate>协议的对象，那么在该对象里面实现下面这2个方法
- (nullable id <UIViewControllerAnimatedTransitioning>)animationControllerForPresentedController:(UIViewController *)presented presentingController:(UIViewController *)presenting sourceController:(UIViewController *)source {
    self.isPresented = YES;
    return self;
}
或者自定义的话
//展示的时候使用哪一个动画
- (id <UIViewControllerAnimatedTransitioning>)animationControllerForPresentedController:(UIViewController *)presented presentingController:(UIViewController *)presenting sourceController:(UIViewController *)source
{
    HMAnimatedTransitioning *anim = [[HMAnimatedTransitioning alloc] init];
    anim.presented = YES;
    return anim;//返回一个遵守了<UIViewControllerAnimatedTransitioning>协议的对象即可
}
4、1、/// 该代理方法用于告诉系统谁来负责控制器如何消失
- (nullable id <UIViewControllerAnimatedTransitioning>)animationControllerForDismissedController:(UIViewController *)dismissed {
    self.isPresented = NO;
    return  self;
}
或者自定义的话
//销毁的时候使用哪一个动画
- (id <UIViewControllerAnimatedTransitioning>)animationControllerForDismissedController:(UIViewController *)dismissed
{
    HMAnimatedTransitioning *anim = [[HMAnimatedTransitioning alloc] init];
    anim.presented = NO;
    return anim;
}

5、//以下方法也是普通情况下写在当前控制器下即可，但是如果自定义一个遵守了<UIViewControllerAnimatedTransitioning>协议对象的话，需要写在这个对象的类里面
#pragma mark - <UIViewControllerAnimatedTransitioning>
- (NSTimeInterval)transitionDuration:(nullable id <UIViewControllerContextTransitioning>)transitionContext{
    return  998;
}

//该方法负责控制动画的执行效果
- (void)animateTransition:(id <UIViewControllerContextTransitioning>)transitionContext
{
    if (self.presented) {
        UIView *toView = [transitionContext viewForKey:UITransitionContextToViewKey];
//        toView.layer.transform = CATransform3DMakeRotation(M_PI_2, 1, 1, 0);
//        toView.y = -toView.height;
        toView.x = toView.width;
        [UIView animateWithDuration:duration animations:^{
//            toView.y = 0;
            toView.x = 0;
//            toView.layer.transform = CATransform3DIdentity;
        } completion:^(BOOL finished) {
            [transitionContext completeTransition:YES];
        }];
    } else {
        [UIView animateWithDuration:duration animations:^{
            UIView *fromView = [transitionContext viewForKey:UITransitionContextFromViewKey];
//            fromView.y = -fromView.height;
            fromView.x = -fromView.width;
//            fromView.layer.transform = CATransform3DMakeRotation(M_PI_2, 1, 1, 0);
        } completion:^(BOOL finished) {
            [transitionContext completeTransition:YES];
        }];
    }
}

//目前还没有发现这个方法实现与否有什么用，可能新的api已经不再需要这个方法
//销毁的时候调用这个方法
- (void)dismissalTransitionDidEnd:(BOOL)completed
{
//    NSLog(@"dismissalTransitionDidEnd");
    [self.presentedView removeFromSuperview];
}

 ```

