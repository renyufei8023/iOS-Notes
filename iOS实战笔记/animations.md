#Animations
```/******************** ** 基本动画**  ********************/```
- **基本动画一般配合控件的hidden、alpha、以及transform属性使用**

   ##### - 头尾式
   ##### - dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(),^{   });

   ##### - UIView animateWithDuration：animations：

   ##### - UIView animateWithDuration: animations: completion:

```objc
//嵌套配合使用比较好
[UIView  animateWithDuration:1.0 animations:^{
        self.mylabel.alpha=1.0;
    } completion:^(BOOL finished) {
        [UIView animateWithDuration:1.0 delay:1.5 options:UIViewAnimationOptionTransitionNone animations:^{
            self.mylabel.alpha=0.0;
        } completion:^(BOOL finished) {
            NSLog(@"----%@",self.mylabel.text);
        }];
    }];
```


```   /********************  核心动画  ********************/```
- position，决定着layer在父控件中的位置，以父控件的左上角为原点
- anchorPoint 锚点(默认(0.5,0.5),范围是(0-1,0-1))，指明了layer自身上的哪一个点和position这个点是重合的
- 每一个UIView内部都会默认关联一个CALayer,我们称其为根层,所有的非根层也就是手动创建的CALyer对象，都存在隐式动画
- 隐式动画：当对非根层layer部分属性进行修改时，默认会产生一些动画效果，被称为隐式动画
- 核心动画的执行过程都是在后头操作的，不会阻塞主线程，是直接作用在layer上的，并不是uiview
---
- CABasicAnimation
 - 设置以下2个属性，可保证动画执行后不回退到原位置，但是需要在图层添加动画之前设置这2个属性
   - basic.removedOnCompletion=NO;
   - basic.fillMode=kCAFillModeForwards;

---
- 核心动画和UIView的区别
 - 核心动画的一切都是假象，并不会真正的改变图层的属性值 ，UIView动画需要真实改变属性值才可以产生动画效果
 - 做动画如果不需要与用户交互的话，那么可以使用核实动画 
