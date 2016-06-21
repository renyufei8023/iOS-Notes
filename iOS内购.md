交易流程:
![](/Users/renyufei/iOS-Notes/images/QQ20160621-0.png)

内购流程:


![](/Users/renyufei/iOS-Notes/images/无标题.png)

内购常用方法:
1.请求有效的产品代号集合
2.购买指定产品
3.验证购买(在购买完成之后，验证)
4.恢复购买(针对非消耗品)

首先我们需要去请求有效的产品集合，也就是有哪些可以内购的产品，代码如下:

```objc
// 去自己的服务器请求所有想卖商品的ProductIds
    NSString *filePath = [[NSBundle mainBundle] pathForResource:@"iapdemo.plist" ofType:nil];
    NSArray *productArray = [NSArray arrayWithContentsOfFile:filePath];
    NSArray *productIdArray = [productArray valueForKeyPath:@"productId"];
    
    // 去苹果服务器请求可卖的商品
    NSSet *productIdSet = [NSSet setWithArray:productIdArray];
    // 1) 实例化产品请求
    SKProductsRequest *request = [[SKProductsRequest alloc] initWithProductIdentifiers:productIdSet];
    // 2) 设置代理
    request.delegate = self;
    // 3) 启动请求
    [request start];

```
提示：
1. 实例化请求时，必须指定有效的identifiers集合，之所以如此处理，主要是为了确保提交的内购商品真的通过了苹果的审批，处于可用状态！
2. 要想获取到准确的可用产品集合，需要通过代理方法实现`- (void)productsRequest:(SKProductsRequest *)request didReceiveResponse:(SKProductsResponse *)response`
3. 越狱用户无法测试内购，但是可以购买


购买产品
1. 内购的交易过程是通过SKPaymentTransactionObserver监控的，因此需要为IAPHelper添加交易观察者：

// 添加交易观察者对象
`[[SKPaymentQueue defaultQueue]addTransactionObserver:sharedInstance];`

2. 由于发起交易需要使用SKProduct对象，因此需要使用字典记录所有可用的商品

我们可以在`- (void)viewWillAppear:(BOOL)animated`方法中添加观察者，为什么不在viewDidload里面设置呢，因为viewDidload方法设置的话如果这个控制器的视图还没有加载完毕就跳转到另一个也有内购的控制器，会造成数据混乱

我们可以在`- (void)productsRequest:(SKProductsRequest *)request didReceiveResponse:(SKProductsResponse *)response`这个代理方法中拿到所有的内购信息，实现代码ruxia

```objc
#pragma mark - 实现SKProductsRequest的代理方法
- (void)productsRequest:(SKProductsRequest *)request didReceiveResponse:(SKProductsResponse *)response
{
    // 展示商品
    self.products = [response.products sortedArrayWithOptions:NSSortConcurrent usingComparator:^NSComparisonResult(SKProduct *obj1, SKProduct *obj2) {
        return [obj1.price compare:obj2.price];
    }];
    
    // 2.刷新表格
    [self.tableView reloadData];
}
```
购买的时候需要先创建票据，然后把票据加入到交易队列中去，代码如下

```objc
#pragma mark - 购买商品
- (void)buyProduct:(SKProduct *)product
{
    // 1.创建票据
    SKPayment *payment = [SKPayment paymentWithProduct:product];
    
    // 2.将票据加入到交易队列中
    [[SKPaymentQueue defaultQueue] addPayment:payment];
}
```
通过观察者方法监听交易状态

```objc
#pragma mark - 实现SKPaymentQueue的回调方法
/*
 队列中的交易发生改变时,就会调用该方法
 */
- (void)paymentQueue:(SKPaymentQueue *)queue updatedTransactions:(NSArray *)transactions
{
    /*
     SKPaymentTransactionStatePurchasing,    正在购买
     SKPaymentTransactionStatePurchased,     已经购买(购买成功)
     SKPaymentTransactionStateFailed,        购买失败
     SKPaymentTransactionStateRestored,      恢复购买
     SKPaymentTransactionStateDeferred       未决定
     */
    for (SKPaymentTransaction *transation in transactions) {
        switch (transation.transactionState) {
            case SKPaymentTransactionStatePurchasing:
                NSLog(@"用户正在购买");
                break;
                
            case SKPaymentTransactionStatePurchased:
                NSLog(@"购买成功,将对应的商品给用户");
                
                // 将交易从交易队列中移除
                [queue finishTransaction:transation];
                break;
                
            case SKPaymentTransactionStateFailed:
                NSLog(@"购买失败,告诉用户没有付钱成功");
                
                // 将交易从交易队列中移除
                [queue finishTransaction:transation];
                break;
                
            case SKPaymentTransactionStateRestored:
                NSLog(@"恢复商品,将对应的商品给用户");
                // transation.payment.productIdentifier
                // 将交易从交易队列中移除
                [queue finishTransaction:transation];
                break;
                
            case SKPaymentTransactionStateDeferred:
                NSLog(@"未决定");
                break;
            default:
                break;
        }
    }
}

```

恢复购买试下如下

```objc
[[SKPaymentQueue defaultQueue] restoreCompletedTransactions];
```


