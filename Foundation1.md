###Foundation
######NSSting

	void test2(){
    	NSLog(@"to upper is %@",[@"Hello world!" uppercaseString]);//转换成全部大写字母
    	NSLog(@"to upper is %@",[@"Hello world!" lowercaseString]);//全部转换成小写字母
    	NSLog(@"to upper is %@",[@"Hello world!" capitalizedString]);//第一个字母大写，其他小写
    
    	BOOL result = [@"abc" isEqualToString:@"aBc"];
    	NSLog(@"hehe%i",result);
    
    	NSComparisonResult result2 = [@"abc" isEqualToString:@"aBC"];//如果是[@"abc" caseInsensitiveCompare:@"aBc"]则忽略大小写比较
    
    	if (result2 == NSOrderedAscending) {
       	 NSLog(@"left<right");
    	}else if (result2 == NSOrderedDescending){
        	NSLog(@"right>left");
    	}else if (result2 == NSOrderedSame){
        	NSLog(@"right=left");
    	}
		}
		
		void test3(){
    NSLog(@"has prefix ab? %i",[@"abcdef" hasPrefix:@"ab"]);//是否以ab开头
    //结果：has prefix ab? 1
    NSLog(@"has suffix ab? %i",[@"abcdef" hasSuffix:@"ef"]);//是否以ef结尾
    
    NSRange range = [@"abcdefgabef" rangeOfString:@"cde"];
    if (range.location == NSNotFound) {
        NSLog(@"没有找到");
    }else{
        NSLog(@"range is %@",NSStringFromRange(range));//是个结构体，一个是从0开始的索引，另一个是长度
    }
    
	}

	void test4(){
    	NSLog(@"%@",[@"abcdef" substringFromIndex:3]);//从第三个索引开始截取
    	NSLog(@"%@",[@"abcdef" substringToIndex:3]);//从0开始截取到第三个索引
    	NSLog(@"%@",[@"abcdef" substringWithRange:NSMakeRange(2, 3)]);//在这range范围之内截取
    
    	NSString *str1 = @"12.abcd.3a.";
    	NSArray *array1 = [str1 componentsSeparatedByString:@"."];//字符串分割
    	NSLog(@"%@",array1);
	}

	void test5(){
    	NSLog(@"%li",(long)[@"12" integerValue]);
    	NSLog(@"%zi",[@"hello world,世界你好" length]);
    	NSLog(@"%c",[@"abc" characterAtIndex:0]);//取出指定位置的字符
    	const char *s = [@"abc" UTF8String];
    	NSLog(@"%s",s);
	}
	
	
