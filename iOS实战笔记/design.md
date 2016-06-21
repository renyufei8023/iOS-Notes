### Design设计原理
- 如果A控制器的view成为B控制器view的子控件，注意A控制器一定要成为B控制器的子控制器
![](images/设计原理1.png)

- 如果要把子控件暴露出去，一定要写readonly，防止别人修改
![](images/设计原理2.png)
