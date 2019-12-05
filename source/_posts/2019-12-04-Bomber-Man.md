---
title: Bomber Man / 炸弹人
categories: Unity
tags:
  - Code
  - Basic
comments: true
copyright: true
date: 2019-12-04 09:29:15
---

## 游戏展示

![Bomber Man](https://github.com/GameDevLog/GameDevLogTemplete/raw/master/ScreenShots/BomberMan.gif)

<!--more-->

![Bomber Man](https://github.com/GameDevLog/BomberMan/raw/master/Screenshots/BomberMan.png)

## 游戏开发

### 创建 **BomberMan** 游戏

{% asset_img CreateBomberMan.png CreateBomberMan %}

### 导入资源

可以从 GitHub 上面查看到。

{% asset_img Assets_Sprites.png import Sprites Assets %}

### 设置分辨率 400x800

{% asset_img Resolution_400_800.png Resolution 400x800 %}

从 `Project > Sprites` 中拖动 `BG` 到 `GamePlay` Scene。

### 添加背景

* BG
    * Transform
        * Scale
            * X: `1.5`
            * Y: `1.5`

{% asset_img BackGround.png BackGround %}

### 添加 Player

从 `Project > Sprites` 中拖动 `Player` 到 `GamePlay` Scene。

* Player
    * Transform
        * Position
            * X: `0`
            * Y: `-3.66`
    * Sprite Renderer
        * Additional Settings
            * Order in Layer: `1`

{% asset_img Player.png Player %}

#### 给 Player 添加 Rigibody 2D

* Player
    * Add Component
        * Rigibody 2D
    
{% asset_img Rigibody_2D.png Rigibody 2D %}

`Kinematic`: 运动学

如果该属性设置为 `true` 表示该物体运动状态不受外力，碰撞和关节的影响，而只受到动画以及附加在物体上的脚本影响，但是该物体仍然能改变其他物体运动状态

* Player
    * Rigibody 2D
        * Body Type: `Kinematic`
    
{% asset_img Kinematic.png Kinematic 2D %}

#### 给 Player 添加 Circle Collider 2D

* Player
    * Circle Collider 2D
        * Is Trigger: `True`
        * Radius: `0.41`

{% asset_img Circle_Collider_2D.png Circle Collider 2D %}

#### 创建 PlayerScripts

* Project
    * Assets
        * Create
            * Folder: `Scripts`
        * Scripts
            * Create
            * C# Script: `PlayerScript`

添加 `PlayerScript` 到 `Player` 组件上。

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerScript : MonoBehaviour
{
    public float speed = 10f;
    private float minX = -2.55f;
    private float maxX = 2.55f;

    // Update is called once per frame
    void Update()
    {
        MovePlayer();
    }

    void MovePlayer()
    {
        float h = Input.GetAxis("Horizontal");
        Vector2 currentPosition = transform.position;

        if (h > 0)
        {
            // going to the right side
            currentPosition.x += speed * Time.deltaTime;
        }
        else if (h < 0)
        {
            // going to the left side
            currentPosition.x -= speed * Time.deltaTime;
        }

        if (currentPosition.x < minX)
        {
            currentPosition.x = minX;
        }
        else if (currentPosition.x > maxX)
        {
            currentPosition.x = maxX;
        }

        transform.position = currentPosition;
    }
}
```

{% asset_img Player_Script.png Player Script %}

运行游戏，使用 `AWDS` 中的 AD 或者使用 `左右方向键` 移动

### 添加 Bomb

1. 从 `Project > Sprites` 中拖动 `Bomb` 到 `GamePlay` Scene。
1. 添加 `Tag`
1. 添加 `Rigibody 2D`
1. 添加 `Circle Collider 2D`

* Bomb
    * Tag: `Bomb`
    * Transform
        * Position
            * X: `0`
            * Y: `3.28`
        * Scale
            * X: `0.8`
            * Y: `0.8`
    * Rigibody 2D
    * Circle Collider 2D
        * Is Trigger: `True`


{% asset_img AddTag.png Add Tag %}

{% asset_img AddBombTag.png Add Bomb Tag %}

{% asset_img Bomb.png Bomb %}

### Prefabs / 预设体

* Project
    * Assets
        * Create
            * Folders: `Prefabs`
        * Prefabs

将 `Hirearchy > GamePay > Bomb` 拖到 `Prefabs` 中。

删除 `Hirearchy > GamePay > Bomb`。

{% asset_img Prefabs.png Prefabs %}

### Spawner / 生成器

* Hirearchy
    * Create Empt
        * Rename: `Spawner`
* Inspector
    * Spawner
        * Select Icon （左上角)
        * Transform: `Reset`
            * Position
                * X: `0`
                * Y: `5.6`

#### 创建 SpawnerScripts

* Project
    * Assets
        * Scripts
            * Create
            * C# Script: `SpawnerScripts`

添加 `SpawnerScripts` 到 `Spawner` 组件上。

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SpawnerScript : MonoBehaviour
{
    public GameObject bombPrefab;

    private float minX = -2.55f;
    private float maxX = 2.55f;

    void Start()
    {
        StartCoroutine(SpawnBombs());
    }

    IEnumerator SpawnBombs()
    {
        yield return new WaitForSeconds(Random.Range(0f, 1f));

        Instantiate(bombPrefab, new Vector2(Random.Range(minX, maxX), transform.position.y),
            Quaternion.identity);

        StartCoroutine(SpawnBombs());

    }
} 
```

将 `Prefabs > Bomb` 拖动到 `Hierarchy > Spawner` 上。

{% asset_img Spawner.png Spawner %}

### Collector / 收集器

* Hirearchy
    * Create Empt
        * Rename: `Collector`
* Inspector
    * Collector
        * Select Icon （左上角)
        * Transform: `Reset`
            * Position
                * X: `0`
                * Y: `-6.625`
        * Box Collider 2D
            * Is Trigger: `True`
            * Size
                * X: `7.4`
                * Y: `1`
            * Info
            * Bounds
                * Center
                    * X: 0
                    * Y: -4.9

#### 创建 CollectorScripts

**Score Text**

* Hirearchy
    * Create
        * UI
            * Text
* Inspector
    * Rect Transform
        * Archors
            * top
            * left
        * Pos X: `125.5`
        * Pos Y: `125.5`
        * Width: `224`
        * Height: `50`
    * Text(Script)
        * Text: `Score: 0`
        * Character
            * Font Size: `36`
        * Paragraph
            * V: `Center`
        * Color: `White`

{% asset_img Text.png Text %}

* Project
    * Assets
        * Scripts
            * Create
            * C# Script: `CollectorScripts`

添加 `CollectorScripts` 到 `Collector` 组件上。

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class CollectorScript : MonoBehaviour
{
    public Text scoreText;
    private int score;

    private void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.tag == "Bomb")
        {
            IncreaseScore();
            collision.gameObject.SetActive(false);
        }
    }

    void IncreaseScore()
    {
        score++;

        scoreText.text = "Score: " + score;
    }
}
```

{% asset_img Collector.png Collector %}

### 重置游戏

`Score Text`:

* Inspector
    * Rect Transform
        * Pos X: `180`
        * Pos Y: `-60`
        * Width: `300`
        * Height: `100`
    * Text(Script)
        * Text: `High Score: 0\nScore: 0`

* 修改 `CollectorScripts` 代码。

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class CollectorScript : MonoBehaviour
{
    public Text scoreText;
    private int score;
    private static int highScore;

    private void Start()
    {
        DisplayScore();
    }

    private void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.tag == "Bomb")
        {
            IncreaseScore();
            collision.gameObject.SetActive(false);
        }
    }

    void DisplayScore()
    {
        scoreText.text = "High Score: " + highScore + "\nScore: " + score;
    }

    void IncreaseScore()
    {
        score++;

        if (score > highScore)
        {
            highScore = score;
        }

        DisplayScore();
    }
}
```

## 导出游戏

* File
    * Build Settings (Shift + Cmd + B)

{% asset_img Build_Settings.png Build Settings %}

* Player Settings 
{% asset_img Player_Settings.png Player Settings  %}

## GitHub

<https://github.com/GameDevLog/GameDevLog>