######NSArray

		void test7(){
    NSString *path = @"Users/renyufei/Desktop/test.txt";
    NSLog(@"path%@",[path pathExtension]);//取得扩展名，注意ObjC中扩展名不包括"."
    NSLog(@"%@",[path stringByDeletingPathExtension]);//删除扩展名
    NSLog(@"%@",[path stringByAppendingPathExtension:@"mp3"]);
	}

	void test8(){
    	NSObject *obj = [[NSObject alloc]init];
    
    NSArray *array= [NSArray arrayWithObjects:@"abc",obj,@"cde",@"opq",@25, nil];
    NSLog(@"长度%zi",array.count);
    NSLog(@"%i",[array containsObject:@"cde"]);//判断是否包含某个对象，返回的是BOOL值
    NSLog(@"%@",[array lastObject]);
    NSLog(@"%lu",(unsigned long)[array indexOfObject:@"cde"]);//对象所在的位置
    
	//    [array enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
	//        NSLog(@"%@",obj);
	//    }];
    
    NSEnumerator *enumeratop = [array reverseObjectEnumerator];
    id obj2 = nil;
    while (obj2 = [enumeratop nextObject]) {
        NSLog(@"嘿嘿%@",obj2);
    }//可以把数组逆序输出
    
    NSLog(@"新书租%@",[array subarrayWithRange:NSMakeRange(1, 3)]);//根据一定范围获得一个新的数组
    NSLog(@"%@",[array componentsJoinedByString:@","]);//把数组连起来并且形成一个字符串
    
    NSString *path = @"/Users/renyufei/Desktop/array.xml";
    [array writeToFile:path atomically:YES];
    NSArray *array3 = [NSArray arrayWithContentsOfFile:path];
    NSLog(@"321123%@",array3);
}

	void test9(){
    	NSArray *array = [NSArray arrayWithObjects:@"3",@"1",@"2",@"6",@"0", nil];
    	NSArray *array1 = [array sortedArrayUsingSelector:@selector(compare:)];//从小到大进行排序
    	NSLog(@"hahah%@",array1);

	}

	void test10(){
    NSMutableArray *array1 = [NSMutableArray arrayWithObjects:@"1",@"3",@"2", nil];
    NSArray *array2 = [array1 sortedArrayUsingSelector:@selector(compare:)];
    NSLog(@"哈哈哈%@",array2);
    [array1 sortUsingSelector:@selector(compare:)];//从小到大排序，而且会改变array1的排序。在不可变数组中无论对数组怎么排序，原来的数组顺序都不会改变，但是在可变数组中如果使用sortUsingSelector:排序原来的数组顺序就发生了变化。
    NSLog(@"array1是什么%@",array1);
    
	}


	void test11(){
    NSDictionary *dict1 = [NSDictionary dictionaryWithObjects:[NSArray arrayWithObjects:@"1",@"2", nil] forKeys:[NSArray arrayWithObjects:@"a",@"b", nil]];
    NSLog(@"字典是什么%@",dict1);
    NSLog(@"%@",[dict1 objectsForKeys:[NSArray arrayWithObjects:@"a",@"e", nil] notFoundMarker:@"not found"]);
    
    NSMutableDictionary *dic = [NSMutableDictionary dictionaryWithObjectsAndKeys:@"1",@"a",
                              @"2",@"b",
                              @"3",@"c",
                              @"2",@"d",
                              nil];
    [dic removeObjectForKey:@"b"];
    [dic addEntriesFromDictionary:@{@"e":@"7",@"f":@"6"}];
    
    
	}


	void test12(){
    NSNumber *number1 = [NSNumber numberWithChar:'a'];
    NSArray *array1 = [NSArray arrayWithObject:number1];
    NSLog(@"是什么呢%@",array1);
    NSNumber *number2 = [array1 lastObject];
    NSLog(@"嘿嘿%@",number2);
    
    char char1 = [number2 charValue];
    NSLog(@"char1是什么%c",char1);
    
	}

	void test13(){
    CGPoint point1 = CGPointMake(10, 20);
    NSValue *value1 = [NSValue valueWithPoint:point1];
    NSArray *array1 = [NSArray arrayWithObject:value1];
    NSLog(@"数组1是什么%@",array1);
    
    NSValue *value2 = [array1 lastObject];
    CGPoint point2 = [value2 pointValue];
    NSLog(@"x=%f,y=%f",point2.x,point2.y);
	}
	
	void test6(){
    NSMutableArray *array = [NSMutableArray array];
    [array addObject:@"/Users"];
    [array addObject:@"Kenshincui"];
    [array addObject:@"Desktop"];
    
    NSString *path = [NSString pathWithComponents:array];//把数组里面的内容拼接成一个字符串
    NSLog(@"哈哈%@",[path pathComponents]);//把字符串组成数组
    
    
    NSLog(@"%i",[path isAbsolutePath]);//是否是绝对路径（其实就是看字符串是否以“/”开头）
    NSLog(@"%@",[path lastPathComponent]);//取得最后一个目录
    NSLog(@"%@",[path stringByDeletingLastPathComponent]);//删除最后一个目录
    NSLog(@"%@",[path stringByAppendingPathComponent:@"Documents"]);//拼接路径
    
    
}
