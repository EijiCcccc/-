# 核心动画
## Core Animatetion简介
  是一组非常强大的动画处理api
  可以用在Mac OS X 和ios 平台
  动画执行都是在后台操作的，不会阻塞主线程
  注意：Core Animation 是直接作用在CALayer上的
  
 ## CAAnimation继承结构, 实现了CAMediaTiming协议，可以指定执行时间
 
 ### CAPropertyAnimations 属性动画，需要先指定动画的属性
 #### CABasicAnimation 基本动画
    
    -(void)doAnimation {
    
      <!--   创建动画对象，做什么动画   -->
      CABasicAnimation *anim = [[CABasicAnimation alloc] init];
      
      <!--    怎么做动画    -->
      anim.keyPath = @"position.x";
      
      <!--       anim.byValue = @10; 在自身的基础上增加 -->
      
      <!--    从 fromValue 到 toValue    -->
      anim.toValue = @300;
      anim.fromValue = @10;
      
      <!--   不希望回到原来的位置     -->
      anim.fillMode = kCAFillModeForwards;
      <!--    如果不设置removedOnCompletion，那么fillMode不起作用    -->
      anim.removedOnCompletion = NO;
      
      <!--     添加动画，对谁做动画   -->
      [self.layer addAnimation: anim forKey: nil];
    }
 
 #### CAKeyframeAnimation 关键帧动画
 
    - (void)doAnimation  {
      <!--   创建关键帧动画   -->
      CAKeyframeAnimation *anim = [[CAKeyframeAnimation alloc] init];

      <!--       NSValue *v1 = [NSValue valueWithCGPoint: CGPointMake(100, 100)];
            NSValue *v2 = [NSValue valueWithCGPoint: CGPointMake(150, 100)];
            NSValue *v3 = [NSValue valueWithCGPoint: CGPointMake(100, 150)];
            NSValue *v4 = [NSValue valueWithCGPoint: CGPointMake(150, 150)];
            NSValue *v5 = [NSValue valueWithCGPoint: CGPointMake(100, 100)];
            anim.values = @[v1, v2, v3 ,v4, v5]; 
            通过设置关键点去描述运动的轨迹
      -->

      anim.keyPath = @"position";
      anim.duration = 2;

      <!-- 通过UIBezierPath设置运动轨迹  -->
      UIBezierPath *path = [UIBezierPath bezierPathWithArcCenter:CGPointMake(150, 150) radius:100 startAngle:0 endAngle:2* M_PI clockwise: 1];
      anim.path = path.CGPath;

      <!-- 设置循环执行动画 -->
      anim.repeatCount = INT_MAX;

      [self.layer addAnimation: anim forKey: nil];
  }
   
 ### CAAnimationGroup 组动画， 添加各种属性动画
 
   -(void)doAnimation {
      <!--  创建组动画   -->
      CAAnimationGroup *group = [[CAAnimationGroup alloc] init];

      <!-- 创建基本动画 -->
      CABasicAnimation *anim = [[CABasicAnimation alloc] init];
      anim.keyPath = @"transform.rotation";
      anim.byValue = @(2 * M_PI);

      <!-- 创建关键帧动画 -->
      CAKeyframeAnimation *anim1 = [[CAKeyframeAnimation alloc] init];
      UIBezierPath *path = [UIBezierPath bezierPathWithArcCenter:CGPointMake(150, 150) radius:100 startAngle:0 endAngle:2* M_PI clockwise: 1];
      anim1.path = path.CGPath;
      anim1.keyPath = @"position";

      <!--  设置组动画 -->
      group.animations = @[anim, anim1];
      group.duration = 3;
      group.repeatCount = INT_MAX;

      [self.layer addAnimation: group forKey: nil];
  }
 
 ### CATranstion 转场动画
      <!-- 需要先添加swipe手势 -->
      
      <!-- 实现swipe手势 -->
     - (IBAction)imageChanged:(UISwipeGestureRecognizer * )sender {
        self.imageName ++;

        if (self.imageName == 3) {
            self.imageName = 0;
        }

        self.imageView.image = [UIImage imageNamed: [NSString stringWithFormat:@"%ld", self.imageName + 1]];

        CATransition *anim = [[CATransition alloc] init];
        anim.type = @"cube";
        
        <!--      根据左右滑动设置动画方向    -->
        anim.type = @"cube";
        if (sender.direction == UISwipeGestureRecognizerDirectionLeft) {
            anim.subtype = kCATransitionFromRight;
        } else {
            anim.subtype = kCATransitionFromLeft;
        }
        [self.imageView.layer addAnimation: anim forKey: nil];
    }
 
 
    转场效果
   ![image](https://user-images.githubusercontent.com/45653681/141759985-611c65bd-225b-4a6b-9225-54ebb6c2a89f.png)

## 禁止隐式动画，控件的根layer是没有隐式动画
       [CATransaction begin];
       [CATransaction setDisableActions:YES];
       [CATransaction commit];

## layer 的transform 属性
layer的transform 是一个CATransform3D类型，x、y、z轴

