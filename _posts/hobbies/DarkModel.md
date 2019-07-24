模糊效果
```swift
let blueEffect = UIBlurEffect(style: .systemThinMaterial)        
let vibracyView = UIVibrancyEffect(blurEffect: blueEffect, style: .fill)
```

动态颜色
```swift
 let dynamicColor = UIColor{ (traitCollection: UITraitCollection) -> UIColor in
            if traitCollection.userInterfaceStyle == .dark {
                return .black
            } else {
                return .white
            }
        }
```

设置cgcolor颜色

```swift
let layer = CALayer()

// Option 1
layer.borderColor = UIColor.label.resolvedColor(with: view.traitCollection).cgColor

// Option 2
view.traitCollection.performAsCurrent {
    layer.borderColor = UIColor.label.cgColor
}

// Option 3
let savedTraitCollection = UITraitCollection.current
UITraitCollection.current = view.traitCollection
layer.borderColor = UIColor.label.cgColor
UITraitCollection.current = savedTraitCollection

```

监听
```swift
 override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
        super.traitCollectionDidChange(previousTraitCollection)
        if traitCollection.hasDifferentColorAppearance(comparedTo: previousTraitCollection) {
            // Resolve fynamic colors again
        }
    }
```
使用 traits 的时机
```swift
UIViewController.viewWillLayoutSubviews()
UIView.layoutSubViews()
UIViewController.ViewDidLayoutSubviews()
```

设置指定页面模式

```swift
// UIView & UIViewControll
override var overrideUserInterfaceStyle: UIUserInterfaceStyle
```

设置整个APP的模式

info.plist 文件添加 UIUserInterfaceStyle，设置值为 Light 或 Dark


重写设置 VC 的模式
```swift
override func setOverrideTraitCollection(_ collection: UITraitCollection?, forChild childViewController: UIViewController)
```

动态图片

```swift
let image = UIImage(named: "xx")
let asset = image?.imageAsset
let resolvedImage = asset?.image(with: view.traitCollection)
```




API Update 
状态栏
.default -> .darkcontent | .lightContent

指示器
样式 .medium | .larger 
颜色 使用 color 设置

属性字符串
设置文字动态颜色 添加forgroundColor


web content
```css
:root {
    color-scheme: light dark;
    --post-body-color: #333;
}
.div1 {
    width: 100%;
    height: 120px;
    background-color: var(--post-body-color);
}
@media (prefers-color-scheme: dark) {
    :root {
        --post-body-color: #eee;
    }
}
```