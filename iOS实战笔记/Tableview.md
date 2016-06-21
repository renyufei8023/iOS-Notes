## tableView性能优化 - cell的循环利用方式1

```objc
/**
 *  什么时候调用：每当有一个cell进入视野范围内就会调用
 */
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    // 0.重用标识
    // 被static修饰的局部变量：只会初始化一次，在整个程序运行过程中，只有一份内存
    static NSString *ID = @"cell";

    // 1.先根据cell的标识去缓存池中查找可循环利用的cell
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:ID];

    // 2.如果cell为nil（缓存池找不到对应的cell）
    if (cell == nil) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:ID];
    }

    // 3.覆盖数据
    cell.textLabel.text = [NSString stringWithFormat:@"testdata - %zd", indexPath.row];

    return cell;
}
```
## tableView性能优化 - cell的循环利用方式2
- 定义一个全局变量

```objc
// 定义重用标识
NSString *ID = @"cell";
```

- 注册某个标识对应的cell类型

```objc
// 在这个方法中注册cell
- (void)viewDidLoad {
    [super viewDidLoad];

    // 注册某个标识对应的cell类型
    [self.tableView registerClass:[UITableViewCell class] forCellReuseIdentifier:ID];
}
```

- 在数据源方法中返回cell

```objc
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    // 1.去缓存池中查找cell
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:ID];

    // 2.覆盖数据
    cell.textLabel.text = [NSString stringWithFormat:@"testdata - %zd", indexPath.row];

    return cell;
}
```

## tableView性能优化 - cell的循环利用方式3
- 在storyboard中设置UITableView的Dynamic Prototypes Cell
![](images/Snip20150602_152.png)

- 设置cell的重用标识
![](images/Snip20150602_153.png)

- 在代码中利用重用标识获取cell

```objc
// 0.重用标识
// 被static修饰的局部变量：只会初始化一次，在整个程序运行过程中，只有一份内存
static NSString *ID = @"cell";

// 1.先根据cell的标识去缓存池中查找可循环利用的cell
UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:ID];

// 2.覆盖数据
cell.textLabel.text = [NSString stringWithFormat:@"cell - %zd", indexPath.row];

return cell;
```
## 错误将UIViewController当做UITableViewController来用
![](images/Snip20150602_110.png)

## UITableView的常见设置
```objc
// 分割线颜色
self.tableView.separatorColor = [UIColor redColor];

// 隐藏分割线
self.tableView.separatorStyle = UITableViewCellSeparatorStyleNone;
```

## UITableViewCell的常见设置
```objc
// 取消选中的样式
cell.selectionStyle = UITableViewCellSelectionStyleNone;
// 设置选中的背景色
UIView *selectedBackgroundView = [[UIView alloc] init];
selectedBackgroundView.backgroundColor = [UIColor redColor];
cell.selectedBackgroundView = selectedBackgroundView;

// 设置默认的背景色
cell.backgroundColor = [UIColor blueColor];

// 设置默认的背景色
UIView *backgroundView = [[UIView alloc] init];
backgroundView.backgroundColor = [UIColor greenColor];
cell.backgroundView = backgroundView;

// backgroundView的优先级 > backgroundColor
// 设置指示器
//    cell.accessoryType = UITableViewCellAccessoryDisclosureIndicator;
cell.accessoryView = [[UISwitch alloc] init];
```

## 自定义cell
- `等高的cell`
    - `storyboard自定义cell`
        - 1.创建一个继承自UITableViewCell的子类，比如XMGDealCell<br>
        ![](images/Snip20150602_305.png)
        - 2.在storyboard中
            - 往cell里面增加需要用到的子控件<br>
            ![](images/Snip20150602_302.png)
            - 设置cell的重用标识<br>
            ![](images/Snip20150602_303.png)
            - 设置cell的class为XMGDealCell<br>
            ![](images/Snip20150602_304.png)
        - 3.在控制器中
            - 利用重用标识找到cell
            - 给cell传递模型数据<br>
            ![](images/Snip20150602_301.png)
        - 4.在XMGDealCell中
            - 将storyboard中的子控件连线到类扩展中<br>
            ![](images/Snip20150602_299.png)
            - 需要提供一个模型属性，重写模型的set方法，在这个方法中设置模型数据到子控件上<br>
            ![](images/Snip20150602_298.png)
            ![](images/Snip20150602_300.png)

    - `xib自定义cell`
        - 1.创建一个继承自UITableViewCell的子类，比如XMGDealCell<br>
        - 2.创建一个xib文件（文件名建议跟cell的类名一样），比如XMGDealCell.xib
            - 拖拽一个UITableViewCell出来
            - 修改cell的class为XMGDealCell
            - 设置cell的重用标识
            - 往cell中添加需要用到的子控件
        - 3.在控制器中
            - 利用registerNib...方法注册xib文件
            - 利用重用标识找到cell（如果没有注册xib文件，就需要手动去加载xib文件）
            - 给cell传递模型数据<br>
        - 4.在XMGDealCell中
            - 将xib中的子控件连线到类扩展中
            - 需要提供一个模型属性，重写模型的set方法，在这个方法中设置模型数据到子控件上
            - 也可以将创建获得cell的代码封装起来（比如cellWithTableView:方法）

    - `代码自定义cell(使用frame)`
        - 1.创建一个继承自UITableViewCell的子类，比如XMGDealCell
            - 在initWithStyle:reuseIdentifier:方法中
                - 添加子控件
                - 设置子控件的初始化属性（比如文字颜色、字体）
            - 在layoutSubviews方法中设置子控件的frame
            - 需要提供一个模型属性，重写模型的set方法，在这个方法中设置模型数据到子控件
        - 2.在控制器中
            - 利用registerClass...方法注册XMGDealCell类
            - 利用重用标识找到cell（如果没有注册类，就需要手动创建cell）
            - 给cell传递模型数据
            - 也可以将创建获得cell的代码封装起来（比如cellWithTableView:方法）

    - `代码自定义cell(使用autolayout)`
        - 1.创建一个继承自UITableViewCell的子类，比如XMGDealCell
            - 在initWithStyle:reuseIdentifier:方法中
                - 添加子控件
                - 添加子控件的约束（建议使用`Masonry`）
                - 设置子控件的初始化属性（比如文字颜色、字体）
            - 需要提供一个模型属性，重写模型的set方法，在这个方法中设置模型数据到子控件
        - 2.在控制器中
            - 利用registerClass...方法注册XMGDealCell类
            - 利用重用标识找到cell（如果没有注册类，就需要手动创建cell）
            - 给cell传递模型数据
            - 也可以将创建获得cell的代码封装起来（比如cellWithTableView:方法）
- 非等高的cell
    - xib自定义cell(重点)
        - 在模型中增加一个cellHeight属性，用来存放对应cell的高度
        - 在cell的模型属性set方法中调用[self layoutIfNeed]方法强制布局，然后计算出模型的cellheight属性值
        - 在控制器中实现tableView:estimatedHeightForRowAtIndexPath:方法，返回一个估计高度，比如200
        - 在控制器中实现tableView:heightForRowAtIndexPath:方法，返回cell的真实高度（模型中的cellHeight属性）
    - storyboard自定义cell
    - 代码自定义cell（frame）
    - 代码自定义cell（Autolayout）
