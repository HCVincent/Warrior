# Native Gameplay Tags 系统

## 概述
GameplayTags 是虚幻引擎中用于标记和分类游戏元素的轻量级系统。这个系统允许我们使用层级化的字符串标签来标识和组织游戏功能。

## 文件结构

### 头文件 (WarriorGameplayTags.h)
```cpp
#pragma once
#include "NativeGameplayTags.h"

namespace WarriorGameplayTags
{
    WARRIOR_API UE_DECLARE_GAMEPLAY_TAG_EXTERN(Input_TAG_Move);
    WARRIOR_API UE_DECLARE_GAMEPLAY_TAG_EXTERN(Input_TAG_Look);
}
```

### 实现文件 (WarriorGameplayTags.cpp)
```cpp
#include "WarriorGameplayTags.h"

namespace WarriorGameplayTags
{
    UE_DEFINE_GAMEPLAY_TAG(Input_TAG_Move, "Input.Move");
    UE_DEFINE_GAMEPLAY_TAG(Input_TAG_Look, "Input.Look");
}
```

## 关键概念解释

### 1. 命名空间使用
```cpp
namespace WarriorGameplayTags
```
- 使用命名空间避免标签名称冲突
- 将相关的游戏标签组织在一起

### 2. 标签声明宏
```cpp
UE_DECLARE_GAMEPLAY_TAG_EXTERN(Input_TAG_Move);
```
- `UE_DECLARE_GAMEPLAY_TAG_EXTERN`: 在头文件中声明一个游戏标签
- `WARRIOR_API`: 确保标签可以在模块外部使用

### 3. 标签定义宏
```cpp
UE_DEFINE_GAMEPLAY_TAG(Input_TAG_Move, "Input.Move");
```
- 第一个参数: 标签变量名
- 第二个参数: 实际的标签字符串
- 使用点号(.)创建层级结构

## 标签结构
当前定义的标签：
- `Input.Move`: 用于移动输入相关的功能
- `Input.Look`: 用于视角控制相关的功能

## 实际应用

### 1. 如何使用这些标签
```cpp
// 在代码中使用标签
if (GameplayTag == WarriorGameplayTags::Input_TAG_Move)
{
    // 处理移动输入
}
```

### 2. 标签的优势
- 类型安全：编译时检查
- 性能高效：标签在运行时是FName
- 易于维护：集中管理所有标签
- 支持层级结构：可以用点号创建标签层级

### 3. 常见用途
- 输入动作的分类
- 游戏状态的标记
- 能力系统的标签
- 动画状态的标识

## 最佳实践

1. **命名规范**
   - 使用清晰的前缀（如 Input_TAG_）
   - 采用层级结构（使用点号分隔）
   - 保持命名一致性

2. **组织方式**
   - 相关标签放在同一命名空间
   - 使用注释说明标签用途
   - 保持标签结构简单明了

3. **维护建议**
   - 定期检查未使用的标签
   - 记录标签的用途和关系
   - 避免创建过于复杂的标签层级

## 如何添加新的标签

1. 在头文件中声明：
```cpp
WARRIOR_API UE_DECLARE_GAMEPLAY_TAG_EXTERN(New_TAG_Name);
```

2. 在cpp文件中定义：
```cpp
UE_DEFINE_GAMEPLAY_TAG(New_TAG_Name, "Category.Subcategory.Name");
```

## 调试技巧

- 使用 `PrintGameplayTags` 控制台命令查看所有已注册的标签
- 在编辑器中使用 GameplayDebugger 查看运行时的标签状态
- 设置断点检查标签的匹配情况
