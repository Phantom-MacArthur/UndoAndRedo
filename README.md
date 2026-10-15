# UndoAndRedo

Slay the Spire 2 的单人战斗撤销与重做 mod。当前 mod 版本：**2.2.2**。

## 2.2.2 更新

- 修复在出牌动作尚未完成时撤销，快速路径回退到重放后因战斗大厅初始化参数不匹配而失败的问题。
- 确保撤销或重做异常时恢复重放状态并移除冻结画面，避免画面卡住。
- 修复空值处理并清理编译期不可达分支；Release 构建无警告、无错误。

## 使用方法

| 按键 | 功能 |
| --- | --- |
| ← 左方向键 | 撤销最近一次玩家决策 |
| → 右方向键 | 重做 |

支持撤销/重做出牌、使用或丢弃药水、结束回合。撤销结束回合时，也会一并回退敌方回合。撤销后执行新的操作会清空重做记录。

默认优先使用经过游戏战斗状态校验的快速快照；快照不可用或校验失败时，撤销会回退到游戏内置战斗回放机制。重做使用回放机制。操作过程中会重建战斗画面，因此也可以在动画或敌方回合期间请求撤销。

## 限制

- 仅支持单人游戏。
- 从事件中开始的战斗只能使用快速快照路径；若当前没有可用且已验证的快照，撤销不会执行。
- 若同时使用自动结束回合的 mod，撤销结束回合后，该 mod 可能立即再次结束回合。
- 快速路径通常约需 0.1 秒；回放路径需要重建战斗，耗时会随已进行的回合增加。

## 安装

将 `UndoAndRedo.dll` 和 `UndoAndRedo.json` 放入游戏目录的 `mods` 文件夹。游戏目录结构示例：

```text
Slay the Spire 2/
└── mods/
    ├── UndoAndRedo.dll
    └── UndoAndRedo.json
```

## 从源码构建

需要安装 .NET 9 SDK，并将 `STS2GameDir` 指向 Slay the Spire 2 的安装目录。PowerShell 示例：

```powershell
dotnet build .\UndoAndRedo.csproj -c Release "-p:STS2GameDir=C:\Program Files (x86)\Steam\steamapps\common\Slay the Spire 2"
```

构建完成后，将以下两个文件复制到游戏目录的 `mods` 文件夹：

```text
bin\Release\net9.0\UndoAndRedo.dll
UndoAndRedo.json
```

## 项目结构

```text
.
├── FastPath/              快照还原与战斗画面重建
├── banner.html            Mod 横幅素材
├── nexusmods_description.bbcode
├── ReplayRecorder.cs      记录战斗事件、撤销边界和校验信息
├── RewindEngine.cs        撤销/重做及游戏回放流程
├── SelfTest.cs            可选的游戏内端到端测试
├── ShadowSnapshot.cs      快照诊断工具
├── UndoAndRedoMod.cs      Mod 入口、按键和 Harmony 补丁
├── UndoAndRedo.csproj
├── UndoAndRedo.json       Mod 清单
├── DOCUMENTATION.md       实现设计与测试说明
└── LESSONS.md             已发现问题、修复原因和兼容性经验
```

日志默认写入游戏用户数据目录下的 `logs/UndoAndRedo.log`。如需详细跟踪信息，可在同一 `logs` 目录创建 `UndoAndRedo.debug` 文件。

## 致谢

本 mod 的灵感来自 Slay the Spire 1 的 [Undo the Spire](https://github.com/filippobaroni/undo-the-spire)，作者为 [filippobaroni](https://steamcommunity.com/sharedfiles/filedetails/?id=3354673683)。本项目针对 Slay the Spire 2 的 Godot/.NET 架构重新实现。
