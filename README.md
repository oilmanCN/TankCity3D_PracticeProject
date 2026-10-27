# TankCity 3D

[English](#english) | [简体中文](#简体中文)

<a id="english"></a>

## English

TankCity 3D is a Unity tank-combat game set in a maze of interconnected zones. Drive through the maze, defeat enemies, destroy obstacles, collect upgrades, and find your way to the finish.

Built with C# and a custom IMGUI interface, the project explores how gameplay, AI, UI, audio, and persistent data work together in a small game. It is a practice project focused on Unity fundamentals, including singletons, MVC-style separation, events, coroutines, and IMGUI. The UI does not depend on uGUI or TextMeshPro.

### Gameplay

- **A maze divided into zones:** Walls separate the map into distinct combat areas. Destroy obstacles or complete encounters to open the way forward.
- **Zone-based encounters:** Fight stationary turrets, patrol tanks, roaming tanks, and guard turrets. Clearing a zone triggers the next progression event.
- **Branching routes:** After clearing the starting zone, two floating arrows appear. Touch an arrow to choose a route and remove the corresponding walls.
- **Destructible obstacles:** Color-changing blocks break after taking enough hits from the player and may drop rewards.
- **Upgrades and weapon pickups:**
  - **Stat upgrades:** Increase attack, defense, maximum health, or restore current health.
  - **Weapon pickups:** Randomly replace the current weapon, with different weapons providing different attack and defense bonuses.
- **Reaching the finish:** Clear the final zone to reveal the finish marker, then touch it to complete the level.

### Controls

| Action | Input |
| --- | --- |
| Move forward / backward | `W` / `S` |
| Turn the tank left / right | `A` / `D` |
| Rotate the turret horizontally | Move the mouse |
| Raise / lower the barrel | Move the mouse while the right mouse button is released |
| Look around freely | Hold the right mouse button and move the mouse |
| Fire | Left mouse button |
| Pause menu | `Esc` |

### Features

#### Gameplay systems

- **Zone management:** Detects when all enemies in a zone have been defeated and triggers route selection or the finish marker.
- **Route selection:** Collision-triggered arrows let the player choose a path and dynamically remove the corresponding wall group.
- **Roaming AI:** Picks random destinations around its spawn point, with obstacle avoidance, separation from other tanks, stuck detection, and recovery.
- **Patrol AI:** Loops through waypoints, slows down when turning, skips waypoints when stuck, and uses teleport recovery after repeated skips.
- **Combat AI:** Acquires the player as a target, checks line of sight with raycasts, smoothly rotates the turret, and fires on a cooldown.
- **Shields:** Configurable enemy shields absorb damage before health. Excess damage carries over to health, while visual feedback indicates hits, low shield strength, and shield destruction.
- **Damage resolution:** Projectiles cache their attack value and faction when fired, allowing damage to resolve even after the firing tank has been destroyed.

#### Custom IMGUI interface

- **Custom controls:** Buttons, sliders, toggles, labels, textures, and input fields implemented with IMGUI.
- **Shared panel base:** The generic singleton `BasePanel<T>` provides consistent panel show/hide behavior.
- **Game screens:** Start, settings, leaderboard, pause, warning, defeat, victory, and the main HUD.
- **Pause flow:** Pause the game, open settings and return, or confirm an exit through the `Esc` menu.

#### Visual feedback and camera

- **Hit flashes:** `HitFlashEffect` provides consistent red flashes for the player, enemies, and obstacles. It uses `MaterialPropertyBlock` to change colors without creating per-object material instances.
- **Shield effects:** `ShieldEffectController` coordinates low-shield flashing, hit flashes, and hiding a broken shield, with defined color priorities.
- **Obstacle colors:** Destructible blocks cycle through random HSV colors that avoid red hues, keeping red available as a hit cue.
- **Overhead status bars:** World positions are converted to screen and GUI coordinates to display shield and health bars, with smooth transitions, distance scaling, and occlusion checks.
- **Crosshair:** A raycast along the barrel direction projects an aiming point onto the screen. The system supports multiple muzzles and changes color when targeting an enemy.
- **Defeat sequence:** The player's tank is hidden on death, a destruction effect plays, and the defeat panel appears after the effect finishes.
- **Third-person camera:** Follows the tank with yaw linked to the turret. Right-button dragging adds a temporary viewing offset that returns to center on release; collision checks help prevent the camera from passing through walls.

#### Audio

- **AudioSource pool:** Ten AudioSources are used in rotation to support overlapping sound effects.
- **Per-clip throttling:** The same clip plays at most once within a 0.04-second window to reduce overlapping repetitions during dense combat.
- **Per-frame limit:** Regular sound-effect playback is limited to four sounds per frame.
- **Priority playback:** Key sounds, such as player shots and player destruction, use `PlaySoundPriority` to bypass regular throttling.

#### Data

- **PlayerPrefs persistence:** `PlayerPrefsDataMgr` saves and loads music settings, sound-effect settings, and leaderboard data.
- **MVC-style separation:** `GameDataMgr` acts as the data model. It is a singleton that does not inherit from `MonoBehaviour` and manages the game's data.

### Technical overview

| Area | Implementation |
| --- | --- |
| Engine | Unity 2021 LTS |
| Language | C# |
| UI | Custom IMGUI (`OnGUI`), without uGUI or TextMeshPro |
| Persistence | PlayerPrefs |
| Input | Legacy Input Manager |
| Rendering | Built-in Render Pipeline |

### Architecture and patterns

| Pattern | Application |
| --- | --- |
| Singleton | `GameDataMgr`, `MusicMgr`, `SoundEffMgr`, `CrosshairMgr`, and panels |
| Generics | `BasePanel<T>` |
| Template Method | `TankBase.Dead` / `OnDead` |
| Observer | `ZoneController.OnZoneCleared` and `Weapon.GetAimPoints` events |
| Object Pool | The AudioSource pool in `SoundEffMgr` |
| MVC-style separation | `GameDataMgr` as the model, panels as views, and event bindings for control logic |
| Strategy-style behavior | Enemy subclasses override `UpdateCombat`, `Patrol`, and `MoveToTarget` |

### Play the game

1. Download the repository and extract the files.
2. Keep the entire [`TankCity3D`](TankCity3D) folder together, including its data folder, DLLs, and runtime files.
3. On Windows, launch [`TankCity3D.exe`](TankCity3D/TankCity3D.exe).

### License

This project is shared for learning and discussion under the [MIT License](LICENSE).

---

<a id="简体中文"></a>

## 简体中文

TankCity 3D 是一款基于 Unity 的 3D 坦克迷宫射击游戏。玩家驾驶坦克穿过由多个区域组成的迷宫，消灭敌人、击碎障碍、拾取升级奖励，逐步探索通往终点的路线。

项目使用 C# 和基于 IMGUI 编写的自定义界面，将玩法、AI、UI、音效与数据存储整合到一个小型游戏中。这是一个围绕 Unity 基础架构展开的练习项目，涵盖单例、MVC 分层、事件、协程和 IMGUI 等内容；UI 不依赖 uGUI 或 TextMeshPro。

### 玩法简介

- **迷宫分区**：地图由多个独立区域（Zone）组成，区域之间由墙隔开。玩家需要击碎障碍或完成区域战斗，逐步打通路线。
- **区域战斗**：各区域布置固定炮塔、巡逻坦克、随机游走坦克、守卫炮台等敌人。清空当前区域后，会触发下一步推进事件。
- **路径选择**：起始区域清空后，出现左右两个漂浮箭头。触碰其中一个即可选择路线，并移除对应的墙组。
- **可摧毁障碍**：场景中的变色方块受到玩家多次射击后会被摧毁，并可能掉落奖励。
- **升级与武器拾取**：
  - **属性奖励**：提升攻击、防御、最大生命，或恢复当前生命。
  - **武器奖励**：随机替换当前武器，不同武器提供不同的攻击与防御加成。
- **抵达终点**：清空最后一个区域后，终点标记出现，触碰即可通关。

### 操作方式

| 操作 | 按键 |
| --- | --- |
| 车体前进 / 后退 | `W` / `S` |
| 车体左转 / 右转 | `A` / `D` |
| 炮塔水平旋转 | 移动鼠标 |
| 炮管俯仰 | 未按住鼠标右键时移动鼠标 |
| 相机自由观察 | 按住鼠标右键并移动鼠标 |
| 开火 | 鼠标左键 |
| 暂停菜单 | `Esc` |

### 功能特性

#### 游戏系统

- **区域管理**：检测区域内的敌人是否全部清空，并触发路径选择或终点生成。
- **路径选择**：通过碰撞触发箭头完成路线选择，动态移除对应墙组。
- **随机游走 AI**：在出生点附近随机选点移动，支持避障、同类避让、卡住检测与恢复。
- **巡逻 AI**：沿路径点循环移动，支持转向减速、卡住时跳过路径点，以及连续跳过后的传送恢复。
- **战斗 AI**：自动寻找玩家，通过射线判断视线遮挡，平滑转动炮塔，并按冷却时间开火。
- **护盾系统**：敌人可配置护盾值；伤害优先扣除护盾，溢出部分扣除生命，并提供受击、濒破和破碎反馈。
- **伤害结算**：子弹在发射时缓存攻击力与阵营，即使发射者已被销毁，仍能正确结算伤害。

#### 自定义 IMGUI 界面

- **自定义控件**：使用 IMGUI 编写按钮、滑动条、开关、标签、纹理和输入框。
- **统一面板基类**：通过泛型单例 `BasePanel<T>` 管理面板的显示与隐藏。
- **游戏界面**：包含开始、设置、排行榜、暂停、警告、失败、胜利和主 HUD。
- **暂停流程**：通过 `Esc` 菜单暂停游戏、进入设置并返回，或确认退出。

#### 视觉反馈与相机

- **受伤闪红**：玩家、敌人和障碍物统一使用 `HitFlashEffect`，通过 `MaterialPropertyBlock` 修改颜色，避免为每个对象创建材质实例。
- **护盾特效**：`ShieldEffectController` 统一协调濒破闪烁、受击闪红和破碎隐藏，并明确颜色优先级。
- **障碍物变色**：可摧毁方块周期性切换随机 HSV 颜色，避开红色系，使红色受击提示更易辨认。
- **头顶状态条**：将世界坐标转换为屏幕与 GUI 坐标，显示护盾和生命双条，支持平滑过渡、距离缩放与遮挡检测。
- **准心系统**：沿炮管方向从炮口发出射线，将瞄准点投影到屏幕；支持多炮口，并在瞄准敌人时变色。
- **死亡演出**：玩家死亡后隐藏坦克本体并播放特效，特效结束后显示失败面板。
- **第三人称相机**：跟随坦克，水平朝向与炮塔关联；按住右键拖动可临时偏移视角，松开后自动回正，并通过碰撞检测减少穿墙。

#### 音效系统

- **AudioSource 池**：轮换使用 10 个 AudioSource，支持多个音效重叠播放。
- **同类音效节流**：同一音频片段在 0.04 秒内最多播放一次，减少密集战斗中的重复叠加。
- **单帧数量限制**：常规音效每帧最多播放 4 个。
- **优先播放**：玩家射击、玩家死亡等关键音效通过 `PlaySoundPriority` 播放，绕过常规节流。

#### 数据系统

- **PlayerPrefs 持久化**：通过 `PlayerPrefsDataMgr` 保存和读取音乐、音效设置及排行榜数据。
- **MVC 分层**：`GameDataMgr` 作为数据模型，以不继承 `MonoBehaviour` 的单例管理游戏数据。

### 技术概览

| 方面 | 实现 |
| --- | --- |
| 引擎 | Unity 2021 LTS |
| 开发语言 | C# |
| UI | 自定义 IMGUI（`OnGUI`），不依赖 uGUI 或 TextMeshPro |
| 数据存储 | PlayerPrefs |
| 输入 | 旧版 Input Manager |
| 渲染管线 | Built-in Render Pipeline |

### 架构与设计模式

| 模式 | 应用位置 |
| --- | --- |
| 单例 | `GameDataMgr`、`MusicMgr`、`SoundEffMgr`、`CrosshairMgr` 及各面板 |
| 泛型 | `BasePanel<T>` |
| 模板方法 | `TankBase.Dead` / `OnDead` |
| 观察者 | `ZoneController.OnZoneCleared`、`Weapon.GetAimPoints` 事件 |
| 对象池 | `SoundEffMgr` 的 AudioSource 池 |
| MVC 分层 | `GameDataMgr` 作为 Model，面板作为 View，通过事件绑定连接控制逻辑 |
| 策略式行为 | 不同敌人子类重写 `UpdateCombat`、`Patrol` 和 `MoveToTarget` |

### 如何运行

1. 下载仓库并解压。
2. 保持 [`TankCity3D`](TankCity3D) 文件夹完整，保留其中的数据文件夹、DLL 和运行时文件。
3. 在 Windows 上运行 [`TankCity3D.exe`](TankCity3D/TankCity3D.exe)。

### 许可证

本项目用于学习交流，采用 [MIT License](LICENSE)。
