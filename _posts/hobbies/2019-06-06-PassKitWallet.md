---
title: PassKit Wallet
categories: [略知一二]
---

创建资源文件，然后签名，放到项目中

https://www.jianshu.com/p/23c2f8cf449c

```swift
guard PKPassLibrary.isPassLibraryAvailable() else {
    print("设备不支持")
    return
}

guard let url = Bundle.main.url(forResource: "Lollipop1", withExtension: "pkpass") else {
    
    return
}

guard let passData = try? Data.init(contentsOf: url) else {return}

guard let pass = try? PKPass.init(data: passData) else {return}

if PKAddPassesViewController.canAddPasses(),let addPassVC = PKAddPassesViewController.init(pass: pass) {
    self.present(addPassVC, animated: true, completion: nil)
}
```