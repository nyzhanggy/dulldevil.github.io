---
title: ObjectiveGit（一）clone & commit
categories: [git]
---

[ObjectiveGit](https://github.com/libgit2/objective-git) 是对 [libgit2](https://github.com/libgit2/libgit2) 的上层封装。在项目中如果要使用 ObjectiveGit 的话需要生成对应的 framework。具体的导入方法可以查看[ObjectiveGit](https://github.com/libgit2/objective-git)。

如果想要更好的理解 ObjectiveGit ，可以提前看一下[Pro Git](https://git-scm.com/book/zh/v2)，会让你对git有更加深入的理解。

## clone
ObjectiveGit 中 clone 一个远程仓库会用到下面这方法

```objective_c
// GTRepository.h

+ (instancetype _Nullable)cloneFromURL:(NSURL *)originURL toWorkingDirectory:(NSURL *)workdirURL options:(NSDictionary * _Nullable)options error:(NSError **)error transferProgressBlock:(void (^ _Nullable)(const git_transfer_progress *, BOOL *stop))transferProgressBlock;
```

其中 options 中的 key 如下

```
GTRepositoryCloneOptionsTransportFlags
GTRepositoryCloneOptionsBare
GTRepositoryCloneOptionsPerformCheckout
GTRepositoryCloneOptionsCheckoutOptions
GTRepositoryCloneOptionsCredentialProvider
GTRepositoryCloneOptionsCloneLocal
GTRepositoryCloneOptionsServerCertificateURL
```

这里说一下这个 `GTRepositoryCloneOptionsCredentialProvider` 。如果 clone 远程仓库需要权限验证的话，比如账号密码，验证信息需要通过 `GTRepositoryCloneOptionsCredentialProvider` 配置， 对应的 value 是一个 `GTCredentialProvider`。

```objective_c
GTCredentialProvider *provider = [GTCredentialProvider providerWithBlock:^GTCredential * _Nullable(GTCredentialType type, NSString * _Nonnull URL, NSString * _Nonnull userName) {
       GTCredential *cre = [GTCredential credentialWithUserName:@"username" password:@"password" error:NULL];
       return cre;
   }];
   NSDictionary *cloneOptions = @{GTRepositoryCloneOptionsCredentialProvider: provider };
```


options 其底层对应的是 `git_clone_options` 其他变量的说明在 [libgit2/libgit2](https://github.com/libgit2/libgit2) 的 clone.h 中。



## commit
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
