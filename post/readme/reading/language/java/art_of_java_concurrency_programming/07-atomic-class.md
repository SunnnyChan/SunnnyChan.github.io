# Java 中的 13 个原子操作类
```md
通常 我们会使用 synchronized 保证多线程不会同时更新共享变量。

从Java 5 开始，提供 Atomic 包中的原子操作类提供了一种用法简单、性能高效、
线程安全地更新一个变量的方式。

Atomic 包 提供基本类型、数组、引用、属性4中类型的原子更新方式。
包中的原子操作类基本都是使用 Unsafe 实现的 包装类。
```
## 原子更新基本类型类
```md
AtomicBoolean
AtomicInteger
AtomicLong
```

## 原子更新数组
```md
AtomicIntegerArray
AtomicLongArray
AtomicReferenceArray
```

## 原子更新引用类型
```md
AtomicReference
AtomicReferenceFieldUpdater
AtomicMarkableReference
```

## 原子更新字段类
```md
AtomicIntegerFieldUpdater
AtomicLongFieldUpdater
AtomicStampedReference
```
