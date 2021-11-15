# 触摸事件
## UIView的触摸事件
### 继承NSResponse才有的方法
#### 触摸开始
    -(void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
        NSLog(@"%s", __func__);
    }
#### 触摸移动
    -(void)touchesEnded:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
         NSLog(@"%s", __func__);
    }

#### 触摸结束
    -(void)touchesMoved:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{
         NSLog(@"%s", __func__);
    }


#### 触摸意外中断
    -(void)touchesCancelled:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
         NSLog(@"%s", __func__);
    }
#### UITouch对象说明

#####   打印(NSSet<UITouch *> *)touches
######      <UITouch: 0x7ffa33009bc0> 
######      phase: Began 
######      tap count: 1 
######      window: <UIWindow: 0x7ffa33008010; frame = (0 0; 414 896); gestureRecognizers = <NSArray: 0x6000027231b0>; layer = <UIWindowLayer: 0x600002979520>> 
######      view: <CYView: 0x7ffa31412f30; frame = (20 92; 120 120);  autoresize = RM+BM; layer = <CALayer: 0x6000029180c0>> 
######      location in window: {99, 154.66665649414062} 
######      previous location in window: {99, 154.66665649414062} 
######      location in view: {79, 62.666656494140625} 
######      previous location in view: {79, 62.666656494140625}

#####   Touch属性
######    @property(nonatomic,readonly) NSTimeInterval      timestamp;
######    @property(nonatomic,readonly) UITouchPhase        phase;
######    @property(nonatomic,readonly) NSUInteger          tapCount;   // touch down within a certain point within a certain amount of time
######    @property(nonatomic,readonly) UITouchType         type API_AVAILABLE(ios(9.0));
######    @property(nonatomic,readonly) CGFloat majorRadius API_AVAILABLE(ios(8.0));
######    @property(nonatomic,readonly) CGFloat majorRadiusTolerance API_AVAILABLE(ios(8.0));
######    @property(nullable,nonatomic,readonly,strong) UIWindow                        *window;
######    @property(nullable,nonatomic,readonly,strong) UIView                          *view;
######    @property(nullable,nonatomic,readonly,copy)   NSArray <UIGestureRecognizer *> *gestureRecognizers API_AVAILABLE(ios(3.2));

######    - (CGPoint)locationInView:(nullable UIView *)view;
  以某一个view为坐标系点击的位置
######    - (CGPoint)previousLocationInView:(nullable UIView *)view;
  以某一个view为坐标系，在移动中上一个点的位置
######    - (CGPoint)preciseLocationInView:(nullable UIView *)view API_AVAILABLE(ios(9.1));
  点击对象的位置
######    - (CGPoint)precisePreviousLocationInView:(nullable UIView *)view API_AVAILABLE(ios(9.1));
   上一次触摸对象的位置

#### NSSet
 set: 集合。无序，通过anyObject取值，通过for in遍历
 array：有序，可以通过下标取值，通过for，for in遍历

#### 案例 手指在屏幕上移动时，在划过的痕迹上显示图片，并在2秒内淡出

    <!-- //手指在屏幕上移动时，在划过的痕迹上显示图片，并在2秒内淡出 -->

    -(void)addSpark: (NSSet *)touches {
  
      int i = 0;
      
      for (UITouch *t in touches) {
      
          CGPoint p = [t locationInView: t.view];
          
          UIImageView *imageView= [[UIImageView alloc] initWithImage: self.images[i]];
          
          imageView.center = p;
          
          [self addSubview:imageView];
          
          NSLog(@"%lu", touches.count);
          
          [UIView animateWithDuration: 2 animations:^{
          
              imageView.alpha = 0;
              
          } completion:^(BOOL finished) {
          
              [imageView removeFromSuperview];
              
          }];
          
          i++;
          
      }
      
    }
  
  <!-- //使一个view根据手势滑动 -->

    -(void)touchesMoved:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{

      UITouch *t = touches.anyObject;

      CGPoint p = [t locationInView: self.superview];

      CGPoint lastP = [t previousLocationInView: self.superview];

      CGFloat offsetX = p.x - lastP.x;

      CGFloat offsetY = p.y - lastP.y;

      self.center = CGPointMake(self.center.x + offsetX, self.center.y + offsetY);

    }


#### 控件不能响应的情况
##### 1.user interaction = no
##### 2.控件隐藏
##### 3.透明度小于0.01
##### 4.子视图超出了父控件的有效范围

#### 事件的传递和响应
##### 1.什么是hitTest?
hitTest: withEvent: 是UIView 里面的一个方法，该方法的作用 在于 : 在视图的层次结构中寻找一个最适合的 view 来响应触摸事件。

该方法会被系统调用，调用的时候，如果返回为nil，即事件有可能被丢弃，否则返回最合适的view 来响应事件
##### 2.hitTest调用顺序
运行循环 -> UIApplication -> UIWindow -> UIController -> UIController.view -> superView ->view

