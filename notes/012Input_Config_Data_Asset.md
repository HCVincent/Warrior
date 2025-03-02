# 输入配置数据资产系统

## 概述
这个系统使用数据资产（Data Asset）来管理游戏的输入配置，将输入动作与游戏标签关联起来，实现了一个灵活且易于维护的输入系统。

## 文件结构

### 头文件 (DataAsset_InputConfig.h)
```cpp
#pragma once

#include "CoreMinimal.h"
#include "Engine/DataAsset.h"
#include "GameplayTagContainer.h"
#include "DataAsset_InputConfig.generated.h"

// ... 其他包含
```

## 核心组件

### 1. 输入动作配置结构体
```cpp
USTRUCT(BlueprintType)
struct FWarriorInputActionConfig
{
    GENERATED_BODY()

public:
    UPROPERTY(EditDefaultsOnly, BlueprintReadOnly, meta = (Categories = "InputTag"))
    FGameplayTag InputTag;

    UPROPERTY(EditDefaultsOnly, BlueprintReadOnly)
    UInputAction* InputAction;
};
```
这个结构体定义了：
- `InputTag`: 用于标识输入动作的GameplayTag
- `InputAction`: 对应的输入动作资源

### 2. 输入配置数据资产类
```cpp
UCLASS()
class WARRIOR_API UDataAsset_InputConfig : public UDataAsset
{
    GENERATED_BODY()

public:
    UPROPERTY(EditDefaultsOnly, BlueprintReadOnly)
    UInputMappingContext* DefaultMappingContext;

    UPROPERTY(EditDefaultsOnly, BlueprintReadOnly, meta = (TitleProperty = "InputTag"))
    TArray<FWarriorInputActionConfig> NativeInputActions;

    UInputAction* FindNativeInputActionByTag(const FGameplayTag& InInputTag);
};
```

主要功能：
- `DefaultMappingContext`: 默认的输入映射上下文
- `NativeInputActions`: 存储输入动作配置的数组
- `FindNativeInputActionByTag`: 通过标签查找对应的输入动作

## 实现细节

### 查找输入动作的实现 (DataAsset_InputConfig.cpp)
```cpp
UInputAction* UDataAsset_InputConfig::FindNativeInputActionByTag(const FGameplayTag& InInputTag)
{
    for (const FWarriorInputActionConfig& InputActionConfig : NativeInputActions)
    {
        if (InputActionConfig.InputTag == InInputTag && InputActionConfig.InputAction)
        {
            return InputActionConfig.InputAction;
        }
    }
    return nullptr;
}
```

功能说明：
- 遍历所有输入动作配置
- 匹配输入标签
- 返回找到的输入动作或nullptr

## 使用方法

### 1. 创建数据资产
1. 在内容浏览器中右键
2. 选择 "DataAsset_InputConfig" 类型
3. 设置默认映射上下文
4. 添加输入动作配置

### 2. 配置输入动作
```cpp
// 示例：配置移动输入
FWarriorInputActionConfig MoveConfig;
MoveConfig.InputTag = WarriorGameplayTags::Input_TAG_Move;
MoveConfig.InputAction = MoveAction;
```

### 3. 在代码中使用
```cpp
// 示例：通过标签获取输入动作
UInputAction* MoveAction = InputConfig->FindNativeInputActionByTag(WarriorGameplayTags::Input_TAG_Move);
if (MoveAction)
{
    // 使用输入动作
}
```

## 优势

1. **数据驱动**
   - 输入配置完全数据化
   - 易于修改和维护
   - 支持蓝图编辑

2. **灵活性**
   - 使用GameplayTags系统
   - 可以动态更改输入映射
   - 支持多个输入配置

3. **可扩展性**
   - 易于添加新的输入动作
   - 可以创建多个配置资产
   - 支持运行时修改

## 最佳实践

1. **命名规范**
   - 使用清晰的标签名称
   - 保持输入动作命名一致性
   - 适当注释配置用途

2. **组织建议**
   - 相关输入动作组织在一起
   - 使用有意义的分类
   - 保持配置结构清晰

3. **调试技巧**
   - 检查标签匹配
   - 验证输入动作是否正确加载
   - 测试输入响应

## 注意事项

- 确保所有InputTag都已正确定义
- 检查InputAction资源的有效性
- 注意输入优先级的设置
- 考虑输入上下文的切换逻辑
