---
title: OC 代码格式化
---


代码规范工具


## uncrustify

### 安装
可使用 brew 直接安装

```shell
brew install uncrustify
```

安装成功之后可以使用 `uncrustify -v` 查看版本号，同时验证是否安装成功。


### 使用

直接通过命令行对目标文件进行格式化

```
uncrustify -c ~/.uncrustify_obj_c.cfg targetFile.m  -l oc --no-backup --replace 

-c          配置文件       
-l          文件语言
--no-backup 修改之后不做文件备份
--replace   要格式化的目标文件
```

更多的操作可通过 `uncrustify --help ` 查看



## OCLint

### 安装

可使用brew安装OCLint，首先需要设置brew的第三方仓库oclint/formulae。

```
brew tap oclint/formulae
```

然后安装OCLint。

```
brew install oclint
```

在日志分析的阶段还需要用到一个格式化的工具 xcpretty

```shell
gem install -n /usr/local/bin xcpretty
```

### 使用

OCLint 是对build日志的分析，所以第一步先要对项目进行build，并将日志格式化输出

```shell
xcodebuild -workspace MizLiCai.xcworkspace -scheme MizLiCai COMPILER_INDEX_STORE_ENABLE=NO build | xcpretty -r json-compilation-database -o compile_commands.json
```

得到 compile_commands.json 文件之后就可以对文件进行分析，在 compile_commands.json 所在的目录下执行：

```shell
oclint-json-compilation-database  -e Pods -- -report-type html -o=report.html

# -e Pods             不分析 Pods 文件夹下的所有文件，可添加多个
# -report-type html   输出文件格式
# -o=report.html      输出文件名
```

### 规则配置

OCLint 的规则可以在命令中之间添加参数来修改默认的值或者不使用某些规则。

```
oclint-json-compilation-database  -e Pods -- -rc=LONG_LINE=200 -disable-rule=LongLine -report-type html -o=report.html
```

如果规则比较多可以创建 .oclint 文件

```
rules:
disable-rules:
  - AssignIvarOutsideAccessors
  - LongLine
  - UnusedMethodParameter
  - LongVariableName
  - ShortVariableName
  - RedundantNilCheck
  - UseObjectSubscripting
  - InvertedLogic

rule-configurations:
  - key: LONG_METHOD 
    value: 100


output: oclint.html
report-type: html
```



