#屏幕适配
- Portrait 竖屏
- Landscape 横屏
##`autoresizing`


    专门处理父子控件的关系，子控件会随着父控件的行为发生相应的变化；
    局限性：只可以解决父子控件的关系，无法解决兄弟控件的关系
    redView.autoresizingMask=[];
    UIViewAutoresizingNone                 = 0,
    UIViewAutoresizingFlexibleLeftMargin   = 1 << 0, 距离父控件左边的间距是伸缩的（左边不固定的，右边固定）
    UIViewAutoresizingFlexibleRightMargin  = 1 << 2, 距离父控件右边的间距是伸缩的（右边不固定的，左边固定）
    UIViewAutoresizingFlexibleTopMargin    = 1 << 3, 距离父控件顶部的间距是伸缩的（顶部不固定的，底部固定）
    UIViewAutoresizingFlexibleBottomMargin = 1 << 5, 距离父控件底部的间距是伸缩的（底部不固定的，顶部固定）
    UIViewAutoresizingFlexibleWidth        = 1 << 1, 宽度跟随父控件的宽度进行自动伸缩
    UIViewAutoresizingFlexibleHeight       = 1 << 4, 高度跟随父控件的高度进行自动伸缩

## `AutoLayout`

     1、从iOS7，也就是Xcode5开始，使用AutoLayout可以完美解决屏幕适配问题，2个核心概念是参照和约束。
     2、xib中最好把控件重新命名一下，方便参照约束
     3、报错一般2个原因，要么约束冲突要么就是约束不完整
     4、UILabel是特殊的控件，只需要设置x、y、w即可，高度系统会自动根据内容判断并包裹，上下不会留宽度，不使用自动布局的label上下会由系统自动空出间距，
     要想label的宽度在文字很多的时候按照我们规定的宽度自动换行，很少的时候宽度就是文字本身的宽度，需要设置宽度约束为小于等于

##`Masonry`

     1、这个方法只会添加新的约束，多次添加会覆盖旧值
       [blueView mas_makeConstraints:
     2、这个方法会将以前的所有约束删掉，添加新的约束
       [blueView mas_remakeConstraints:
     3、这个方法将会覆盖以前的某些特定的约束
       [blueView mas_updateConstraints:

#####`   注意点`


     第一：
     mas_equalTo：这个方法会对参数进行包装
     equalTo：这个方法不会对参数进行包装
     mas_equalTo的功能强于 > equalTo

     第二：
     #define MAS_SHORTHAND
     //使用这个宏之后在equalTo（）里面针对对象不需要再加mas_,外面的mas_equalTo也不需要加mas_
     //make.left.equalTo(self.view.left).offset(margin)

     #define MAS_SHORTHAND_GLOBALS
     // 使用这个宏之后在equalTo（）里面针对基本数据类型不需要再加mas_
     //make.width.equalTo(100);
     注意：使用这2个宏要放在#import"masonry"之前

     第三：
     约束的类型：
     1.尺寸：width\height\size
     2.边界：left\leading\right\trailing\top\bottom
     3.中心点：center\centerX\centerY
     4.边界：edges
     第四：特别约束书写格式
     自定义view的子控件布局，写在initWithFrame或者initWithStyle方法中即可
     make.size.equalTo(CGSizeMake(10,10))

###UIButton布局注意点
- Button的高度布局
 - 系统会自动根据button里面  titleLabel  的文字尺寸计算button的高度，建议一般给button设置一个大于某个值以及小于某个值的宽度；
 - 它的高度一般就是自己titlelabel的高度+一个固定值;<br>
 - 有了固定值，还需要给button的titleEdgeInsets设置内边距，而且内边距需要和固定值对称
   - 对称的意思就是，比如内边距是40，你想让button看起来匀称，左右设置10（可以自己定）的间距，上下就需要设置各20（需要根据固定值计算，一般就是固定值的一半），（20，10，20，10）
- 最好在button的titleLabel获得数据或者按钮的高度发生改变的时候进行一次强制刷新layoutIfNeeded

###UITableview布局注意点
- 在弹出键盘的时候让Tableview自己高度不变，但是位置跟着键盘上移
 - 底部粘着键盘上的tabbar，左右固定，高度不设置固定值，但是需要设置高度等于控制器高度 - 44
 - 或者键盘弹出时让整个控制器view高度向上挪(本来顶部左上角是(0,0),向上挪就是y值为负)，消失时向下挪(y值为正)
