# 处理错误（Dealing with Errors）

如果你来自其他平台和语言，你可能习惯于处理大多数错误处理的异常。当你使用Objective-C写代码，异常仅仅用于程序员错误，像数组访问越界或者非法的方法参数。这些都是你应该在应用发布前在测试中发现并修改的问题。

所有的错误都是用NSError类的实例表示。这个章节给出使用NSError对象的一个简短的介绍，包括如何使用可能失败并返回错误的框架方法。进一步的信息，请查看[_Error Handling Programming Guide_](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ErrorHandlingCocoa/ErrorHandling/ErrorHandling.html#//apple_ref/doc/uid/TP40001806)。

## 为大部分错误使用NSError

可悲的是，为每一个可能的问题制定应急计划和解决方案是不可能的。相反，你必须计划错误，并知道如何处理它们以提供最佳的用户体验。

### 某些委托方法会警告你错误

如果你为了使用一个执行特定任务的框架类正在实现一个委托对象，像从远程服务器下载信息，你通常会发现你需要实现至少一个错误相关的方法。例如，_NSURLConnectionDelegate_协议，包含一个_connection:didFailWithError:_方法：

```
- (void)connection:(NSURLConnection *)connection didFailWithError:(NSError *)error;
```

如果一个错误产生，这个委托方法会被调用以提供一个描述问题的NSError对象。

NSError对象包括数字错误码，域和说明，还有打包在用户信息字典中的其他相关信息。

Cocoa和Cocoa Touch错误分以不同的域做区分，而不是要求每一个可能的错误都有一个唯一的数字码。例如，如果_NSURLConnection_中发生一个错误，上面的_connection:didFailWithError:_方法将从_NSURLErrorDomain_中提供错误。

错误对象也包括一个本地化的描述，比如“找不到指定主机名的服务器”。

### 一些方法通过引用传递错误

一些Cocoa和Cocoa Touch API通过引用传回错误。例如，你可能会决定使用NSData方法_writeToURL:options:error:_写入的方式，将从服务器接收的数据存储到磁盘。这个方法的最后一个参数是一个NSError指针的引用：

```
- (BOOL)writeToURL:(NSURL *)aURL
           options:(NSDataWritingOptions)mask
             error:(NSError **)errorPtr;
```

调用这个方法之前，你需要创建一个合适的指针以传递它的地址：

```
    NSError *anyError;
    BOOL success = [receivedData writeToURL:someLocalFileURL
                                    options:0
                                      error:&anyError];
    if (!success) {
        NSLog(@"Write failed with error: %@", anyError);
        // present error to user
    }
```

如果出错，_writeToURL:..._方法将返回NO，并更新你的_anyError_指针指向一个描述问题的错误对象。

处理通过引用传递的错误时，重要的是要像上面一样测试方法的返回值看是否有错误发生。不要仅仅去看是否错误指针被设置指向了一个错误。

> 提示：如果你不关心错误对象，error:参数传NULL就好。

### 如果可能就恢复，或者向用户显示错误



### 生成你自己的错误

创建你自己的NSError对象，你需要定义你自己的错误域，应该是这样的形式：

```
com.companyName.appOrFrameworkName.ErrorDomain
```

你也需要为你的域中产生的每一个错误选一个唯一的错误码，还有一个需要存储在用户信息字典中的合适的描述，如下所示：

```
    NSString *domain = @"com.MyCompany.MyApplication.ErrorDomain";
    NSString *desc = NSLocalizedString(@"Unable to…", @"");
    NSDictionary *userInfo = @{ NSLocalizedDescriptionKey : desc };
 
    NSError *error = [NSError errorWithDomain:domain
                                         code:-101
                                     userInfo:userInfo];
```

这个例子使用_NSLocalizedString_函数从_Localizable.strings_文件中查询错误描述的本地化版本，如本地化字符串资源中所描述。

如果你需要像前面描述的一样通过引用传回一个错误，你的方法签名需要包含一个指向指向NSError对象的指针的参数。你也需要使用返回值表明成功或失败，如下所示：

```
- (BOOL)doSomethingThatMayGenerateAnError:(NSError **)errorPtr;
```

如果一个错误产生，返回表示失败的NO之前，你需要先检查为错误参数提供的是否是一个非空的指针，然后尝试街引用它以设置错误，如下所示：

```
- (BOOL)doSomethingThatMayGenerateAnError:(NSError **)errorPtr {
    ...
    // error occurred
    if (errorPtr) {
        *errorPtr = [NSError errorWithDomain:...
                                        code:...
                                    userInfo:...];
    }
    return NO;
}
```



## 异常被用于程序员错误

Objective-C支持与其他编程语言非常相似的异常，跟Java或C++的语法很像。像NSError一样，异常在Cocoa和Cocoa Touch中也是对象，用NSException类的对象表示。

如果你需要写一段可能会引发异常的代码，你可以把代码放在try-catch块中：

```
    @try {
        // do something that might throw an exception
    }
    @catch (NSException *exception) {
        // deal with the exception
    }
    @finally {
        // optional block of clean-up code
        // executed whether or not an exception occurred
    }
```

如果@try块中的代码抛出了一个异常，它将被@catch块捕获以便于你可以处理它。例如，若干你在使用底层的C++库时使用异常做错误处理，你可以捕获异常然后创建合适的NSError对象展示给用户。

如果一个异常抛出且没被捕获，默认的未捕获异常处理程序会在控制台打印日志并终止应用程序。

你不应该使用_try-catch_块代替Objective-C方法的标准编程检查。例如，_NSArray_的情况，你应该总是在获取一个给定下标的对象之前检查数组的_count_来决定元素的个数。如果你发起一个越界请求，_objectAtIndex:_方法会抛出一个异常，这样你就可以在开发周期的早期发现代码中的bug，你应该避免在发布给用户的应用程序中抛出异常。

更多关于Objective-C应用程序的异常信息，请看[_Exception Programming Topics_](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Exceptions/Exceptions.html#//apple_ref/doc/uid/10000012i)。

