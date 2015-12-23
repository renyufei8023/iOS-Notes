###CALyer
<table class="kc-table" cellspacing="0" cellpadding="0" width="800" border="0"> <thead> <tr> <th width="136">属性 </th> <th width="595">说明 </th> <th width="67">是否支持隐式动画 </th></tr></thead> <tbody> <tr> <td valign="top" width="136">anchorPoint</td> <td valign="top" width="595">和中心点position重合的一个点，称为“锚点”，锚点的描述是相对于x、y位置比例而言的默认在图像中心点(0.5,0.5)的位置</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">backgroundColor</td> <td valign="top" width="595">图层背景颜色 </td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">borderColor</td> <td valign="top" width="595">边框颜色</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">borderWidth</td> <td valign="top" width="595">边框宽度</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">bounds</td> <td valign="top" width="595">图层大小</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">contents</td> <td valign="top" width="595">图层显示内容，例如可以将图片作为图层内容显示</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">contentsRect</td> <td valign="top" width="595">图层显示内容的大小和位置</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">cornerRadius</td> <td valign="top" width="595">圆角半径</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">doubleSided</td> <td valign="top" width="595">图层背面是否显示，默认为YES</td> <td valign="top" width="67">否</td></tr> <tr> <td valign="top" width="136">frame</td> <td valign="top" width="595">图层大小和位置，不支持隐式动画，所以CALayer中很少使用frame，通常使用bounds和position代替</td> <td valign="top" width="67">否</td></tr> <tr> <td valign="top" width="136">hidden</td> <td valign="top" width="595">是否隐藏</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">mask</td> <td valign="top" width="595">图层蒙版</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">maskToBounds</td> <td valign="top" width="595">子图层是否剪切图层边界，默认为NO</td> <td valign="top" width="67">是 </td></tr> <tr> <td valign="top" width="136">opacity</td> <td valign="top" width="595">透明度 ，类似于UIView的alpha</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">position</td> <td valign="top" width="595">图层中心点位置，类似于UIView的center </td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">shadowColor</td> <td valign="top" width="595">阴影颜色</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">shadowOffset</td> <td valign="top" width="595">阴影偏移量</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">shadowOpacity</td> <td valign="top" width="595">阴影透明度，注意默认为0，如果设置阴影必须设置此属性</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">shadowPath</td> <td valign="top" width="595">阴影的形状</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">shadowRadius</td> <td valign="top" width="595">阴影模糊半径</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">sublayers</td> <td valign="top" width="595">子图层</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">sublayerTransform</td> <td valign="top" width="595">子图层形变</td> <td valign="top" width="67">是</td></tr> <tr> <td valign="top" width="136">transform</td> <td valign="top" width="595">图层形变</td> <td valign="top" width="67">是</td></tr></tbody></table>

###基础动画
动画创建的步骤：
1. 初始化动画并设置动画属性
2. 设置动画属性初始值（可忽略），结束值以及其他动画属性
3. 给图层添加动画

