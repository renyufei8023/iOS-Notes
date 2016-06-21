## NSNotification/Delegate/Block
### 通知 -- NSNotification
- 任何对象之间都可以发送通知，可以跨控制器跨界面传输数据

```objc
1、//创建通知，在需要发布通知的类内部创建
//object和userInfo它们都是包含在note这个对象里面的，所以都可以传递出去被外界拿到，只不过需要根据实际情况
NSNotification *note=[NSNotification notificationWithName:通知名称 object:发布通知的对象 userInfo:(字典)可以传一些信息,比如模型];
//发送通知
[[NSNotificationCenter defaultCenter] postNotification:note];

2、//监听通知，在需要监听的控制器监听
//一旦object对象发布了名称是name的通知，那么我就会监听到object对象发出的name通知，就会自动调用self当前控制器的reciveNotification:方法
 [NSNotificationCenter defaultCenter] addObserver:通知监听者，一般都是self selector:@selector(keyBoardFrameChange:) name:通知的名称 object:发布通知的对象（如果写nil，代表谁发布的通知，只要名称是name，那么我都可以监听,一般都写nil）

3、//并且会自动传入通知发布的时候创建的NSNotification对象note，note里面就有通知的发布者以及自带的那个userInfo字典，我们一般都是从这个字典获取数据，进行数据传递
-(void)keyBoardFrameChange:(NSNotification *)note
```
```objc
4、//移除通知
-(void)dealloc
{
    //控制器死的时候移除所有通知
    [[NSNotificationCenter defaultCenter] removeObserver:self];
    //A移除B的名称为notificationName的通知
    [[NSNotificationCenter defaultCenter] removeObserver:A name:notificationName object:B];
}
5、//以键盘弹出和收回为例
   //收到通知需要调用的方法
-(void)keyBoardFrameChange:(NSNotification *)note{
    //键盘弹出所需的时间
    NSTimeInterval time=[note.userInfo[UIKeyboardAnimationDurationUserInfoKey] doubleValue];
   //键盘的高度
    NSInteger keyBHH=[note.userInfo[UIKeyboardFrameBeginUserInfoKey] CGRectValue].size.height;

    [UIView animateWithDuration:time animations:^{
        self.view.transform=CGAffineTransformMakeTranslation( 0, -keyBHH);
    }];
}
//拖拽Tableview的时候退出键盘，并把view的transform的平移清空
-(void)scrollViewWillBeginDragging:(UIScrollView *)scrollView
{
    [UIView animateWithDuration:0.25 animations:^{
        [self.view endEditing:YES ];
        self.view.transform=CGAffineTransformIdentity;
    }];
}

```
- 常用通知
 - UITextField通知
   - dd

 - 键盘通知
   - UIKeyboardDidShowNotification
   - UIKeyboardDidHideNotification<br>
   //可直接监听键盘的弹出和消失
   - UIKeyboardDidChangeFrameNotification
  ![](images/keyboard.png)
##代理
- 封装控件最好用代理，因为以后可能会给控件扩充其他方法
