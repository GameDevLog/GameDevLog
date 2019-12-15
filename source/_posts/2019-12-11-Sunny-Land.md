---
title: Sunny Land / Unity 资源
categories: Unity
tags:
  - Code
  - Design
comments: true
copyright: true
date: 2019-12-11 19:04:52
---

## 游戏展示

![Sunny Land](https://raw.githubusercontent.com/GameDevLog/GameDevLogTemplete/master/ScreenShots/6.SunnyLand.gif)

<!--more-->

## 游戏开发

### 创建 2D 游戏

略

### Import Assets / 导入资源

[Sunny Land](https://assetstore.unity.com/packages/2d/characters/sunny-land-103349)

{% asset_img Sunny_Land_Assets.png Sunny Land Assets %}

### Background / 背景

每单元像素改为 `16`，*以下所有资源做相同操作*。

* Project
    *  Sunnyland
        * artwork
            * Environment
                * back
* Inspector
    * Sprite Mode
        * Pixels Per Unit: `16`

* back -> Hierarchy

{% asset_img Background.png Background %}

### Tilemap / 瓦片地图

* Hierarchy
    * Create
        * 2D Object
            * Tilemap
* Window
    * 2D
        * Tile palette
* Tile palette
    * Create New Palette
 
{% asset_img Tile_Palette.png Tile Palette Window %}

{% asset_img Create_new_Palette.png Create new Palette %}       

* Project
    *  Sunnyland
        * artwork
            * Environment
                * tileset
* Inspector
    * Sprite Mode: `Multiple`
        * Pixels Per Unit: `16`
    * Apply
    * Sprite Editor
 
{% asset_img Sprite_Editor.png Sprite Editor %}

* Sprite Editor
    * Slice
        * Type: `Grid By Cell Size`
        * Pixel Size
            * X: `16`
            * Y: `16`
    * Apply

{% asset_img Slice.png Create Slice %}

* Project
    *  Sunnyland
        * artwork
            * Environment
                * tileset -> `Tile Palette`

{% asset_img Tileset.png Tileset %}

{% asset_img Draw_Map.png Draw Map %}

### Main Camera Size / 调整相机尺寸

* Inspector
    * Camera
        * Size: `6`

{% asset_img Main_Camera_Size.png Main Camera Size %}

### Sorting Layer / 层排序

`Order In Layer` 也可以排序，数字越大越前

* Hierarchy
    * Back
* Inspector
    * Sprite Renderer
        * Additional Settings
            * Sorting Layer
                * Add Sorting Layer...

新增

1. Background
2. Foreground

{% asset_img Sorting_Layer.png Sorting Layer %}

* Hierarchy
    * Back
* Inspector
    * Check: `显示 Back`
    * Sprite Renderer
        * Additional Settings
            * Sorting Layer: `Background`

* Hierarchy
    * Grid
        * Tielmap
* Inspector
    * Sprite Renderer
        * Additional Settings
            * Sorting Layer: `Fackground`

{% asset_img Change_Layer.png Change Layer %}

### Player

创建 Player Sprite, 并将 `player-idle-1` 拖入

* Project
    * Assets
        * Sunnyland
            * artwork
                * Sprites
                    * player
                        * idle: `16 Pixels Per Unit`
                            * `player-idle-1`
-> 
* Hierarchy
    * Create
        * 2D Object
            * Sprite: `Player`
* Inspector
    * Sprite Renderer
        * Sprite: `player-idle-1`

### Physics / 物理

Tilemap

* Inspector
    * Tilemap Collider 2D

Player

* Inspector
    * Rigidbody 2D
        * Constraints
            * Freeze Rotation
                * Z: `true` # 不让 Z 轴移动
    * Box Collider 2D
        * Edit Collider

{% asset_img Player_Box_Collider_2D.png Player Box Collider_2D %}

`PlayerController.cs`

```csharp
using System;
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    public float moveSpeed;
    public float jumpForce;
    private Rigidbody2D rb;

    private void Start()
    {
        rb = GetComponent<Rigidbody2D>();

        if (moveSpeed <= 0)
        {
            moveSpeed = 400;
        }

        if (jumpForce <= 0)
        {
            jumpForce = 400;
        }
    }

    private void FixedUpdate()
    {
        Movement();
    }

    private void Movement()
    {
        // -1 ~ 1
        float horizontalMove = Input.GetAxis("Horizontal");
        // [-1, 0, 1]
        float faceDirection = Input.GetAxisRaw("Horizontal");
        float EPSILON = 0.1f;

        if (Math.Abs(horizontalMove) > EPSILON)
        {
            rb.velocity = new Vector2(horizontalMove * moveSpeed * Time.deltaTime, rb.velocity.y);
        }

        if ((Math.Abs(faceDirection) > EPSILON))
        {
            transform.localScale = new Vector3(faceDirection, 1, 1);
        }

        if (Input.GetButtonDown("Jump"))
        {
            rb.velocity = new Vector2(rb.velocity.x, jumpForce * Time.deltaTime);
        }
    }
}
```

### Animation / 动画

**先选中 `Player`
先选中 `Player`
先选中 `Player`**

* Porject
    * Assets
        * Animation
            * Player
                * Animator Controller: `Player`
-> 
* Inpsector
    * Animator
        * Controller: `Player`
* Hierarchy
    * Player
* Window
    * Animation
        * Animation
* Animation
    * Create: `Idle`
    * Setting
        * Show Sample Rate: `true`
    * Samples: `10`
* Project
    * Assets
        * Sunnyland
            * artwork
                * Sprites
                    * player
                        * idle
                            * player-idle-[1-4] ->`Idle`

{% asset_img Animation.png Add Animation %}

### Animator / 动画师（动画转变）

* Window
    * Animation
        * Animator
* Animator
    * Idle
        * Make Transition
            * Run
            * Jump
    * Run
        * Make Transition
            * Idle
            * Jump
    * Jump
        * Make Transition
            * Fall
    * Fall
        * Make Transition
            * Idle
    * Parameters + float
        * running: `0.0`
    * Parameters + bool
        * jumping: `false`
        * falling: `false`
        * idle: `false`

{% asset_img Animator.png Add Animator %}

Idle -> Run

* Inspector
    * Has Exit Time: `false`
    * Settings
        * Transition Duration: `0`
    * Conditions
        * running > `0.1`

Run -> Idle

* Inspector
    * Has Exit Time: `false`
    * Settings
        * Transition Duration: `0`
    * Conditions
        * running < `0.1`

Idle -> Jump & Run -> Jump

* Inspector
    * Has Exit Time: `false`
    * Settings
        * Transition Duration: `0`
    * Conditions
        * jumping: `true`

Jump -> Fall

* Inspector
    * Has Exit Time: `false`
    * Settings
        * Transition Duration: `0`
    * Conditions
        * falling: `true`
        * jumping: `false`

Fall -> Idle

* Inspector
    * Has Exit Time: `false`
    * Settings
        * Transition Duration: `0`
    * Conditions
        * falling: `false`
        * idle: `true`

### Bug Fix:

* Bug 现象
    * `Player` 会卡住
* 原因
    * `Box Collider 2D` 与 `Tilemap` 多点碰撞检测
* 解决方案
    * 添加 `Circle Collider 2D`，单点碰撞检测

{% asset_img Circle_Collider_2D.png Add Circle Collider 2D %}

### Cinemachine / 电影镜头

#### Install Package / 安装包

* Menu
    * Window
        * Package Manager
* Packages
    * Cinemachine: `Install`

{% asset_img Cinemachine.png Cinemachine %}

#### Create 2D Camera / 创建 2D Camera

* Menu
    * Cinemachine
        * Create 2D Camera

{% asset_img 2D_Camera.png Add 2D Camera %}

#### CM vcam1

* Inspector
    * Cinemachine Virtual Camera(Script)
        * Follow: `Player`
        * Lens
            * Orthographic Size: `7.43` 
        * Body
            * Dead Zone Width: `0.2`
            * Dead Zone Height: `0.24`

{% asset_img CM_vcam.png Add CM vcam %}

##### 调整背景

* Hierarchy
    * Create
        * Create Empty: `Background`
* Inspector
    * Polygon Collider 2D
        * IsTrigger: `True`

{% asset_img Circle_Collider_2D.png Add Circle Collider 2D %}

##### CinemaConfiner / 界限，边界

* Inspector
    * Cinemachine Virtual Camera(Script)
        * Add Extension
            * CinemaConfiner
    * Cinemachine Confiner(Script)
        * Bounding Shape 2D: `Background(Polygon Collider 2D)`

{% asset_img Bounding_Shape_2D.png Add Bounding Shape 2D %}

### Collections / 收集

cherry & gem: `16 px`

* Hierarchy
    * Create
        * 2D Object
            * Sprite: `Cherry`
* Inspector
    * Tag: `Collection`
    * Sprite Renderer
        * Sprite: `cherry-1`
        * Sorting Layer: `Foreground`
    * Box Collider
        * IsTrigger: `true`
    * Animator
        * Controller: `Cherry`
<-
* Project
    * Assets
        * Animation
            * Collections
                * Animator Controller: `Cherry`

{% asset_img Cherry_Animation.png Cherry Animation %}

### Bug Fix:

* Bug 现象
    * `Player` 方向键，粘墙(Tilemap)
* 原因
    * `Player` 与 `Tilemap` 之间有磨擦力，不会自动滑落
* 解决方案
    * `Assets` 添加 `Physics Material 2D`，磨擦力(Friction) 置为 `0`，添加到 `Player > Box Collider 2D`

{% asset_img Physics_Material_2D.png Physics Material 2D %}

### UI

* Hierarchy
    * Create
        * UI
            * Canvas
* Canvas
    * Create
        * UI
            * Text: `Cherry`
            * Text: `Numbers`

{% asset_img Text_Archors.png Text Archors %}

### Enemies / 敌人

#### Frog

* Hierarchy
    * Create
        * Create Empty: `Enemies`
    * Enemies
        * Create
            * 2D Object
                * Sprite: `Frog`
        * Frog
            * Left: `Empty Object`
            * Right: `Empty Object`
* Inspector
    * Tag: `Enemy`
    * Sprite Renderer
        * Sprite: `frog-idle-1`
    * Rigidbody 2D
        * Constraints
            * Freeze Rotation
                * Z: `true`
    * Box Collider 2D
        * Edit Collider

创建 *Animator Controller*: **Frog**， 拖动到 `Frog`

创建三种状态

1. FrogIdle
1. FrogJump
1. FrogFall

* Prefabs
    * Player

{% asset_img Hurt.png Hurt %}

#### Eagle

从 `Assets` 中拖动 `eagle-attack-1` 到 `Hierarchy`，重命名为 `Eagle`

* Hierarchy
    * Eagle
        * Top: `Empty Object`
        * Bottom: `Empty Object`
* Inspector
    * Tag: `Enemy`
    * Sprite Renderer
        * Sprite: `eagle-attack-1`
    * Rigidbody 2D
        * Gravity Scale: `0` # 重力
        * Constraints
            * Freeze Position
                * X: `true`
            * Freeze Rotation
                * Z: `true`
    * Box Collider 2D
        * Edit Collider
        * Offset
            * X: `-0.4`
            * Y: `-0.4`
        * Radius: `0.5`

创建 *Animator Controller*: **Eagle**， 拖动到 `Eagle`

创建一种状态 `Eagle`

* Prefabs
    * Player

### Audio / 音效

* Audio Listener: 耳朵
* Audio Source: 扬声器
* Audio Clips: 音乐/声音

#### Assets

* Sound FX - Retro Pack
* Casual Game BGM

Player BGM / 背景音

* Inspector
    * Prefab
        * Overrides
            * Apply All # 每个 `Player Prefab` 都包含声音
    * Audio Source
        * Loop: `true`
        * AudioClip: `BGM_01`
<-
* Project
    * CasualGame
        * BGM_01

Enemy 消灭声音

* Hierarchy
    * Enemy
        * Frog >
* Inspector
    * Audio Source Effects
        * Play On Awake: `false`
        * AudioClip: `hit_04`
<-
* Project
    * Zero Rare
        * Retro Source
            * Hit
                * hit_04

{% asset_img Hit.png Hit %}
 
其它声音

* 跳
* 收集
* 。。。

### Dialog / 对话框

#### Assets / 字体

Font: Free Pixel Font - Thaleah

* Hierarchy
    * Canves
        * Panel: `Dialog`
            * Text

{% asset_img Dialog.png Dialog %}

#### House

* Inspector
    * Box Collider 2D
        * Edit Collider
        * Is Trigger: `true`
    * Enter Dialog(Script)
        * Enter Dialog: `Dialog`

{% asset_img House.png House %}

#### Dialog Animation

* Project
    * Animation
        * Dialog
            * Animation: `Dialog`

拖动到 UI `Dialog` 上，自动生成 `EnterDialog` Animator

{% asset_img Dialog_Animation.png Dialog Animation %}

### Crouch / 下蹲

* Edit
    * Project Settings...
        * Input
            * Jump
                * Duplicate Array Element: `Crouch`

{% asset_img Porject_Settings.png Porject_Settings %}

* Crouch
    * Positive Button: `s`
    * Alt Positive Button: `down`

{% asset_img Crouch_Settings.png Crouch Settings %}

* Project
    * Assets
        * Animation
            * Player
                * Animation: `Crouch`

### DeadLine / 死亡

* Hierarchy
    * Empty Object: `DeadLine`
* Inspector
    * Tag: `DeadLine`
    * Transform
        * Position
            * Y: `-8.3`
    * Box Collider 2D
        * Is Trigger: `True`

{% asset_img DeadLine.png DeadLine %}

### 光效

* 素材的 Diffuse 材质 
* 法线贴图 Normal map
* 点光源，方向光源

#### 安装 Package

Lightweight RP

* Level_2
    * Grid
        * Ground
* Inspector
    * Tilemap Renderer
        * Material: `Default-Diffuse`

变暗

## 导出游戏

* File
    * Build Settings (Shift + Cmd + B)

![Build Settings](https://game.iosdevlog.com/2019/12/04/Bomber-Man/Build_Settings.png)

* Player Settings

{% asset_img Player_Settings.png Player Settings  %}

## 小结

UI 与 Game 用的是两套不同的坐标。

参考:

1. [Unity教程 入门第一款游戏制作](https://space.bilibili.com/370283072/channel/detail?cid=85776)
1. [2D Movement in Unity (Tutorial)](https://www.youtube.com/watch?v=dwcT-Dch0bA)

## 下载

### 游戏

Release: <https://github.com/GameDevLog/GameDevLogTemplete/releases>

### 源码

GitHub: <https://github.com/GameDevLog/GameDevLogTemplete>