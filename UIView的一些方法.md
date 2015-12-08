###layoutSubviews在以下情况下会被调用：
1. init初始化不会触发layoutsubViews但是是用initWithFrame 进行初始化时，当rect的值不为CGRectZero时,也会触发
2. addSubview也会触发layoutSubviews
3. 设置view的frame灰触发layoutSubViews，前提是frame的值是设置前后发生了变化
4. 滚动UIScrollView会触发layoutSubviews
5. 旋转屏幕会触发父UIView上的layoutSubviews事件
6. 改变一个UIView的大小的时候也会触发父UIView上的layoutSubviews


###刷新子对象布局
* -layoutSubviews方法：这个方法，默认没有做任何事情，需要子类进行重写
* -setNeedsLayout方法：标记为需要重新布局，一部调用layoutIfNeeded刷新布局，不立即刷新，但layoutSubviews一定会被调用（配合layoutIfNeeded使用）
* -layoutIfNeeded方法：如果有需要刷新的标记，立即调用layoutSubviews进行布局（如果没有标记，不会调用layoutSubviews）
* 如果要立即刷新，要先调用[view setNeedsLayout]，把标记设为需要布局，然后马上调用[view layoutIfNeeded]，实现布局
* setNeedsDisplay调用drawRect方法来实现view的绘制，而setNeedsLayout则调用layoutSubView来实现view中subView的重新布局


###重绘
* -drawRect:(CGRect)rect方法：重写此方法，执行重绘任务
* -setNeedsDisplay方法：标记为需要重绘，异步调用drawRect
* -setNeedsDisplayInRect:(CGRect)invalidRect方法：标记为需要局部重绘
* sizeToFit会自动调用sizeThatFits方法
* sizeToFit不应该在子类中被重写，应该重写sizeThatFits
* sizeThatFits传入的参数是receiver当前的size，返回一个适合的size
* sizeToFit可以被手动直接调用
* sizeToFit和sizeThatFits方法都没有递归，对subviews也不负责，只负责自己



###layoutSubviews对subviews重新布局
* layoutSubviews方法调用先于drawRect
* setNeedsLayout在receiver标上一个需要被重新布局的标记，在系统runloop的下一个周期自动调用layoutSubviews
* layoutIfNeeded方法如其名，UIKit会判断该receiver是否需要layout.根据Apple官方文档,layoutIfNeeded方法应该是这样的，layoutIfNeeded遍历的不是superview链，应该是subviews链
* drawRect是对receiver的重绘，能获得context
* setNeedDisplay在receiver标上一个需要被重新绘图的标记，在下一个draw周期自动重绘，iphone device的刷新频率是60hz，也就是1/60秒后重绘 
