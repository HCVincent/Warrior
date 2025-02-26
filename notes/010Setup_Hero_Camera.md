# 英雄角色和相机系统设置

本文档记录了虚幻引擎中英雄角色（Hero Character）的基础设置，包括碰撞体积、相机系统和移动特性等配置。

## 主要组件设置

### 1. 胶囊体碰撞设置
```cpp
GetCapsuleComponent()->InitCapsuleSize(42.f, 96.0f);
```
- 设置角色的碰撞体积
- 半径：42 虚幻单位
- 高度：96 虚幻单位

### 2. 角色旋转控制
```cpp
bUseControllerRotationPitch = false;
bUseControllerRotationYaw = false;
bUseControllerRotationRoll = false;
```
- 禁用角色随控制器的自动旋转
- Pitch（俯仰）：禁用
- Yaw（偏航）：禁用
- Roll（滚动）：禁用

### 3. 相机系统
#### 弹簧臂组件（CameraBoom）
```cpp
CameraBoom = CreateDefaultSubobject<USpringArmComponent>(TEXT("CameraBoom"));
CameraBoom->SetupAttachment(GetRootComponent());
CameraBoom->TargetArmLength = 200.0f;
CameraBoom->SocketOffset = FVector(0.0f, 55.0f, 65.0f);
CameraBoom->bUsePawnControlRotation = true;
```
- 用于控制相机和角色之间的距离和位置
- 臂长：200 虚幻单位
- 插槽偏移：(0, 55, 65)
- 启用Pawn控制旋转

#### 跟随相机（FollowCamera）
```cpp
FollowCamera = CreateDefaultSubobject<UCameraComponent>(TEXT("FollowCamera"));
FollowCamera->SetupAttachment(CameraBoom, USpringArmComponent::SocketName);
FollowCamera->bUsePawnControlRotation = false;
```
- 附加到弹簧臂末端
- 禁用相机的Pawn控制旋转

### 4. 移动特性设置
```cpp
GetCharacterMovement()->bOrientRotationToMovement = true;
GetCharacterMovement()->RotationRate = FRotator(0.0f, 500.0f, 0.0f);
GetCharacterMovement()->MaxWalkSpeed = 400.0f;
GetCharacterMovement()->BrakingDecelerationWalking = 2000.0f;
```
- 启用移动时的自动旋转
- 旋转速率：(0, 500, 0)
- 最大行走速度：400
- 行走制动减速度：2000

## 如何查找这些功能

1. **虚幻引擎文档**
   - 访问 [Unreal Engine Documentation](https://docs.unrealengine.com/)
   - 查看Character类的详细文档

2. **虚幻引擎编辑器**
   - 使用编辑器的蓝图系统
   - 利用自动补全功能
   - 右键函数名查看定义和文档

3. **源代码参考**
   - 查看引擎源码（需要源码版本）
   - 路径：`[UE安装目录]/Engine/Source/Runtime/`

4. **相关头文件**
   - CapsuleComponent.h
   - SpringArmComponent.h
   - CameraComponent.h
   - CharacterMovementComponent.h

Q:但是我不可能看完整个api文档再去开发吧，开发的时候我怎么知道有这么一个api可以被我使用呢

## 如何在开发中快速发现需要的API

### 1. 使用虚幻编辑器的可视化界面
- 在编辑器中选中Actor或Component
- 查看Details面板中的所有可配置选项
- 记住感兴趣的属性名称，这些通常直接对应到代码中的变量名

### 2. 使用IDE的智能提示
- 输入对象变量名后输入 `->`
- IDE会自动列出所有可用的函数和属性
- 通过函数名称可以大致猜测其功能
- Visual Studio/Rider等IDE还会显示简单的函数说明

### 3. 参考模板项目
- 创建UE模板项目（如Third Person模板）
- 查看模板项目中的代码实现
- 这些模板包含了最常用的功能实现方式

### 4. 使用蓝图作为参考
1. 先在蓝图中实现功能
   - 蓝图节点的搜索功能非常强大
   - 可以用关键词快速找到需要的功能
2. 找到对应的C++实现方式
   - 右键蓝图节点，选择"复制节点引用"
   - 这会显示对应的C++函数名

### 5. 社区资源
- UE论坛的示例代码
- YouTube教程
- GitHub上的开源项目
- AnswerHub上的问答

### 6. 查看相关类的头文件
- 找到基类头文件（如Character.h）
- 查看类中声明的属性和方法
- 头文件通常包含详细的注释说明

记住：不需要记住所有API，知道如何找到它们更重要。随着开发经验增加，常用的API会自然而然地记住。

## 注意事项

- 这些设置适用于第三人称游戏角色
- 所有数值都可以根据具体需求调整
- 可以在编辑器中实时调整这些参数来测试效果
