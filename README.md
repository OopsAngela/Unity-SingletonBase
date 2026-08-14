# MonoSingletonBase

一个 Unity 下的 **MonoBehaviour 泛型单例基类**，用于为场景中唯一存在的脚本自动创建并管理实例。

## 特性

- **泛型基类**：通过 `MonoSingletonBase<T>` 约束，子类无需重复编写单例逻辑。
- **按需加载**：首次访问 `Instance` 时才创建/查找实例，避免 Awake 阶段相互引用导致的 `Null` 异常。
- **自动建对象**：若场景中未挂载该脚本，会自动创建一个空 GameObject 并挂上组件。
- **统一初始化入口**：提供 `Init()` 虚方法，子类可重写用于初始化，保证只执行一次。

## 文件

| 文件 | 说明 |
|---|---|
| `MonoSingletonBase.cs` | 单例基类，命名空间 `Common` |

## 使用方法

1. 将脚本放入项目的 `Common` 命名空间（已默认包含）。
2. 让你的脚本继承该类，并将自身作为泛型参数：

```csharp
using Common;
using UnityEngine;

public class GameManager : MonoSingletonBase<GameManager>
{
    protected override void Init()
    {
        // 在这里做初始化（替代 Awake，避免 Null 问题）
        Debug.Log("GameManager 初始化完成");
    }

    public void DoSomething()
    {
        Debug.Log("做点事情");
    }
}
```

3. 任意地方通过 `Instance` 访问：

```csharp
GameManager.Instance.DoSomething();
```

## 工作原理简述

- `Instance` 的 `get` 访问器实现**按需加载**：已存在则直接返回；不存在则先 `FindObjectOfType` 查找，找不到就自动 `new GameObject().AddComponent<T>()`。
- `Awake()` 中把 `instance` 指向自身并调用 `Init()`，确保已挂载在场景中的脚本也能正确初始化。
- 子类如需初始化逻辑，重写 `protected virtual void Init()` 即可（可不用）。

## 注意

- 该单例基于场景中的 GameObject，**不会跨场景持久化**。如需跨场景保留，请在子类 `Awake` 中调用 `DontDestroyOnLoad(gameObject)`。
- 多个同类型组件挂在场景里时，以第一个被 `Awake` 初始化的实例为准。

## 许可证

> 请在此填写本项目适用的许可证（如基于 EPL-1.0 的二次开发，请保留原许可证声明）。
