---
title: Objective-C 中的 Block
date: 2016-07-20 14:29:26
category: iOS
tag: 开发

---
Block 是什么、有什么用。我不解释。
我主要说两点：
1. 不同使用场景的书写格式。
2. Block 使用的注意事项。
<!--more-->
## 书写格式
作为一个类型定义

```
typedef returnType (^TypeName)(parameterTypes);
TypeName blockName = ^returnType(parameters) {...};
```

作为一个属性

```
@property (nonatomic, copy, nullability) returnType (^blockName)(parameterTypes);
```

作为一个方法参数

```
- (void)someMethodThatTakesABlock:(returnType (^nullability)(parameterTypes))blockName;
```

作为一个局部变量

```
returnType (^blockName)(parameterTypes) = ^returnType(parameters) {...};
```

作为一个参数去调用一个方法

```
[someObject someMethodThatTakesABlock:^returnType (parameters) {...}];
```

## 注意事项

#### Block 对变量的捕获规则：

- 静态存储区的变量（例如全局变量、方法中的static变量）：**引用，可修改**。
- Block 接受的参数：**传值，可修改** ，和一般函数的参数相同。
- 栈变量（被捕获的上下文变量）：**const，不可修改**。当 block 被copy 后，block 会对 id 类型的变量产生强引用。每次执行 block 时，捕获到的变量都是最初的值。
- 栈变量（有==__block==前缀）：**引用，可以修改**。若是 id 类型则不会被 block retain，必须手动处理其内存管理。若该类型是C类型变量，block 被 copy 到 heap 后，该值也会被挪动到 heap。

#### 对 __block 和 __weak 的理解

- 如果修改（非指针）局部变量，需要加 __block

```
 __block int count = 10;
 int (^myNumberBlock)(int) = ^(int num) {
         count ++;//这样就可以了
         return num * count;
     };
```

- 局部变量是数组或指针的时候只复制这个指针，两个指针指向同一个地址，block 只修改指针上的内容。如：

```
    NSMutableArray *numbersArray = [NSMutableArray arrayWithObjects:@"8",@"9",@"10",nil];
    NSMutableArray *numbersArrayCount = [NSMutableArray arrayWithCapacity:1];
    [numbersArray enumerateObjectsWithOptions:NSEnumerationConcurrent usingBlock: ^(id obj,NSUInteger idx, BOOL *stop){
        [numbersArrayCount addObject:[NSNumber numberWithUnsignedInteger:[obj length]]];
    }];
    NSLog(@"numbersArrayCount::  %@",numbersArrayCount);
```
当变量是一个指针的时候，block 里只是复制了一份这个指针，两个指针指向同一个地址。所以，在 block 里面对指针指向内容做的修改，在block外面一样生效。这种情况，变量前不需要加 __block的

- block 中访问 self 的实例变量时，self 被 retain，block 也是一个强引用，会导致循环引用。

```
__weak __typeof(self)weakSelf = self; 
//等同于 __weak ClassName *weakSelf = self;
```
用 __weak 是弱引用，当 self 释放时，weakSelf 已经等于 nil。





