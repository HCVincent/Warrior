# C++ 语法解释

## 1. 引用符号 `&`

### 用法：
```cpp
const FString& Msg
```

### 解释：
- `&` 表示**引用**（reference）
- `FString&` 表示这是一个 `FString` 类型的引用
- 使用引用的好处：
  - 避免复制整个对象，提高效率
  - 可以直接修改原始对象（如果函数需要修改它）
- 类似于Java中的对象参数传递（Java中对象总是通过引用传递）

## 2. 解引用符号 `*`

### 用法：
```cpp
*Msg
```

### 解释：
- `*` 是**解引用**操作符
- `Msg` 是一个 `FString` 对象，`*Msg` 获取该对象的内容
- `UE_LOG` 函数需要 `TCHAR*` 类型的参数，而 `*Msg` 将 `FString` 转换为 `TCHAR*`
- 类似于Java中调用对象的 `toString()` 方法

## 3. 对比表

| 符号 | C++ 含义 | Java 类比 |
|------|----------|-----------|
| `&`  | 引用     | 对象参数传递 |
| `*`  | 解引用   | `toString()` |

## 4. 代码示例

```cpp
static void Print(const FString& Msg, const FColor& Color = FColor::MakeRandomColor(), int32 InKey = -1)
{
    if (GEngine) {
        GEngine->AddOnScreenDebugMessage(InKey, 7.f, Color, Msg);
        UE_LOG(LogTemp, Warning, TEXT("%s"), *Msg);
    }
}
```

### 解释：
- `const FString& Msg`：使用引用传递字符串，避免复制
- `*Msg`：将 `FString` 转换为 `TCHAR*` 以供 `UE_LOG` 使用