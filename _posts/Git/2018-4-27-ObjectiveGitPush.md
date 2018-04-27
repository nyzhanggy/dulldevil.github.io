---
title: ObjectiveGit（二）push & pull
categories: [git]
---

## push
生成一个commit之后，接下来就可以推送到远端了。push之前需要获取到远端仓库信息。由于一个本地仓库可以设置多个远端仓库，这里默认就取第一个远端仓库。

```
GTConfiguration *conf = [_repo configurationWithError:NULL];
NSError *error = nil;
GTRemote *remote = [conf.remotes firstObject];
```

由于push操作是需要权限的，所以要配置用户名和密码。**注意这里的key值和clone的时候不一样。**

```
GTCredentialProvider *provider = [GTCredentialProvider providerWithBlock:^GTCredential * _Nullable(GTCredentialType type, NSString * _Nonnull URL, NSString * _Nonnull userName) {
   GTCredential *cre = [GTCredential credentialWithUserName:@"xporter" password:@"12345678" error:NULL];
   return cre;
}];

 NSDictionary *remoteOptions = @{GTRepositoryRemoteOptionsCredentialProvider:provider};
```   

获取分支信息

```
GTBranch *curretntBarch = [_repo currentBranchWithError:&error];
```

开始向远端推送

```
BOOL r = [_repo pushBranch:curretntBarch toRemote:remote withOptions:pushOptions error:&error progress:^(unsigned int current, unsigned int total, size_t bytes, BOOL * _Nonnull stop) {
    NSLog(@"%d/%d",current,total);
}];
```

如果没有报错的话就是推送成功了，可以到远端仓库进行验证。

## pull
要同步远端的指定分支的信息，就需要用到pull方法。先获取需要更新的分支

```
GTBranch *brach = [_repo currentBranchWithError:NULL];
```

pull 也是需要权限信息的，要设置用户信息

```
GTCredentialProvider *provider = [GTCredentialProvider providerWithBlock:^GTCredential * _Nullable(GTCredentialType type, NSString * _Nonnull URL, NSString * _Nonnull userName) {
   GTCredential *cre = [GTCredential credentialWithUserName:@"xporter" password:@"12345678" error:NULL];
   return cre;
}];

NSDictionary *remoteOptions = @{GTRepositoryRemoteOptionsCredentialProvider:provider};
```

获取远端分支信息

```
GTConfiguration *conf = [_repo configurationWithError:NULL];
NSError *error = nil;
GTRemote *remote = [conf.remotes firstObject];
```

拉取远端信息

```
BOOL r = [_repo pullBranch:brach fromRemote:remote withOptions:pullOptions error:&error progress:^(const git_transfer_progress * _Nonnull progress, BOOL * _Nonnull stop) {

}];
```
