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

### 添加UICollisionBehavior
    <!-- 因为需要绘制路径 -->
    - (void)loadView {
      self.view = [[CYView alloc] initWithFrame: [UIScreen mainScreen].bounds];
      self.view.backgroundColor = [UIColor whiteColor];
   }
   
    - (void)createBehavior {
    
      <!--   与另外一个view发生碰撞，需要都添加到  UICollisionBehavior -->
      UICollisionBehavior *collision = [[UICollisionBehavior alloc]initWithItems:@[self.redView, self.blueView]];
      <!--       把引用view的bounds变成边界，属性表示是否以当前坐标系边界作为检测碰撞的边界 -->
      collision.translatesReferenceBoundsIntoBoundary = YES;
      
       <!--  添加边界， 通过2个点确定一条线      -->
       <!--  [collision addBoundaryWithIdentifier: @"key" fromPoint: CGPointMake(0, 200) toPoint:CGPointMake(200, 250)];       -->
       <!--   以一个自定义路径为边界   -->
      [collision addBoundaryWithIdentifier: @"key" forPath: [UIBezierPath bezierPathWithRect: self.blueView.frame]];
      
      <!--   实时监听     -->
      collision.action = ^{
        <!--    获取redView， 绘制在下落过程中的实际大小, 碰撞后产生旋转，会导致frame变化    -->
        CYView *view = (CYView *)self.view;
        view.redRect = self.redView.frame;
        [self.view setNeedsDisplay];
      };
      
      [self.animator addBehavior: collision];
    }

    <!-- 绘制边界重写drawRect -->
    @interface CYView : UIView

    @property (nonatomic, assign) CGRect redRect;

    @end

    @implementation CYView

    -(void)drawRect:(CGRect)rect {
      <!--   绘制collision创建的边界   -->
      <!--     //    UIBezierPath *patn = [[UIBezierPath alloc] init]; -->
      <!--     //    [patn moveToPoint: CGPointMake(0, 200)]; -->
      <!--     //    [patn addLineToPoint: CGPointMake(200, 250)]; -->
      
      <!--   绘制redView的frame大小     -->
      [[UIBezierPath bezierPathWithRect: self.redRect] stroke];
    }
    @end
      
### 代理方法   
     <!-- 与item开始碰撞     -->
    - (void)collisionBehavior:(UICollisionBehavior *)behavior beganContactForItem:(id <UIDynamicItem>)item1 withItem:(id <UIDynamicItem>)item2 atPoint:(CGPoint)p;
     <!-- 与item结束碰撞     -->
    - (void)collisionBehavior:(UICollisionBehavior *)behavior endedContactForItem:(id <UIDynamicItem>)item1 withItem:(id <UIDynamicItem>)item2;

     <!-- 与边界开始碰撞     -->
    // The identifier of a boundary created with translatesReferenceBoundsIntoBoundary or setTranslatesReferenceBoundsIntoBoundaryWithInsets is nil
    - (void)collisionBehavior:(UICollisionBehavior*)behavior beganContactForItem:(id <UIDynamicItem>)item withBoundaryIdentifier:(nullable id <NSCopying>)identifier atPoint:(CGPoint)p;
    
     <!-- 与边界结束碰撞     -->
    - (void)collisionBehavior:(UICollisionBehavior*)behavior endedContactForItem:(id <UIDynamicItem>)item withBoundaryIdentifier:(nullable id <NSCopying>)identifier;

    
  
### 事例
    // The identifier of a boundary created with translatesReferenceBoundsIntoBoundary or setTranslatesReferenceBoundsIntoBoundaryWithInsets is nil
    - (void)collisionBehavior:(UICollisionBehavior*)behavior beganContactForItem:(id <UIDynamicItem>)item withBoundaryIdentifier:(nullable id <NSCopying>)identifier atPoint:(CGPoint)p {
      <!-- 通过上面设置绘制路径的key获取碰撞的对象，需要强转类型   -->
      NSString *str = (NSString *)identifier;
      if ([str isEqualToString: @"key"){ 
  
      }
    }
