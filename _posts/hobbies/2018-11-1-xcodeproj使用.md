---
title: xcodeproj 使用
categories: [聚沙成塔]
published: false
---

项目引入静态库项目

```ruby
require 'xcodeproj'

project_path = 'path to main project'

project = Xcodeproj::Project.open(project_path)

# 引入静态库项目
project.main_group.new_reference("/Users/zhanggy/Desktop/AiYouChe/Libiary/SubProject/SubProject.xcodeproj")

# 添加静态库到主工程
subp = project.root_object.project_references[0]
build_file = project.native_targets[0].frameworks_build_phase.add_file_reference(subp[:product_group].children[0])


project.save

```