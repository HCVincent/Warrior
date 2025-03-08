# WarriorBaseCharacter 学习笔记

## 1. 类的基本结构

`AWarriorBaseCharacter` 是一个基础角色类，它继承自 UE4 的 `ACharacter` 类，并实现了 `IAbilitySystemInterface` 接口。这个类作为游戏中所有角色的基类，提供了基本的能力系统功能。

```cpp
class WARRIOR_API AWarriorBaseCharacter : public ACharacter, public IAbilitySystemInterface
```

## 2. 关键组件

### 2.1 能力系统组件
```cpp
UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "AbilitySystem")
UWarriorAbilitySystemComponent* WarriorAbilitySystemComponent;
```

- 这是一个自定义的能力系统组件，继承自 UE4 的 `UAbilitySystemComponent`
- 在构造函数中创建为子对象
- 提供了角色的所有能力系统功能
- 使用 `VisibleAnywhere` 和 `BlueprintReadOnly` 使其在编辑器中可见且在蓝图中可读

### 2.2 属性集
```cpp
UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "AbilitySystem")
UWarriorAttributeSet* WarriorAttributeSet;
```

- 这是一个自定义的属性集，继承自 UE4 的 `UAttributeSet`
- 在构造函数中创建为子对象
- 存储角色的所有属性数据（如生命值、魔法值等）
- 同样使用 `VisibleAnywhere` 和 `BlueprintReadOnly` 使其在编辑器中可见且在蓝图中可读

## 3. 构造函数详解

```cpp
AWarriorBaseCharacter::AWarriorBaseCharacter()
{
    // 禁用Tick函数以提高性能
    PrimaryActorTick.bCanEverTick = false;
    PrimaryActorTick.bStartWithTickEnabled = false;

    // 禁用网格体接收贴花，可能是为了性能或视觉效果考虑
    GetMesh()->bReceivesDecals = false;

    // 创建能力系统组件
    WarriorAbilitySystemComponent = CreateDefaultSubobject<UWarriorAbilitySystemComponent>(TEXT("WarriorAbilitySystemComponent"));

    // 创建属性集
    WarriorAttributeSet = CreateDefaultSubobject<UWarriorAttributeSet>(TEXT("WarriorAttributeSet"));
}
```

**为什么这样设计？**
- 禁用Tick函数可以提高性能，除非角色需要每帧更新
- 禁用网格体接收贴花可能是为了性能考虑或特定的视觉效果需求
- 在构造函数中创建组件是UE4的标准做法，确保组件在游戏开始前就已初始化

## 4. 接口实现

### 4.1 能力系统接口
```cpp
virtual UAbilitySystemComponent* GetAbilitySystemComponent() const override;
```

这个方法实现了 `IAbilitySystemInterface` 接口，允许其他系统获取角色的能力系统组件。

实现方式：
```cpp
UAbilitySystemComponent* AWarriorBaseCharacter::GetAbilitySystemComponent() const
{
    return GetWarriorAbilitySystemComponent();
}
```

这种实现利用了已定义的内联方法，避免了代码重复，同时保持了一致性。

### 4.2 便捷访问方法
```cpp
FORCEINLINE UWarriorAbilitySystemComponent* GetWarriorAbilitySystemComponent() const { return WarriorAbilitySystemComponent; }

FORCEINLINE UWarriorAttributeSet* GetWarriorAttributeSet() const { return WarriorAttributeSet; };
```

这些内联方法提供了类型安全的方式来访问组件，避免了类型转换。

#### FORCEINLINE 关键字详解

`FORCEINLINE` 是虚幻引擎定义的一个宏，用于强制函数内联。它的作用和含义如下：

1. **定义**：`FORCEINLINE` 是虚幻引擎对标准 C++ `inline` 关键字的扩展，在不同平台上有不同的实现。

2. **作用**：
   - 指示编译器尝试将函数调用直接替换为函数体，而不是生成函数调用指令
   - 消除函数调用的开销（如栈操作、参数传递、返回值处理等）
   - 为编译器提供更多优化机会

3. **与普通 inline 的区别**：
   - 普通的 `inline` 只是对编译器的建议，编译器可以选择忽略
   - `FORCEINLINE` 更强烈地要求编译器进行内联，虽然在某些情况下编译器仍可能忽略
   - 在某些平台上，`FORCEINLINE` 可能会使用额外的编译器特定指令来增加内联的可能性

4. **使用场景**：
   - 适用于简短、频繁调用的函数
   - 特别适合像 getter/setter 这样的简单访问器
   - 在性能关键的代码路径上使用可以减少函数调用开销

5. **注意事项**：
   - 过度使用可能导致代码膨胀，增加指令缓存压力
   - 不适合复杂或很少调用的函数
   - 内联函数的定义通常需要在头文件中可见

在 `WarriorBaseCharacter` 中，`FORCEINLINE` 用于简单的 getter 方法是非常合适的，因为：
- 这些方法非常简短（只有一行代码）
- 它们可能被频繁调用
- 内联可以完全消除函数调用开销
- 不会导致明显的代码膨胀

## 5. 角色控制

```cpp
virtual void PossessedBy(AController* NewController) override;
```

这个方法在角色被控制器占有时调用，用于初始化能力系统。

```cpp
void AWarriorBaseCharacter::PossessedBy(AController* NewController)
{
    Super::PossessedBy(NewController);
    if (WarriorAbilitySystemComponent)
    {
        WarriorAbilitySystemComponent->InitAbilityActorInfo(this, this);
    }
}
```

**为什么这样设计？**
- 在角色被控制器占有时初始化能力系统是一个好时机，因为此时角色已经与控制器关联
- `InitAbilityActorInfo` 方法设置能力系统的所有者和头像，这对于能力系统正常工作是必需的
- 传递 `this, this` 表示角色既是能力系统的所有者也是头像，这在大多数情况下是合适的

## 6. 设计模式分析

1. **组件模式**：通过组合不同的组件（能力系统组件、属性集）来构建角色功能
2. **接口实现**：实现 `IAbilitySystemInterface` 接口使角色能够与能力系统集成
3. **继承**：从 `ACharacter` 继承基本的角色功能，然后扩展它

## 7. 最佳实践

1. **性能优化**：
   - 禁用不必要的Tick函数和贴花接收
   - 使用 `FORCEINLINE` 优化频繁调用的简单方法

2. **组件初始化**：在构造函数中创建和初始化组件

3. **接口实现**：实现必要的接口以与游戏系统集成

4. **安全检查**：在使用组件前检查其有效性

5. **封装**：
   - 提供类型安全的内联方法来访问组件
   - 通过方法而非直接访问成员变量，提高代码的可维护性和扩展性
