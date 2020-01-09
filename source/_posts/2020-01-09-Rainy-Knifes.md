---
title: Rainy Knifes
categories: Unity
tags:
  - Code
  - Design
comments: true
copyright: true
date: 2020-01-09 13:46:54
---

## 游戏展示

![Rainy Knifes](https://raw.githubusercontent.com/GameDevLog/GameDevLogTemplete/master/ScreenShots/11.RainyKnifes.gif)

<!--more-->

## 游戏开发

```sh
git clone https://github.com/GameDevLog/Rainy-Knifes
```

* Project Settings > General > Display > Window 
    * Size
        * Width: `480`
        * Height: `800`
    * Stretch
        * Mode: `2d`
        * Aspect: `keep`

### 导入资源

Scene

* Node: `Main`
    * Sprites: `Background`
    * Node2D: `GroundParent`
        * Sprites: `ground` x 4

Inspector

* Background > Node2D > Transform
    * Position
        * x: `241.067`
        * y: `403.912`
    * Scale
        * x: `0.67`
        * y: `0.63`

### 创建动画

Scene

* Node2D: `Player`
    * Sprites: `Idle`
    * AnimationPlayer: `IdleAnimation`
    * Sprites: `Walk`
    * AnimationPlayer: `WalkAnimation`

Inspector

* Player > Node2D > Transform
    * Position
        * x: `240`
        * y: `480`

* Idle
    * Sprite > Animation
        * Hframes: `2`
    * Node2D > Transform > Scale
        * x: `0.5`
        * y: `0.5`

* WalkAnimation > AnimationPlayer > Playback Options
    * Speed: `3`

Animation

* New: `Idle`
    * Animation Length: `0.2`
    * Current Pos.: `0`

* Idle > Sprite > Animation
    * Frame: `0` **+**

* New: `Walk`
    * Animation Length: `1`

### 移动 `Player`

```csharp
using Godot;
using System;

public class Player : Node2D
{
	private Sprite idleSprite;
	private Sprite walkSprite;

    private AnimationPlayer idleAnimation;
    private AnimationPlayer walkAnimation;

    [Export]
    float moveSpeed = 500f;
	
    public override void _Ready()
    {
        idleSprite = GetNode<Sprite>("Idle");
        walkSprite = GetNode<Sprite>("Walk");

        idleAnimation = GetNode<AnimationPlayer>("IdleAnimation");
        walkAnimation = GetNode<AnimationPlayer>("WalkAnimation");
    }

    public override void _Process(float delta)
    {
        base._Process(delta);

        if (Input.IsActionPressed("left"))
        {
            Vector2 temp = Position;
            temp.x -= moveSpeed * delta;
            Position = temp;
        }
        else if (Input.IsActionPressed("right"))
        {
            Vector2 temp = Position;
            temp.x += moveSpeed * delta;
            Position = temp;
        }
    }
}
```

### Scenes & Prefabs

### 生成刀

Scene

* Node2D: `Knife`
    * Sprite: `Knife`

Inspector

* Knife > Node2D > Transform
    * Rotation Degree: `-90`

Scene

* Main
    * Position2D: `MinX`
    * Position2D: `MaxX`
    * Timer

Inspector

* MinX > Node2D > Transform > Position
    * x: `10`
    * y: `-50`
* MaxX > Node2D > Transform > Position
    * x: `470`
    * y: `-50`
* Timer > Timer
    * Wait Time: `0.7`
    * Autostart: `True`

### 碰撞检测

Inspector > Node2D > Z Index

* Z Index
    * Background: `0`
    * backg: `1`
    * Player: `2`
    * Knife: `3`

* Knife
    * Area2D

Node

* Groups > Manage Groups > Add
    * Knife

`Player.cs`

```csharp
public class Player : Node2D {
    void _OnCollisionEntered(Area2D area) { 
        if(area.IsInGroup("Knife")) {
            gameplayScript.PlayerDied();
            QueueFree();
        }
    }
}
```

### 重新开始

Scene

* Main
    * Timer: `RestartTimer`

`Main.cs`

```csharp
using Godot;
using System;

public class Main : Node
{
    [Export]
    private PackedScene knifePrefab;

    private Position2D minX;
    private Position2D maxX;

    private Timer restartTimer;

    public override void _Ready()
    {
        minX = GetNode<Position2D>("MinX");
        maxX = GetNode<Position2D>("MaxX");

        restartTimer = GetNode<Timer>("RestartTimer");
        restartTimer.Connect("timeout", this, "_OnRestartGame");
        restartTimer.WaitTime = 2f;
        restartTimer.OneShot = true;
    }

    private void _OnTimerTimeout()
    {
        Knife knife = knifePrefab.Instance() as Knife;
        float x = (float)GD.RandRange(minX.GetPosition().x, maxX.GetPosition().x);

        knife.Position = new Vector2(x, minX.GetPosition().y);
        AddChild(knife);
    }

    private void _OnRestartGame()
    {
        GetTree().ReloadCurrentScene();
    }

    public void PlayerDied()
    {
        restartTimer.Start();
    }
}
```

## 小结

参考：

1. []()

## 下载

### 游戏

Release: <https://github.com/GameDevLog/GameDevLogTemplete/releases>

### 源码

GitHub: <https://github.com/GameDevLog/GameDevLogTemplete>

找到游戏列表对应的 [GameDevLog](https://game.iosdevlog.com)