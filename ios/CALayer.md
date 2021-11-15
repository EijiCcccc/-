# CALayer


## 1.CALayer 和 UIView 的区别

    layer负责视图的渲染，UIview负责事件监听，响应事件
    
## 2.CALayer属性

  UIView *redView = [[UIView alloc] init];
  
<!--   //边框 -->
  
  redView.layer.borderWidth = 10;
  
  redView.layer.borderColor = [UIColor grayColor].CGColor;
  
<!--   //阴影 -->
  redView.layer.shadowOffset = CGSizeZero;
  
  redView.layer.shadowColor = [UIColor blueColor].CGColor;
  
  redView.layer.shadowOpacity = 1;
  
  redView.layer.shadowRadius = 50;

<!--   //圆角 -->
  redView.layer.cornerRadius = 50;
  
  redView.layer.masksToBounds = YES;
    
<!--   //bounds -->
  redView.layer.bounds = CGRectMake(0, 0, 200, 200);
  
<!--   //位置,默认情况下，center -->
  redView.layer.position = CGPointMake(0, 0);
  
<!--   //视图内容 -->
  redView.layer.contents = CFBridgingRelease([UIImage imageNamed:@""].CGImage);
  
## 3.时钟实例

### 添加钟表
  CALayer *clock = [[CALayer alloc] init];
  
  clock.bounds = CGRectMake(0, 0, 200, 200);
  
  clock.position = CGPointMake(200, 200);

  clock.contents =  (__bridge id)[UIImage imageNamed:@"p1"].CGImage;
  
  clock.cornerRadius = 100;
  
  clock.masksToBounds = YES;
  
### 添加秒针

  CALayer *second = [[CALayer alloc] init];
  
  second.bounds = CGRectMake(0, 0, 2, 100);
  
  second.position = clock.position;
  
  second.backgroundColor = [UIColor redColor].CGColor;
  
<!--   //锚点 -->
  second.anchorPoint = CGPointMake(0.5, 0.8);
  
  self.second = second;
    
  [self.view.layer addSublayer: clock];
  
  [self.view.layer addSublayer: second];
  
<!--   //每秒调用一次 -->
  //[NSTimer scheduledTimerWithTimeInterval: 1 target:self selector:@selector(timeChanged) userInfo: nil repeats:YES];
  
<!--   //每秒60帧，定时器 -->
  CADisplayLink *link = [CADisplayLink displayLinkWithTarget: self selector:@selector(timeChanged)];
  
  [link addToRunLoop: [NSRunLoop mainRunLoop] forMode: NSDefaultRunLoopMode];
  
  [self timeChanged];
  
### 实现timeChanged方法

-(void)timeChanged {

    NSLog(@"time");
    
    CGFloat angle = 2 * M_PI / 60;
    
    NSDate *date = [NSDate date];
    
    <!--  使用日期格式转化获取。NSDateFormatter   -->
    <!--  NSDateFormatter *formatter = [[NSDateFormatter alloc] init];    -->
    <!--  formatter.dateFormat = @"ss";    -->
    <!--  CGFloat time = [[formatter stringFromDate: date] floatValue];    -->
    <!--  self.second.affineTransform = CGAffineTransformRotate(self.second.affineTransform,  angle);    -->
    
    //根据日历获取
    NSCalendar *cal = [NSCalendar currentCalendar];
    
    CGFloat time = [cal component: NSCalendarUnitSecond fromDate: date];
    
    self.second.affineTransform = CGAffineTransformMakeRotation(angle * time);
    
}
