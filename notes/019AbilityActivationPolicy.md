# WarriorGameplayAbility 学习笔记

## 1. 类的基本结构

`UWarriorGameplayAbility` 是一个自定义的游戏能力类，继承自虚幻引擎的 `UGameplayAbility` 类。这个类扩展了基础的游戏能力系统，添加了自定义的激活策略。

```cpp
class WARRIOR_API UWarriorGameplayAbility : public UGameplayAbility
```

## 2. 能力激活策略

### 2.1 激活策略枚举

```cpp
UENUM(BlueprintType)
enum class EWarriorAbilityActivationPolicy : uint8
{
    OnTriggered,  // 当触发时激活
    OnGiven       // 当授予时激活
};
```

这个枚举定义了两种能力激活策略：
- **OnTriggered**：默认策略，能力需要被显式触发才会激活
- **OnGiven**：能力在被授予给角色时自动激活

### 2.2 激活策略属性

```cpp
UPROPERTY(EditDefaultsOnly, Category = "WarriorAbility")
EWarriorAbilityActivationPolicy AbilityActivationPolicy = EWarriorAbilityActivationPolicy::OnTriggered;
```

- 使用 `EditDefaultsOnly` 使其在蓝图类默认值中可编辑
- 默认值设为 `OnTriggered`，遵循标准的能力激活流程
- 归类为 "WarriorAbility" 以便在编辑器中组织属性

## 3. 重写的关键方法

### 3.1 OnGiveAbility

```cpp
virtual void OnGiveAbility(const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilitySpec& Spec) override;
```

这个方法在能力被授予给角色时调用。

```cpp
void UWarriorGameplayAbility::OnGiveAbility(const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilitySpec& Spec)
{
    Super::OnGiveAbility(ActorInfo, Spec);
    if (AbilityActivationPolicy == EWarriorAbilityActivationPolicy::OnGiven)
    {
        if (ActorInfo && !Spec.IsActive())
        {
            ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle);
        }
    }
}
```

**实现分析：**
1. 首先调用父类的 `OnGiveAbility` 方法，确保基础功能正常执行
2. 检查能力激活策略是否为 `OnGiven`
3. 如果是，且 ActorInfo 有效且能力尚未激活，则尝试激活该能力
4. 使用 `TryActivateAbility` 方法，传入能力的句柄来激活它

### 3.2 EndAbility

```cpp
virtual void EndAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, bool bReplicateEndAbility, bool bWasCancelled) override;
```

这个方法在能力结束时调用。

```cpp
void UWarriorGameplayAbility::EndAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, bool bReplicateEndAbility, bool bWasCancelled)
{
    Super::EndAbility(Handle, ActorInfo, ActivationInfo, bReplicateEndAbility, bWasCancelled);

    if (AbilityActivationPolicy == EWarriorAbilityActivationPolicy::OnGiven)
    {
        if (ActorInfo)
        {
            ActorInfo->AbilitySystemComponent->ClearAbility(Handle);
        }
    }
}
```

**实现分析：**
1. 首先调用父类的 `EndAbility` 方法，确保基础功能正常执行
2. 检查能力激活策略是否为 `OnGiven`
3. 如果是，且 ActorInfo 有效，则清除该能力
4. 使用 `ClearAbility` 方法，传入能力的句柄来移除它

## 4. 设计模式分析

### 4.1 模板方法模式
- 通过重写基类的关键方法（`OnGiveAbility` 和 `EndAbility`）来扩展基础功能
- 保留了对基类方法的调用（`Super::OnGiveAbility` 和 `Super::EndAbility`）

### 4.2 策略模式
- 使用枚举 `EWarriorAbilityActivationPolicy` 定义不同的激活策略
- 根据选择的策略执行不同的行为

## 5. 使用场景

### 5.1 OnTriggered 策略
- 适用于需要玩家主动触发的能力，如攻击、技能等
- 这是默认行为，与标准的 GameplayAbility 行为一致

### 5.2 OnGiven 策略
- 适用于被动能力，如光环、持续效果等
- 能力在被授予时自动激活，在结束时自动移除
- 无需玩家干预，简化了被动能力的实现

## 6. 最佳实践

1. **继承与扩展**：通过继承基类并重写关键方法来扩展功能，而不是完全重写

2. **调用基类方法**：在重写的方法中调用 `Super::` 方法，确保基础功能正常执行

3. **参数检查**：在使用指针前检查其有效性（如 `if (ActorInfo)`），避免空指针异常

4. **枚举使用**：使用枚举定义清晰的选项，而不是布尔值或魔法数字

5. **默认值设置**：为属性设置合理的默认值，减少配置错误

6. **编辑器支持**：使用适当的 UPROPERTY 标记，提供良好的编辑器支持
