# 自定义增强输入组件

## 概述
`UWarriorInputComponent` 是一个自定义的增强输入组件，继承自 `UEnhancedInputComponent`。它简化了输入绑定过程，通过GameplayTag系统实现了更灵活的输入处理机制。

## 文件结构

### 头文件 (WarriorInputComponent.h)
```cpp
#pragma once

#include "CoreMinimal.h"
#include "EnhancedInputComponent.h"
#include "DataAssets/Input/DataAsset_InputConfig.h"
#include "WarriorInputComponent.generated.h"

UCLASS()
class WARRIOR_API UWarriorInputComponent : public UEnhancedInputComponent
{
    GENERATED_BODY()
    
public:
    template<class UserObject, typename CallableType>
    void BindNativeAction(const UDataAsset_InputConfig* InInputConfig, 
                         const FGameplayTag& InInputTag, 
                         ETriggerEvent TriggerEvent, 
                         UserObject* ContextObject, 
                         CallableType Func);
};
```

## 核心功能

### 1. BindNativeAction 模板函数
```cpp
template<class UserObject, typename CallableType>
inline void UWarriorInputComponent::BindNativeAction(
    const UDataAsset_InputConfig* InInputConfig,
    const FGameplayTag& InInputTag,
    ETriggerEvent TriggerEvent,
    UserObject* ContextObject,
    CallableType Func)
{
    checkf(InInputConfig, TEXT("Input config data asset is null, can not proceed with binding"));
    if(UInputAction* FoundAction = InInputConfig->FindNativeInputActionByTag(InInputTag))
    {
        BindAction(FoundAction, TriggerEvent, ContextObject, Func);
    }
}
```

功能说明：
- **参数解析**：
  - `InInputConfig`: 输入配置数据资产
  - `InInputTag`: 用于识别输入动作的GameplayTag
  - `TriggerEvent`: 触发事件类型
  - `ContextObject`: 接收回调的对象
  - `Func`: 回调函数

- **安全检查**：
  - 使用 `checkf` 确保输入配置有效
  - 通过标签查找对应的输入动作

- **绑定过程**：
  - 找到输入动作后调用基类的 `BindAction`
  - 自动处理输入动作的绑定

## 使用方法

### 1. 基本使用示例
```cpp
// 在角色类中
void AYourCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
    Super::SetupPlayerInputComponent(PlayerInputComponent);
    
    if (UWarriorInputComponent* WarriorInputComponent = CastChecked<UWarriorInputComponent>(PlayerInputComponent))
    {
        // 绑定移动输入
        WarriorInputComponent->BindNativeAction(
            InputConfig,                              // 输入配置
            WarriorGameplayTags::Input_TAG_Move,     // 输入标签
            ETriggerEvent::Triggered,                 // 触发事件
            this,                                     // 目标对象
            &AYourCharacter::Move                    // 回调函数
        );
    }
}
```

### 2. 配置项目使用
1. 在项目设置中设置默认输入组件：
```cpp
// 在项目设置中
DefaultInputComponentClass=UWarriorInputComponent::StaticClass()
```

## 优势

1. **简化绑定过程**
   - 统一的绑定接口
   - 通过标签系统管理输入
   - 减少重复代码

2. **类型安全**
   - 模板函数确保类型安全
   - 编译时检查参数类型
   - 运行时验证输入配置

3. **灵活性**
   - 支持任意类型的回调函数
   - 可以动态更改输入绑定
   - 与数据资产系统集成

## 最佳实践

1. **组织输入绑定**
   - 集中管理输入标签
   - 使用明确的命名约定
   - 保持绑定逻辑清晰

2. **错误处理**
   - 检查输入配置有效性
   - 验证输入动作存在
   - 处理绑定失败情况

3. **性能考虑**
   - 避免频繁绑定/解绑
   - 合理使用触发事件
   - 注意回调函数的性能

## 调试技巧

1. **常见问题排查**
   - 检查输入配置是否正确加载
   - 验证标签是否匹配
   - 确认回调函数是否被调用

2. **调试工具**
   - 使用日志输出验证绑定
   - 设置断点检查执行流程
   - 监视输入事件触发

## 扩展建议

1. **可能的扩展方向**
   - 添加输入优先级管理
   - 实现输入状态记录
   - 支持输入组合操作

2. **自定义功能**
   - 添加输入验证逻辑
   - 实现输入队列系统
   - 创建输入调试工具

## 注意事项

- 确保正确设置项目的默认输入组件类
- 验证输入配置数据资产的完整性
- 注意输入绑定的生命周期管理
- 考虑多玩家场景下的输入处理