###转场动画
转场动画就是从一个场景以动画的形式过渡到另一个场景。转场动画的使用一般分为以下几个步骤：
1. 创建转场动画
2. 设置转场类型，子类型（可选）以及其他属性
3. 设置转场后的新视图并添加到图层
<table class="kc-table" cellspacing="0" cellpadding="0" width="801" border="0">
<thead>
<tr>
<th width="160">动画类型</th>
<th width="198">说明</th>
<th width="371">对应常量</th>
<th width="68">是否支持方向设置</th></tr>
<thead>
<tbody>
<tr class="subhead">
<td>公开API</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td></tr>
<tr>
<td valign="top" width="160">fade</td>
<td width="198">淡出效果</td>
<td valign="top" width="371">kCATransitionFade</td>
<td valign="top" width="68">是</td></tr>
<tr>
<td valign="top" width="160">movein</td>
<td width="198">新视图移动到旧视图上</td>
<td valign="top" width="371">kCATransitionMoveIn</td>
<td valign="top" width="68">是</td></tr>
<tr>
<td valign="top" width="160">push</td>
<td width="198">新视图推出旧视图</td>
<td valign="top" width="371">kCATransitionPush</td>
<td valign="top" width="68">是</td></tr>
<tr>
<td valign="top" width="160">reveal</td>
<td width="198">移开旧视图显示新视图</td>
<td valign="top" width="371">kCATransitionReveal</td>
<td valign="top" width="68">是</td></tr>
<tr class="subhead">
<td>私有API</td>
<td>&nbsp;</td>
<td>私有API只能通过字符串访问</td>
<td>&nbsp;</td></tr>
<tr>
<td valign="top" width="160">cube</td>
<td width="198">立方体翻转效果</td>
<td valign="top" width="371">无</td>
<td valign="top" width="68">是</td></tr>
<tr>
<td valign="top" width="160">oglFlip</td>
<td width="198">翻转效果</td>
<td valign="top" width="371">无</td>
<td valign="top" width="68">是</td></tr>
<tr>
<td valign="top" width="160">suckEffect</td>
<td width="198">收缩效果</td>
<td valign="top" width="371">无</td>
<td valign="top" width="68">否</td></tr>
<tr>
<td valign="top" width="160">rippleEffect</td>
<td width="198">水滴波纹效果</td>
<td valign="top" width="371">无</td>
<td valign="top" width="68">否</td></tr>
<tr>
<td valign="top" width="160">pageCurl</td>
<td width="198">向上翻页效果</td>
<td valign="top" width="371">无</td>
<td valign="top" width="68">是</td></tr>
<tr>
<td valign="top" width="160">pageUnCurl</td>
<td width="198">向下翻页效果</td>
<td valign="top" width="371">无</td>
<td valign="top" width="68">是</td></tr>
<tr>
<td valign="top" width="160">cameralIrisHollowOpen</td>
<td width="198">摄像头打开效果</td>
<td valign="top" width="371">无</td>
<td valign="top" width="68">否</td></tr>
<tr>
<td valign="top" width="160">cameraIrisHollowClose</td>
<td width="198">摄像头关闭效果</td>
<td valign="top" width="371">无</td>
<td valign="top" width="68">否</td></tr></tbody></table>
<p>另外对于支持方向设置的动画类型还包含子类型：</p>
<table class="kc-table" cellspacing="0" cellpadding="0" width="800" border="0">
<thead>
<tr>
<th width="160">动画子类型</th>
<th width="198">说明</th></tr>
<thead>
<tbody>
<tr>
<td valign="top" width="400">kCATransitionFromRight</td>
<td valign="top" width="400">从右侧转场</td></tr>
<tr>
<td valign="top" width="400">kCATransitionFromLeft</td>
<td valign="top" width="400">从左侧转场</td></tr>
<tr>
<td valign="top" width="400">kCATransitionFromTop</td>
<td valign="top" width="400">从顶部转场</td></tr>
<tr>
<td valign="top" width="400">kCATransitionFromBottom</td>
<td valign="top" width="400">从底部转场</td></tr></tbody></table>###Core Animation
1. CAAnimation：核心动画的基础类，不能直接使用，负责动画运行时间、速度的控制，本身实现了CAMediaTiming协议。

2. CAPropertyAnimation：属性动画的基类（通过属性进行动画设置，注意是可动画属性），不能直接使用。

3. CAAnimationGroup：动画组，动画组是一种组合模式设计，可以通过动画组来进行所有动画行为的统一控制，组中所有动画效果可以并发执行。

4. CATransition：转场动画，主要通过滤镜进行动画效果设置。

5. CABasicAnimation：基础动画，通过属性修改进行动画参数控制，只有初始状态和结束状态。

6. CAKeyframeAnimation：关键帧动画，同样是通过属性进行动画参数控制，但是同基础动画不同的是它可以有多个状态控制。

基础动画、关键帧动画都属于属性动画，就是通过修改属性值产生动画效果，开发人员只需要设置初始值和结束值，中间的过程动画（又叫“补间动画”）由系统自动计算产生。和基础动画不同的是关键帧动画可以设置多个属性值，每两个属性中间的补间动画由系统自动完成，因此从这个角度而言基础动画又可以看成是有两个关键帧的关键帧动画。

