###CGAffineTransform相关函数
* CGAffineTransformMakeTranslation(CGFloat tx,
  CGFloat ty);是改变位置的，
* CGAffineTransformRotate(CGAffineTransform t,
  CGFloat angle)旋转，需要传入一个transform和一个角度
* CGAffineTransformMakeRotation(CGFloat angle)旋转的，直接传入一个角度
* CGAffineTransformScale(CGAffineTransform t,
  CGFloat sx, CGFloat sy)是缩放的
* CGAffineTransformIdentity线性代数里面讲的矩阵变换，这个是恒等变换，当你改变过一个view.transform属性或者view.layer.transform的时候需要恢复默认状态的话，记得先把他们重置可以使用，（假设你一直不断的改变一个view.transform的属性，而每次改变之前没有重置的话，你会发现后来 的改变和你想要的发生变化了，不是你真正想要的结果）


###CTM
Quartz转换实现的原理:Quartz把绘图分成两个部分，
用户空间，即和设备无关，
设备空间，
用户空间和设备空间中间存在一个转换矩阵 ： CTM
#####Quartz提供的3大功能
* 移动
* 旋转
* 缩放

演示如下，首先加载一张图片

	void CGContextDrawImage (
	CGContextRef c,
	CGRect rect,
	CGImageRef image
	);

* 移动函数

	CGContextTranslateCTM (myContext, 100, 50);
	
* 旋转函数

		include <math.h>
		static inline double radians (double degrees) {return degrees * M_PI/180;}
		CGContextRotateCTM (myContext, radians(–45.));
		
缩放

	CGContextScaleCTM (myContext, .5, .75);

翻转， 两种转换合成后的效果，先把图片移动到右上角，然后旋转180度

	CGContextTranslateCTM (myContext, w,h);
	CGContextRotateCTM (myContext, radians(-180.));

组合几个动作

	CGContextTranslateCTM (myContext, w/4, 0);
	CGContextScaleCTM (myContext, .25,  .5);
	CGContextRotateCTM (myContext, radians ( 22.));

	CGContextRotateCTM (myContext, radians ( 22.));
	CGContextScaleCTM (myContext, .25,  .5);
	CGContextTranslateCTM (myContext, w/4, 0);

上面是通过直接修改当前的ctm实现3大效果，下面是通过创建Affine Transforms，然后连接ctm实现同样的3种效果
这样做的好处是可以重用这个Affine Transforms
应用Affine Transforms 到ctm的函数

	void CGContextConcatCTM (
	CGContextRef c,
	CGAffineTransform transform
	);

	Creating Affine Transforms
移动效果

	CGAffineTransform CGAffineTransformMakeTranslation (
	CGFloat tx,
	CGFloat ty
	);

	CGAffineTransform CGAffineTransformTranslate (
	CGAffineTransform t,
	CGFloat tx,
	CGFloat ty
	);

旋转效果

	CGAffineTransform CGAffineTransformMakeRotation (
	CGFloat angle
	);

	CGAffineTransform CGAffineTransformRotate (
	CGAffineTransform t,
	CGFloat angle
	);

缩放效果

	CGAffineTransform CGAffineTransformMakeScale (
	CGFloat sx,
	CGFloat sy
	);

	CGAffineTransform CGAffineTransformScale (
	CGAffineTransform t,
	CGFloat sx,
	CGFloat sy
	);

反转效果

	CGAffineTransform CGAffineTransformInvert (
	CGAffineTransform t
	);

只对局部产生效果

	CGRect CGRectApplyAffineTransform (
	CGRect rect,
	CGAffineTransform t
	);

判断两个AffineTrans是否相等

	bool CGAffineTransformEqualToTransform (
	CGAffineTransform t1,
	CGAffineTransform t2
	);

获得Affine Transform

	CGAffineTransform CGContextGetUserSpaceToDeviceSpaceTransform (
	CGContextRef c
	);

下面的函数只起到查看的效果，比如看一下这个用户空间的点，转换到设备空间去坐标是多少

	CGPoint CGContextConvertPointToDeviceSpace (
	CGContextRef c,
	CGPoint point
	);

	CGPoint CGContextConvertPointToUserSpace (
	CGContextRef c,
	CGPoint point
	);

	CGSize CGContextConvertSizeToDeviceSpace (
	CGContextRef c,
	CGSize size
	);

	CGSize CGContextConvertSizeToUserSpace (
	CGContextRef c,
	CGSize size
	);

	CGRect CGContextConvertRectToDeviceSpace (
	CGContextRef c,
	CGRect rect
	);

	CGRect CGContextConvertRectToUserSpace (
	CGContextRef c,
	CGRect rect
	);

CTM真正的数学行为
这个转换矩阵其实是一个 3×3的 举证
如下图

下面举例说明几个转换运算的数学实现
x y 是原先点的坐标
下面是从用户坐标转换到设备坐标的计算公式

下面是一个identity matrix，就是输入什么坐标，出来什么坐标，没有转换

最终的计算结果是 x=x，y=y，

可以用函数判断这个矩阵是不是一个 identity matrix
bool CGAffineTransformIsIdentity (
CGAffineTransform t
);

