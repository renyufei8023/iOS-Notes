###使用UIStoryBoard给UITableView添加HeadView
可以直接拖动一个UITableViewController 上去，默认就会有一个prototype cell. 如果UITableViewController 不能满足你的需要，也可以使用UIViewController + UITableView 的组合。步骤：
	1. 在UIViewController 上拖动一个UITableView。看起来像这样
[![alt text](http://img.bbs.csdn.net/upload/201504/14/1428973805_630689.png "title")]

2. 再拖一个UITableViewCell 到 UITableView 
[![Alt text](http://img.bbs.csdn.net/upload/201504/14/1428973822_556755.png)]
3. 拖一个UIView放到prototype cell的上面，即headerview 
[![Alt text](http://img.bbs.csdn.net/upload/201504/14/1428973830_416097.png)]


