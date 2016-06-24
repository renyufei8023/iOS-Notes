<table class="table table-bordered table-hover">
    <thead>
        <tr>
            <th>format</th><th>含义</th>
        </tr>
    </thead>
    <tbody>
        <tr><td>%@    </td><td>对象</td></tr>
        <tr><td>%p    </td><td><b>指针</b></td></tr>
        <tr><td>%d, %i</td><td>整型</td></tr>
        <tr><td>%u    </td><td>无符整型</td></tr>
        <tr><td>%x, %X</td><td>二进制整型</td></tr>
        <tr><td>%o    </td><td>八进制整型</td></tr>
        <tr><td>%lld  </td><td>64位长整型（long long）</td></tr>
        <tr><td>%f    </td><td>浮点型</td></tr>
        <tr><td>%lf    </td><td>64位双字浮点型</td></tr>
        <tr><td>%e    </td><td>浮点/双字 （科学计数法）</td></tr>
        <tr><td>%s    </td><td>C字符串</td></tr>
        <tr><td>%c    </td><td>字符</td></tr>
        <tr><td>%C    </td><td>unichar</td></tr>
    </tbody>
</table>


##NSLog日志在控制台着色
使用XcodeColors插件并且设置宏定义，如下

```objc
#define XCODE_COLORS_ESCAPE  @"\033["

#define XCODE_COLORS_RESET_FG  XCODE_COLORS_ESCAPE @"fg;" // Clear any foreground color
#define XCODE_COLORS_RESET_BG  XCODE_COLORS_ESCAPE @"bg;" // Clear any background color
#define XCODE_COLORS_RESET     XCODE_COLORS_ESCAPE @";"   // Clear any foreground or background color

// log蓝色内容
#define LogBlue(frmt, ...) NSLog((XCODE_COLORS_ESCAPE @"fg0,0,255;" frmt XCODE_COLORS_RESET), ##__VA_ARGS__)
// log红色内容
#define LogRed(frmt, ...) NSLog((XCODE_COLORS_ESCAPE @"fg255,0,0;" frmt XCODE_COLORS_RESET), ##__VA_ARGS__)
// log绿色内容
#define LogGreen(frmt, ...) NSLog((XCODE_COLORS_ESCAPE @"fg0,255,0;" frmt XCODE_COLORS_RESET), ##__VA_ARGS__)
// log制定色内容
// color格式，注意分号
//    fgrgb;，前景文字色，例如@"bg0,255,0;"
//    bgrgb;，背景色，例如@"bg255,0,0;"
// 输出绝对是“红配绿赛狗屁”
#define NXLogColored(color, frmt, ...) NSLog((XCODE_COLORS_ESCAPE color frmt XCODE_COLORS_RESET), ##__VA_ARGS__)
```

###利用私有API输出对象的更多信息
在一些时候，我们需要打印更多关于对象的信息，通常我们会自己重写-description或者-debugDescription方法来解决。自从知道了iOS7之后NSObject基类有了几个私有方法之后，一切都改变了。

```objc
@interface NSObject (Private)
// 返回格式化的字符串，内容为该对象的成员变量以及类型和值（并且明确标出继承自父类的成员变量）
-(id)_ivarDescription;
// 返回格式化的字符串，内容为该对象的成员方法和属性列表，包括getter和setter，省略掉父类中继承来的方法等信息
-(id)_shortMethodDescription;
// 返回格式化的字符串，内容为该对象的成员方法和属性列表，包括getter和setter，包含完整父类中继承来的方法等信息
-(id)_methodDescription;
@end
```

###调试工具栏
![](http://pimacun.github.io/images/blog/debugging_in_ios_4.png)
1. 断点调试开关，添加断点时自动打开（图为打开），如果关闭XCode将disable所有断点；
2. 运行时暂停，进入调试状态；
3. 单步执行，点击一次执行一行，其他组合键ctrl+click、ctrl+shift+click，分别对应指令和线程。
4. 进入执行，如果执行到方法调用，点击即可进入该方法内部，配合5进行调试，其他组合键ctrl+click、ctrl+shift+click，同样分别对应指令和线程。
5. 当执行到方法内部，点击直接完成当前方法执行。


在XCode左侧导航里切换到断点导航，⌘+7