参考:http://developer.apple.com/library/ios/#documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/dq_affine/dq_affine.html

	– (void)willAnimateFirstHalfOfRotationToInterfaceOrientation:	(UIInterfaceOrientation)toInterfaceOrientation   duration:	(NSTimeInterval)duration
	{

		if (toInterfaceOrientation == UIInterfaceOrientationPortrait)
			{
				b=YES;

				self.view=mainvv;
				self.view.transform = CGAffineTransformIdentity;
				self.view.transform = CGAffineTransformMakeRotation(degreesToRadian(0));
				self.view.bounds = CGRectMake(0.0, 0.0, 768.0, 1004.0);

			}
		else if (toInterfaceOrientation == UIInterfaceOrientationLandscapeLeft)
	{
		b=NO;

		self.view = self.vv;
		self.view.transform = CGAffineTransformIdentity;
		self.view.transform = CGAffineTransformMakeRotation(degreesToRadian(-90));
		self.view.bounds = CGRectMake(0.0, 0.0, 1024.0, 748.0);

	}
	else if (toInterfaceOrientation == UIInterfaceOrientationPortraitUpsideDown)
	{

		b=YES;
		self.view=mainvv;
		self.view.transform = CGAffineTransformIdentity;
		self.view.transform = CGAffineTransformMakeRotation(degreesToRadian(180));
		self.view.bounds = CGRectMake(0.0, 0.0, 768.0, 1004.0);

	}
	else if (toInterfaceOrientation == UIInterfaceOrientationLandscapeRight)
	{

		b=NO;
		self.view = self.vv;
		self.view.transform = CGAffineTransformIdentity;
		self.view.transform = CGAffineTransformMakeRotation(degreesToRadian(90));
		self.view.bounds = CGRectMake(0.0, 0.0, 1024.0, 748.0);

	}

	}

3

Quartz转换实现的原理:Quartz把绘图分成两个部分，
用户空间，即和设备无关，
设备空间，
用户空间和设备空间中间存在一个转换矩阵 ： CTM
本章实质是讲解CTM

Quartz提供的3大功能
移动，旋转，缩放

演示如下，首先加载一张图片

	void CGContextDrawImage (
	CGContextRef c,
	CGRect rect,
	CGImageRef image
	);

移动函数

	CGContextTranslateCTM (myContext, 100, 50);

旋转函数

	include <math.h>
	static inline double radians (double degrees) {return degrees * M_PI/180;}
	CGContextRotateCTM (myContext, radians(–45.));

缩放

	CGContextScaleCTM (myContext, .5, .75);

翻转， 两种转换合成后的效果，先把图片移动到右上角，然后旋转180度

	CGContextTranslateCTM (myContext, w,h);
	CGContextRotateCTM (myContext, radians(-180.));

组合几个动作

	CGContextTranslateCTM (myContext, w/4, 0);
	CGContextScaleCTM (myContext, .25,  .5);
	CGContextRotateCTM (myContext, radians ( 22.));

	CGContextRotateCTM (myContext, radians ( 22.));
	CGContextScaleCTM (myContext, .25,  .5);
	CGContextTranslateCTM (myContext, w/4, 0);

上面是通过直接修改当前的ctm实现3大效果，下面是通过创建Affine Transforms，然后连接ctm实现同样的3种效果
这样做的好处是可以重用这个Affine Transforms
应用Affine Transforms 到ctm的函数

	void CGContextConcatCTM (
	CGContextRef c,
	CGAffineTransform transform
	);

	Creating Affine Transforms
移动效果

	CGAffineTransform CGAffineTransformMakeTranslation (
	CGFloat tx,
	CGFloat ty
	);

	CGAffineTransform CGAffineTransformTranslate (
	CGAffineTransform t,
	CGFloat tx,
	CGFloat ty
	);

旋转效果

	CGAffineTransform CGAffineTransformMakeRotation (
	CGFloat angle
	);

	CGAffineTransform CGAffineTransformRotate (
	CGAffineTransform t,
	CGFloat angle
	);

缩放效果

	CGAffineTransform CGAffineTransformMakeScale (
	CGFloat sx,
	CGFloat sy
	);

	CGAffineTransform CGAffineTransformScale (
	CGAffineTransform t,
	CGFloat sx,
	CGFloat sy
	);

反转效果

	CGAffineTransform CGAffineTransformInvert (
	CGAffineTransform t
	);

只对局部产生效果

	CGRect CGRectApplyAffineTransform (
	CGRect rect,
	CGAffineTransform t
	);

判断两个AffineTrans是否相等

	bool CGAffineTransformEqualToTransform (
	CGAffineTransform t1,
	CGAffineTransform t2
	);

获得Affine Transform
	
	CGAffineTransform CGContextGetUserSpaceToDeviceSpaceTransform (
	CGContextRef c
	);

下面的函数只起到查看的效果，比如看一下这个用户空间的点，转换到设备空间去坐标是多少
	
	CGPoint CGContextConvertPointToDeviceSpace (
	CGContextRef c,
	CGPoint point
	);

	CGPoint CGContextConvertPointToUserSpace (
	CGContextRef c,
	CGPoint point
	);

	CGSize CGContextConvertSizeToDeviceSpace (
	CGContextRef c,
	CGSize size
	);

	CGSize CGContextConvertSizeToUserSpace (
	CGContextRef c,
	CGSize size
	);

	CGRect CGContextConvertRectToDeviceSpace (
	CGContextRef c,
	CGRect rect
	);

	CGRect CGContextConvertRectToUserSpace (
	CGContextRef c,
	CGRect rect
	);

CTM真正的数学行为
这个转换矩阵其实是一个 3×3的 举证
如下图

下面举例说明几个转换运算的数学实现
x y 是原先点的坐标
下面是从用户坐标转换到设备坐标的计算公式

下面是一个identity matrix，就是输入什么坐标，出来什么坐标，没有转换

最终的计算结果是 x=x，y=y，

可以用函数判断这个矩阵是不是一个 identity matrix
bool CGAffineTransformIsIdentity (
CGAffineTransform t
);

