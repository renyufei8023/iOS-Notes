## UIGestureRecognizer
```objc
//UIGestureRecognizerDelegate的代理方法，默认是NO，YES代表同时支持多个手持
- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldRecognizeSimultaneouslyWithGestureRecognizer:(UIGestureRecognizer *)otherGestureRecognizer
{
    return YES;
}

//可以控制一个view的哪一部分可以接收点击事件，哪一部分不支持点击事件，默认是YES，整个view 都支持
- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldReceiveTouch:(UITouch *)touch;
```
- ####手势使用注意
 - UIPanGestureRecognizer(拖拽)、UIPinchGestureRecognizer(缩放)、UIRotationGestureRecognizer(旋转)、UITapGestureRecognizer(点击)、UISwipeGestureRecognizer(轻扫)、UILongPressGestureRecognizer(长按)等手势都是UIGestureRecognizer的子类
 - 创建手势，给控件添加手势
 - 手势有代理协议UIGestureRecognizerDelegate，如果需要同时支持多个手势，就需要分别让self成为每一个手势的代理，然后同时实现支持多手势代理方法
 - 每一个手势识别器绑定的方法都可以传递一个参数，就是这个手势识别器自己，根据不同的手势，参数里面分别有选择角度，缩放比例等属性
 - 注意每次改变对应位置或者角度等之后需要给对应的位置或者角度属性值清空
 - 如果需要添加轻扫手势，默认是从左向右这个方向，如果需要同时添加多个方向的轻扫手势，需要同时创建多个UISwipeGestureRecognizer

```objc
- (void)pan:(UIPanGestureRecognizer *)pan{
//可以获取当前手指在myImageView的触摸点
// [ pan locationInView:self.myImageView];
//获取拖拽myImageView的前后位置差值,即手势移动的位置，也就是view需要移动的位置
    CGPoint point= [pan translationInView:self.myImageView];
    self.myImageView.transform=CGAffineTransformTranslate(self.myImageView.transform,point.x,point.y);
    [pan setTranslation:CGPointZero inView:self.myImageView];
}
```




