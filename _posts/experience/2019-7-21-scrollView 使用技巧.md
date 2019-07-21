---
title: scrollView 使用技巧
categories: [聚沙成塔]
---


## 弹簧效果
当 scorllview 的contentsize 小于 frame 的时候，scorllview 默认整体就不会滑动了，并且会失去弹簧效果的。如果想要在 contentsize 小于frame 的时候还能有弹簧效果，可设置 alwaysBounceVertical 或者 alwaysBounceHorizontal

```
alwaysBounceVertical = YES; // 水平
alwaysBounceHorizontal = YES; // 垂直
```

## 自动布局
对scrollView进行自动布局的时候需要先在scrollview 上添加一个view，将view的上下左右的约束与scrollview对齐。然后设置 width 与 height 为contentsize。

```
[_scrollView addSubview:_scrollContentView];
[_scrollContentView mas_makeConstraints:^(MASConstraintMaker *make) {
   make.edges.equalTo(_scrollView);
   make.width.equalTo(_scrollView);
}];

```
## 停止到指定位置

```
- (void)scrollViewWillEndDragging:(UIScrollView *)scrollView withVelocity:(CGPoint)velocity targetContentOffset:(inout CGPoint *)targetContentOffset {
    float targetOffset = targetContentOffset->y < 20 ? 0 : 40;
    targetContentOffset->y = targetOffset;
}

```

## 手势穿透

```
- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldRecognizeSimultaneouslyWithGestureRecognizer:(UIGestureRecognizer *)otherGestureRecognizer {
    return YES;
}
```

