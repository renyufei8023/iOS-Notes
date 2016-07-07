##什么情况使用 weak 关键字，相比 assign 有什么不同？
1. weak在ARC环境中在可能造成循环引用的时候，通过weak关键词来解决这个问题，一段strong，一段weak，常见于delagete。
2. 自身已经对其强引用了一次，没必要再次进行强引用，比如从xib或者sb里面拖出来的控件，这里就可以使用weak不过也可以使用strong
3. weak表明该属性不是持有的关系，为这种属性设置新值时，设置方法既不保留新值，也不释放旧值。此特质同assign类似， 然而在属性所指的对象遭到摧毁时，属性值也会清空(nil out)。 而 assign 的“设置方法”只会执行针对“纯量类型” (scalar type，例如 CGFloat 或 NSlnteger 等)的简单赋值操作。
4. assign可以用于非OC对象，而weak只能应用于OC对象


##怎么样使用copy关键字
block使用copy其实是因为在MRC环境中，block是在栈区的，使用copy把它放到堆区，在ARC环境中，copy或者strong的作用都是一样的，写上的话可以提示我们编译器自动对block进行了copy操作，如果不写，那么该类的调用者有可能会忘记或者根本不知道“编译器会自动对 block 进行了 copy 操作”，他们有可能会在调用之前自行拷贝属性值。



copy 此特质所表达的所属关系与 strong 类似。然而设置方法并不保留新值，而是将其“拷贝” (copy)。 当属性类型为 NSString时，经常用此特质来保护其封装性，因为传递给设置方法的新值有可能指向一个 NSMutableString 类的实例。这个类是 NSString 的子类，表示一种可修改其值的字符串，此时若是不拷贝字符串，那么设置完属性之后，字符串的值就可能会在对象不知情的情况下遭人更改。所以，这时就要拷贝一份“不可变” (immutable)的字符串，确保对象中的字符串值不会无意间变动。只要实现属性所用的对象是“可变的” (mutable)，就应该在设置新属性值时拷贝一份。

##这个写法会出什么问题： @property (copy) NSMutableArray *array;
1. 没有使用noatomic进行修饰，会带来一些性能上的问题，默认是atomic也就是系统帮你自动加一把同步锁。
2. 使用copy修饰可变的话，那么会生成一个不可变对象，如果调用之前可变的方法，会造成找不到方法的错误

##@property 的本质是什么？ivar、getter、setter 是如何生成并添加到这个类中的
@property的本质是什么？
`@property = ivar + getter + setter;`
就是一个实例变量加上get，set方法
@property会自动帮你生成get，set方法以及实现，而且会帮你自动生成一个下划线的实例变量，相当于@synthesize的作用，还有就是如果同时重写set以及get方法那么就不会自动帮你生成下划线的实例变量了

@property生成了五个东西
1. OBJC_IVAR_$类名$属性名称 ：该属性的“偏移量” (offset)，这个偏移量是“硬编码” (hardcode)，表示该变量距离存放对象的内存区域的起始地址有多远。
2. setter 与 getter 方法对应的实现函数
3. ivar_list ：成员变量列表
4. method_list ：方法列表
5. prop_list ：属性列表

也就是说我们每次在增加一个属性,系统都会在 ivar_list 中添加一个成员变量的描述,在 method_list 中增加 setter 与 getter 方法的描述,在属性列表中增加一个属性的描述,然后计算该属性在对象中的偏移量,然后给出 setter 与 getter 方法对应的实现,在 setter 方法中从偏移量的位置开始赋值,在 getter 方法中从偏移量开始取值,为了能够读取正确字节数,系统对象偏移量的指针类型进行了类型强转.

##@protocol 和 category 中如何使用 @property
协议或者分类默认是无法添加property的，即使你加入了一个property，系统只是会帮你生成set以及get方法的声明，但是如果你调用这个property的set方法的时候就会crash，因为没有set以及get方法的实现。
如果我们必须实用的话那么就得用runtime进行实行了，实现这个两个函数

```objc
objc_setAssociatedObject
objc_getAssociatedObject
```

##@synthesize和@dynamic分别有什么作用？
1. @property有两个对应的词，一个是 @synthesize，一个是 @dynamic。如果 @synthesize和 @dynamic都没写，那么默认的就是@syntheszie var = _var;
2. @synthesize 的语义是如果你没有手动实现 setter 方法和 getter 方法，那么编译器会自动为你加上这两个方法。
3. @dynamic 告诉编译器：属性的 setter 与 getter 方法由用户自己实现，不自动生成。（当然对于 readonly 的属性只需提供 getter 即可）。假如一个属性被声明为 @dynamic var，然后你没有提供 @setter方法和 @getter 方法，编译的时候没问题，但是当程序运行到 instance.var = someVar，由于缺 setter 方法会导致程序崩溃；或者当运行到 someVar = var 时，由于缺 getter 方法同样会导致崩溃。编译时没问题，运行时才执行相应的方法，这就是所谓的动态绑定。
4. @sythesize和@dynamic两个不能同时使用



