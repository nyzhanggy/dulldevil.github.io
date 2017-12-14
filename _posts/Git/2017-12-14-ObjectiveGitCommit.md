---
title: ObjectiveGit 使用（二） commit
categories: [git]
---

生成一个 commit 需要以下几个步骤：
1. 把文件加入 index
2. 把 index 写入 tree
3. 用 tree 和 提交信息生成 commit

每一步的具体含义可以查阅 [Pro Git](https://git-scm.com/book/zh/v2)。这里主要说下在 ObjectiveGit 中是怎样操作的:

先要获取仓库的 index

```objective_c
GTIndex *index = [_repo indexWithError:&error];
```

然后把要提交的文件添加到 index。这里的 filePath 为文件相对于仓库根目录的路径

```
[index addFile:filePath error:&error];
```

这里 add 之后只是在内存中操作，还需要将 add 操作写入本地

```
[index write:&error];
```

将 index 写入 tree

```
GTTree *tree = [index writeTree:&error];
```

最后将生成 commit

```
GTSignature *sign = [[GTSignature alloc] initWithName:@"Test" email:@"441473064@qq.com" time:[NSDate date]];
[_repo createCommitWithTree:tree message:string author:sign committer:sign parents:parents updatingReferenceNamed:@"HEAD" error:&error];
```

这里面会有一个参数 parents，如果是第一次提交的话 parents 是空的，但是不是第一次提交的话就是必填的，不然生成 commit 会报错。

```
GTReference *ref = [_repo headReferenceWithError:&error];
NSMutableArray *parents = [NSMutableArray array];
if (ref) {
   [parents addObject:ref.resolvedTarget];
}
```

这样一个完整的 commit 就完成了
