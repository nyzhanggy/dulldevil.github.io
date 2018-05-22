---
title: Xcode打包生成ipa四个选项的具体区别
categories: [聚沙成塔]
---

#### 1. Save for iOS App Store Deployment

保存到本地 准备上传App Store 或者在越狱的iOS设备上使用

#### 2. Save for Ad Hoc Deployment

苹果的官方解释是：To distribute your app to users with designated devices, select “Save for Ad Hoc Deployment.” The app will be code signed with the distribution certificate.（保存到本地 准备在账号添加的可使用设备上使用（具体为在开发者账户下添加可用设备的udid），该app包是发布证书编译的）

#### 3. Save for Enterprise Deployment 生成企业包

这种主要针对企业级账户下 准备本地服务器分发的app，也可直接发给你的客户或测试人员安装，无需在帐号中添加设备的udid。注意：此证书为299$的企业证书

#### 4. Save for Development Deployment

苹果的官方解释是：To distribute your app for internal testing, select “Save for Development Deployment.” The app will be code signed with your development certificate. (针对内部测试使用，主要给开发者的设备(具体也为在开发者账户下添加可用设备的udid)。该app包是开发证书编译的)
