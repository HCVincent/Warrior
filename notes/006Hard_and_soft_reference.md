# Hard/Soft Reference in UE5

## What are they

### Hard Reference
- 直接引用对象的指针
- 通过常规的UObject*、TObjectPtr或UPROPERTY()声明
- 被引用的对象会被保持在内存中，不会被垃圾回收
- TObjectPtr是UE5引入的智能指针类型，用于替代传统的UObject*
  - 提供更好的内存安全性
  - 支持空指针检查
  - 与垃圾回收系统更好地集成
  - 在序列化时更高效

### Soft Reference
- 通过名称或路径间接引用对象
- 使用TSoftObjectPtr或UPROPERTY(meta=(AllowedClasses="..."))声明
- 不会阻止被引用对象被垃圾回收
- 可以按需加载和卸载

## Pros and Cons

### Hard Reference
优点：
- 访问速度快，直接通过指针访问
- 确保对象始终可用
- 使用简单直观
- TObjectPtr提供了额外的安全性和功能

缺点：
- 增加内存占用
- 可能导致循环引用
- 不利于内存管理和资源优化

### Soft Reference
优点：
- 更灵活的内存管理
- 支持动态加载/卸载
- 避免循环引用问题
- 适合大型资源管理

缺点：
- 访问对象需要额外的解析步骤
- 可能出现引用失效的情况
- 需要额外的错误处理机制

## When to Use Them

### 使用Hard Reference的场景
- 频繁访问且必须保证可用的核心对象
- 游戏运行时始终需要的资源
- 关卡中的永久性Actor
- 玩家控制器、游戏模式等基础组件
- 当需要TObjectPtr提供的额外安全性和功能时

### 使用Soft Reference的场景
- 大型资源（如贴图、模型、音频）
- 需要动态加载/卸载的内容
- 可选或延迟加载的功能模块
- 跨关卡引用的对象
- 资源包管理系统
- 可能在运行时被替换或修改的资源

## 最佳实践
1. 合理评估对象的生命周期
2. 考虑内存占用和加载性能的平衡
3. 对于Soft Reference，实现proper错误处理
4. 使用异步加载机制处理大型资源
5. 定期检查和清理无效引用
6. 在UE5中优先使用TObjectPtr替代传统的UObject*
7. 注意TObjectPtr的序列化行为，确保数据一致性
8. 在跨模块引用时，考虑使用TObjectPtr提高安全性
