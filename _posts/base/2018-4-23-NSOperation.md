---
title: iOS 基础：NSOperation
categories: [iOS 基础]
---

- TOC
{:toc}

NSOperation是一个抽象类，是OC语言中基于GCD的面向对象的封装。相比GCD来说，面向对象的方式使用起来相对简单，且不用关心线程及线程的生命周期。也提供了一些 GCD 不好实现的功能，比如可以取消在任务处理队列中的任务，添加任务间的依赖关系等等。

### 1、NSOperation
NSOperation 是一个抽象类，真正在使用的时候用到的是其相应的子类 NSInvocationOperation 和 NSInvocationOperation，并且也可以自定义 NSOperation 的子类来实现需要的功能。

```
- (void)operationTest {
    NSOperation *invocationOperation = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(operation) object:nil];
    NSOperation *blockOperation = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"xxxx%@",[NSThread currentThread]);
    }];
    [invocationOperation start];
    [blockOperation start];
}
- (void)operation {
    NSLog(@"-=-=-=-=%@",[NSThread currentThread]);
}
```

在默认情况下，operation 是同步执行的，也就是说在调用它的 start 方法的线程中执行它们的任务。但是从严格意义上来说，在调用 start 方法真正开始执行一个 operation 前，我们应该要做一些防范性的判断，比如检查 operation 的 isReady 状态是否为 YES ，这个取决于它所依赖的 operation 是否已经执行完成；又比如检查 operation 的 isCancelled 状态是否为 YES ，如果是，那么我们就根本不需要再花费不必要的开销去启动它。

NSOperation 之间可以添加相互依赖，但是一定要注意不要A依赖B，然后B又依赖A，这样A和B相互依赖造成都不能得到执行。如果A和B处于不同的操作队列，也是可以设置依赖关系的。

```
[operationB addDependency:operationA];
```

### 2、NSOperationQueue

#### 添加 Operation
operation 处理调用 start 出发任务之外，还可以添加到 OperationQueue 由 OperationQueue 统一调配执行。

```
- (void)operationQueueTest {
    NSOperation *invocationOperation = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(operation) object:nil];
    NSOperation *blockOperation = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"xxxx%@",[NSThread currentThread]);
    }];
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];
    [queue addOperation:blockOperation];
    [queue addOperation:invocationOperation];
}
```

NSOperationQueue 默认是一个并发队列，并且添加到 NSOperationQueue 里面的 Operation 都是异步执行的。

>注意，在将一个 operation 添加到 operation queue 后就不要再修改这个 operation 了。因为 operation 被添加到 operation queue 后随时可能会执行，这个是由系统决定的，所以再修改它的依赖关系或者所包含的数据就很有可能会造成未知的影响。

尽管 NSOperationQueue 类是被设计成用来并发执行 operation 的，但是我们也可以强制一个 operation queue 一次只执行一个 operation 。我们可以通过 setMaxConcurrentoperationCount: 方法来设置一个 operation queue 最大可并发的 operation 数，因此将这个值设置成 1 就可以实现让 operation queue 一次只执行一个 operation 的目的。

#### 取消 Operation

从原则上来说，一旦一个 operation 被添加到 operation queue 后，这个 operation 的所有权就属于这个 operation queue 了，并且不能够被移除。唯一从 operation queue 中出队一个 operation 的方式就是调用它的 cancel 方法取消这个 operation ，或者直接调用 operation queue 的 cancelAllOperations 方法取消这个 operation queue 中所有的 operation 。

#### 等待 Operation

一般来说，为了让我们的应用拥有最佳的性能，我们应该尽可能地异步执行所有的 operation ，从而让我们的应用在执行这些异步 operation 的同时还能够快速地响应用户事件。当然，我们也可以调用 NSOperation 类的 waitUntilFinished 方法来阻塞当前线程，直到这个 operation 执行完成。虽然这种方式可以让我们非常方便地处理 operation 的执行结果，但是却给我们的应用引入了更多的串行，限制了应用的并发性，从而降低了我们应用的响应性。

注意，我们应该要坚决避免在主线程中去同步等待一个 operation 的执行结果，阻塞的方式只应该用在辅助线程或其他 operation 中。因为阻塞主线程会大大地降低我们应用的响应性，带来非常差的用户体验。

除了等待一个单独的 operation 执行完成外，我们也可以通过调用 NSOperationQueue 的 waitUntilAlloperationsAreFinished 方法来等待 operation queue 中的所有 operation 执行完成。有一点需要特别注意的是，当我们在等待一个 operation queue 中的所有 operation 执行完成时，其他的线程仍然可以向这个 operation queue 中添加 operation ，从而延长我们的等待时间。

#### 暂停和恢复 Operation Queue

如果我们想要暂停和恢复执行 operation queue 中的 operation ，可以通过调用 operation queue 的 setSuspended: 方法来实现这个目的。不过需要注意的是，暂停执行 operation queue 并不能使正在执行的 operation 暂停执行，而只是简单地暂停调度新的 operation 。另外，我们并不能单独地暂停执行一个 operation ，除非直接 cancel 掉。


---
参考链接：[iOS 并发编程之 Operation Queues](http://blog.leichunfeng.com/blog/2015/07/29/ios-concurrency-programming-operation-queues/)
