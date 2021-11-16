# UIDynamic
## 简介
  UIKit动力学最大的特点就是将现实世界动力驱动的动画加入了UIKit。比如说重力，碰撞，悬挂等效果。
  
  Dynamic Animator 动画者，是动力学元素与底层ios物理引擎之间的中介，将behavior对象添加到Animator
  
  Dynamic Animator Item 动力学元素，是任何遵守了UIDynamicItem协议的对象
  
  UIDynamicBehavior: 仿真行为，是动力学行为的父类。 
    以下类均继承自该父类
    UIGravityBehavior（重力行为） 
    UICollisionBehavior（碰撞行为） 
    UIAttachmentBehavior（附着行为）
    UISnapBehavior（吸附行为）
    UIPushBehavior（推行为）
    UIDynamicItemBehavior（动力学元素行为）  
  
## UIGravityBehavior
    - (void)createBehavior {
      //根据一个范围创建动画者对象
      self.animator = [[UIDynamicAnimator alloc] initWithReferenceView: self.view];
      
      //根据某一个动力学元素创建行为
      //重力
      UIGravityBehavior *gravity = [[UIGravityBehavior alloc] initWithItems: @[self.redView]];
      
      //重力的属性
      //重力的方向，设置角度 或者 矢量坐标
      gravity.angle = M_PI;
      gravity.gravityDirection = CGVectorMake(1, 1);
      
      //设置重力大小
      gravity.magnitude = 10;

      //把行为添加到动画者当中
      [self.animator addBehavior: gravity];
    }
## UICollisionBehavior
  
    - (void)createBehavior {
    
      UICollisionBehavior *collision = [[UICollisionBehavior alloc]initWithItems:@[self.redView]];
      //把引用view的bounds变成边界，属性表示是否以当前坐标系边界作为检测碰撞的边界
      collision.translatesReferenceBoundsIntoBoundary = YES;
    
      [self.animator addBehavior: collision];
    }