##### 3.模拟方法实现

    <!--  recursively calls -pointInside:withEvent:. point is in the receiver's coordinate system -->

    -(UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {

      //如果空间不允许交互那么返回nil
      if (self.userInteractionEnabled == NO || self.alpha <= 0.01 || self.hidden == YES) {
          return  nil;
      }

      //如果这个点不在当前空间中返回nil
      if (![self pointInside: point withEvent: event]) {
          return  nil;
      }

      //从后向前遍历每一个子控件
      for (int i = (int)self.subviews.count - 1; i >= 0; i--) {
          //获取一个子控件
          UIView *lastVw = self.subviews[i];
          //把当前触摸点坐标转换为相对于子控件的触摸点坐标
          CGPoint subPoint = [self convertPoint:point toView: lastVw];
          //判断是否在子控件中找到了合适的子控件
          UIView *nextVw = [lastVw hitTest:subPoint withEvent: event];
          //如果找到了返回
          if (nextVw) {
              return nextVw;
          }
      }
      //如果以上都没有执行 return；那么返回自己
      return self;
    }

##### 4.事件的传递顺序
事件传递顺序与hitTest 的调用顺序 恰好相反

1、 首先由 view 来尝试处理事件，如果他处理不了，事件将被传递到他的父视图superview

2、superview 也尝试来处理事件，如果他处理不了，继续传递他的父视图 
UIViewcontroller.view

3、UIViewController.view尝试来处理该事件，如果处理不了，将把该事件传递给UIViewController

4、UIViewController尝试处理该事件，如果处理不了，将把该事件传递给主窗口Window

5、主窗口Window尝试来处理该事件，如果处理不了，将传递给应用单例Application

6、如果Application也处理不了，则该事件将会被丢弃

##### 5.UIButton 如果注册了一个点击事件，点击事件不会往父级传递。

##### 6.响应者链条：触摸事件产生以后，所有执行touchBegan的响应者对象

### 手势解锁demo
[手势解锁.zip](https://github.com/EijiCcccc/learing/files/7526086/default.zip)


[image](https://user-images.githubusercontent.com/45653681/141438045-47088789-fbea-41e6-84e4-e48a595bfdee.png)


## 手势识别 Gesture Recognizer

### UITapGestureRecognizer

   //单机，多次点击事件
   UITapGestureRecognizer *tap = [[UITapGestureRecognizer alloc] initWithTarget: self action: @selector(tap:)];
   
   tap.numberOfTapsRequired = 2;
   
   tap.numberOfTouchesRequired = 2;

   [self.image addGestureRecognizer: tap];

### UILongPressGestureRecognizer

     UILongPressGestureRecognizer *longPress = [[UILongPressGestureRecognizer alloc] initWithTarget: self action: @selector(longPress:)];
    
    //长按时间
    longPress.minimumPressDuration = 3;
    
    //偏移距离
    longPress.allowableMovement = 10;
    
    [self.image addGestureRecognizer: longPress];
    
### UISwipeGestureRecognizer

    UISwipeGestureRecognizer *swipe = [[UISwipeGestureRecognizer alloc] initWithTarget: self action: @selector(swipe:)];
    
    //轻扫方向， 如果写成 UISwipeGestureRecognizerDirectionLeft ｜ UISwipeGestureRecognizerDirectionRight
    //判断的时候无法判断方向
    swipe.direction = UISwipeGestureRecognizerDirectionLeft;
    
    [self.image addGestureRecognizer: swipe];
    
 //实现图片根据swipe手势旋转
 
 -(void)rotation: (UIRotationGestureRecognizer *)sender {
     <!-- sender.rotation 转动的角度 -->
    NSLog(@"%f", sender.rotation);
    
    
     <!--  转到某个角度(每次都以原位置为0开始)， 但是第二次旋转时，图片会突然从0开始   -->
     <!--     self.image.transform = CGAffineTransformMakeRotation(sender.rotation); -->
    
     <!--   转动某个角度，如果手势旋转度数为 10， 20， 30，则图片旋转 10， 30， 60(较原位置)  -->
    self.image.transform = CGAffineTransformRotate(self.image.transform, sender.rotation);
    
    //需要恢复到初始的状态，下次转动不会累加之前的度数。
    sender.rotation = 0;
}
    
### UIPinchGestureRecognizer

     UIPinchGestureRecognizer *pinch = [[UIPinchGestureRecognizer alloc] initWithTarget: self action: @selector(pinch:)];
     
    [self.image addGestureRecognizer: pinch];
    
    实现图片跟随缩放手势

     -(void)pinch: (UIPinchGestureRecognizer *)sender {
     
         NSLog(@"%f", sender.scale);
         
          <!--          原理同rotation -->
         self.image.transform = CGAffineTransformScale(self.image.transform, sender.scale, sender.scale);
    
         sender.scale = 1;
         
     }
   
### UIPanGestureRecognizer

     UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget: self action:@selector(pan:)];
    
    [self.image addGestureRecognizer: pan];
    
    图片根据拖拽移动
    -(void)pan: (UIPanGestureRecognizer *) sender {
    
         <!--   需要从sender.view 中获取点击的point   -->
         CGPoint p = [sender translationInView: sender.view];

         self.image.transform = CGAffineTransformTranslate(self.image.transform, p.x, p.y);
          <!-- 需要归0，setTranslation -->
         [sender setTranslation: CGPointZero inView: sender.view];
     }
     }
     
     
### 多手势冲突
     添加代理后实现代理方法
     
     - (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldRecognizeSimultaneouslyWithGestureRecognizer:       (UIGestureRecognizer *)otherGestureRecognizer {
          return YES;
}
