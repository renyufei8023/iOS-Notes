# UIAlertController
```objc
    //创建一个弹框控制器
    /*preferredStyle:
    UIAlertControllerStyleActionSheet = 0,
    UIAlertControllerStyleAlert*/

    UIAlertController *alert=[UIAlertController alertControllerWithTitle:@"输入商品名称" message:nil preferredStyle:UIAlertControllerStyleAlert];
    //创建一个取消和一个确定按钮
    UIAlertAction *action=[UIAlertAction actionWithTitle:@"取消" style:UIAlertActionStyleCancel handler:nil];
    //因为需要点击确定按钮后改变文字的值，所以需要在确定按钮这个block里面进行相应的操作
    UIAlertAction *action1=[UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
        UITextField *textfiel=  [alert.textFields lastObject];//textFields这个是文本框数组，添加几个文本框，数组里面就有几个文本框，*这里只有一个
        deal.title=textfiel.text;//修改模型数据
        [weakself.tableView reloadRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationTop];//刷新数据
    }   ];
    //将取消和确定按钮添加进弹框控制器
    [alert addAction:action];
    [alert addAction:action1];
    //添加一个文本框到弹框控制器
    [alert addTextFieldWithConfigurationHandler:^(UITextField * _Nonnull textField) {
        textField.placeholder=@"请输入名称";//文本框占位文字
    }];
    //显示弹框控制器
    [self presentViewController:alert animated:YES completion:nil];
```

###老版本的使用
- **UIAlertView**

![](images/UIAlertView.png)
- **UIActionSheet**

![](images/UIActionSheet.png)