###UIView动画封装
####动画设置参数
在动画方法中有一个option参数，UIViewAnimationOptions类型，它是一个枚举类型，动画参数分为三类，可以组合使用：<br>
1. 常规动画属性设置（可以同时设置多个进行设置）<br>

*  UIViewAnimationOptionLayoutSubviews：动画过程中保证子视图跟随运动。
*  UIViewAnimationOptionAllowUserInteraction：动画过程中允许用户交互。
*  UIViewAnimationOptionBeginFromCurrentState：所有视图从当前状态开始运行。
*  UIViewAnimationOptionRepeat：重复运行动画。
*  UIViewAnimationOptionAutoreverse ：动画运行到结束点后仍然以动画方式回到初始点。
*  UIViewAnimationOptionOverrideInheritedDuration：忽略嵌套动画时间设置。
*  UIViewAnimationOptionOverrideInheritedCurve：忽略嵌套动画速度设置。
*  UIViewAnimationOptionAllowAnimatedContent：动画过程中重绘视图（注意仅仅适用于转场动画）。
*  UIViewAnimationOptionShowHideTransitionViews：视图切换时直接隐藏旧视图、显示新视图，而不是将旧视图从父视图移除（仅仅适用于转场动画）
*  UIViewAnimationOptionOverrideInheritedOptions ：不继承父动画设置或动画类型。
<br/>


2.动画速度控制（可从其中选择一个设置）

* UIViewAnimationOptionCurveEaseInOut：动画先缓慢，然后逐渐加速。
* UIViewAnimationOptionCurveEaseIn ：动画逐渐变慢。
* UIViewAnimationOptionCurveEaseOut：动画逐渐加速。
* UIViewAnimationOptionCurveLinear ：动画匀速执行，默认值.

3.转场类型（仅适用于转场动画设置，可以从中选择一个进行设置，基本动画、关键帧动画不需要设置）

* UIViewAnimationOptionTransitionNone：没有转场动画效果。
* UIViewAnimationOptionTransitionFlipFromLeft ：从左侧翻转效果。
* UIViewAnimationOptionTransitionFlipFromRight：从右侧翻转效果。
* UIViewAnimationOptionTransitionCurlUp：向后翻页的动画过渡效果。
* UIViewAnimationOptionTransitionCurlDown ：向前翻页的动画过渡效果。
* UIViewAnimationOptionTransitionCrossDissolve：旧视图溶解消失显示下一个新视图的效果。   
* UIViewAnimationOptionTransitionFlipFromTop ：从上方翻转效果。   
* UIViewAnimationOptionTransitionFlipFromBottom：从底部翻转效果。

4.动画模式设置（同前面关键帧动画动画模式一一对应，可以从其中选择一个进行设置）
* UIViewKeyframeAnimationOptionCalculationModeLinear：连续运算模式。
* UIViewKeyframeAnimationOptionCalculationModeDiscrete ：离散运算模式。
* UIViewKeyframeAnimationOptionCalculationModePaced：均匀执行运算模式。
* UIViewKeyframeAnimationOptionCalculationModeCubic：平滑运算模式。
* UIViewKeyframeAnimationOptionCalculationModeCubicPaced：平滑均匀运算模式。
* 前面说过关键帧动画有两种形式，上面演示的是属性值关键帧动画，路径关键帧动画目前UIView还不支持。

#####
其实仅仅有一个视图UIImageView做转场动画，每次转场通过切换UIImageView的内容而已。如果有两个完全不同的视图，并且每个视图布局都很复杂，此时要在这两个视图之间进行转场可以使用+ (void)transitionFromView:(UIView *)fromView toView:(UIView *)toView duration:(NSTimeInterval)duration options:(UIViewAnimationOptions)options completion:(void (^)(BOOL finished))completion NS_AVAILABLE_IOS(4_0)方法进行两个视图间的转场，需要注意的是默认情况下转出的视图会从父视图移除，转入后重新添加，可以通过UIViewAnimationOptionShowHideTransitionViews参数设置，设置此参数后转出的视图会隐藏（不会移除）转入后再显示。 
