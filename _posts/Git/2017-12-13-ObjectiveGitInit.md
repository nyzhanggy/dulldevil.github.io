---
title: ObjectiveGit 使用（一）init & clone
categories: [git]
---

[ObjectiveGit](https://github.com/libgit2/objective-git) 是对 [libgit2](https://github.com/libgit2/libgit2) 的上层封装。在项目中如果要使用 ObjectiveGit 的话需要生成对应的 framework。具体的导入方法可以查看[ObjectiveGit](https://github.com/libgit2/objective-git)。

如果想要更好的理解 ObjectiveGit ，可以提前看一下[Pro Git](https://git-scm.com/book/zh/v2)，会让你对git有更加深入的理解。

## init
ObjectiveGit 中初始化一个本地仓库需要用到下面这个方法

```objective_c
// GTRepository.h

+ (instancetype _Nullable)initializeEmptyRepositoryAtFileURL:(NSURL *)fileURL options:(NSDictionary * _Nullable)options error:(NSError **)error;
```

在这个方法中 fileURL 是必填的参数，是仓库的地址路径；options 是初始化时候的配置项，如果不填的话会使用默认的配置。

对于这个 options 可选的 key 如下：

```
GTRepositoryInitOptionsFlags
GTRepositoryInitOptionsMode
GTRepositoryInitOptionsWorkingDirectoryPath
GTRepositoryInitOptionsDescription
GTRepositoryInitOptionsTemplateURL
GTRepositoryInitOptionsInitialHEAD
GTRepositoryInitOptionsOriginURLString
```

options 其底层对应的是 `git_repository_init_options` ，其他变量的说明在 [libgit2/libgit2](https://github.com/libgit2/libgit2) 的 repository.h 中。

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
        GTCredential *cre = [GTCredential credentialWithUserName:@"admin" password:@"admin" error:NULL];
        return cre;
    }];

NSDictionary *options = @{GTRepositoryRemoteOptionsCredentialProvider: provider };
```


options 其底层对应的是 `git_clone_options` 其他变量的说明在 [libgit2/libgit2](https://github.com/libgit2/libgit2) 的 clone.h 中。
