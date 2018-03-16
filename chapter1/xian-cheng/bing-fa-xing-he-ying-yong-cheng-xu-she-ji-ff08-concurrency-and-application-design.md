# 并发性和应用程序设计（Concurrency and Application Design）

在计算的早期，单位时间计算机可以执行的最大工作量取决于CPU的时钟速度。但是随着技术进步以及处理器的设计变得更紧凑，热量和其他的物理限制开始处理器的最大时钟速度。所以，芯片制造商寻找其他防止来提升他们的芯片的总的性能。他们找到的解决方案是增加每个芯片上的处理器核心个数。通过提升核心个数，一个单独的芯片每秒可以执行更多的命令，而不用提升CPU速度或改变芯片的尺寸或热特性。唯一的问题是如何利用额外的核心。

## 从线程迁移（The Move Away from Threads）

### 调度队列（Dispatch Queues）

### 调度来源（Dispatch Sources）

### 操作队列（Operation Queues）

## 异步设计技术（Asynchronous Design Techniques）

定义应用程序的预期行为（Define Your Application’s Expected Behavior）

分解可执行的工作单元（Factor Out Executable Units of Work）

确定你需要的队列（Identify the Queues You Need）

提高效率的指示（Tips for Improving Efficiency）

## 性能意义（Performance Implications）

## 并发和其他技术（Concurrency and Other Technologies）

OpenCL（_Open Computing Language_）和并发（OpenCL and Concurrency）

什么时候使用线程（When to Use Threads）

