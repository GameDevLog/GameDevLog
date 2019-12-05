---
title: Guess The Number / 猜数字
categories: Unity
tags:
  - Code
  - Design
comments: true
copyright: true
date: 2019-12-05 13:38:41
---

## 游戏展示

![Guess The Number](https://raw.githubusercontent.com/GameDevLog/GameDevLogTemplete/master/ScreenShots/GuessTheNumber.gif)

<!--more-->

## 游戏开发

### 创建 2D 游戏

略

### 设置分辨率 1280x720

{% asset_img Resolution.png Resolution %}

### InputField

* Hirearchy
    * Create
        * UI
            * InputField

{% asset_img Create_InputField.png Create_InputField %}

* Inspector
    * Rect Transform
        * Width: `700`
        * Height: `80`

{% asset_img InputField.png InputField %}

#### Canvas

* Inspector
    * Canvas
        * Render Mode: `Screen Space - Camera`
        * Render Camera: `Main Camera`
    * Canvas Scaler(Script)
        * UI Scale Mode: `Scale With Screen Size`
        * Reference Resolution
            * X: `1280`
            * Y: `720`
        * Match: `0.5`

{% asset_img Canvas.png Canvas %}

#### InputField > Text

* Inspector
    * Text(Script)
        * fontSize: `60`
    * Paragraph
        * Alignment: `center`

{% asset_img InputField_Text.png InputField_Text %}

#### InputField > Placeholder

* Inspector
    * Text(Script)
        * Text: `Enter Your Guess`
    * Paragraph
        * Alignment: `center`

{% asset_img Placeholder.png Placeholder %}

### Text

* Hirearchy
    * Canvas
        * Create
            * Text

* Inspector
    * Rect Transform
        * Pos X: 0
        * Pos Y: `218 `
        * Width: `700`
        * Height: `80`
    * Text(Script)
        * Text: `Guess A Number Between 0 And 100`
    * Paragraph
        * Alignment: `center`

{% asset_img Text.png Text %}

#### Camera

* Inspector
    * Camera
        * Background: `Black`

{% asset_img MainCamera.png Main Camera %}

{% asset_img Input.png Input %}

### Button

* Hirearchy
    * Canvas
        * Create
            * Text

* Inspector
    * Text(Script)
        * Text: `Guess`

#### Button -> Text

* Inspector
    * Rect Transform
        * Pos X: 0
        * Pos Y: `-162 `
        * Width: `300`
        * Height: `108`

{% asset_img Button.png Button %}

### GuessTheNumberScript

* Project
    * Create 
        * Folder: `Scripts`
    * Scripts
        * Create
            * C# Script: `GuessTheNumberScript`

{% asset_img GuessTheNumberScript.png GuessTheNumberScript %}

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class GuessTheNumberScript : MonoBehaviour
{
    public InputField input;
    public Text infoText;
    private int guessNumber;
    private int userGuessNumber;

    void Start()
    {
        guessNumber = Random.Range(0, 100);
    }

    public void CheckGuess()
    {
        userGuessNumber = int.Parse(input.text);

        if (userGuessNumber == guessNumber)
        {
            infoText.text = "You Guessed The Number! You Are A Wizard";
        }
        else if (userGuessNumber > guessNumber)
        {
            infoText.text = "Your Number Is Greater Than The Guess Number";
        }
        else
        {
            infoText.text = "Your Number Is Lower Than The Guess Number";
        }

        input.text = "";
    }
}
```

### GameController

* Hirearchy
    * Create Empty
        * reanem: `GameController`

添加 `GuessTheNumberScript` 到 `GameController`

{% asset_img GameController.png Game Controller  %}

### On Click

Button

* Inspector
    * On Click()
        * 添加: `GameController`
            * 选择： `GuessTheNumberScript.CheckGuess`

{% asset_img OnClick.png Button On Click  %}

## 导出游戏

* File
    * Build Settings (Shift + Cmd + B)

![Build Settings](https://game.iosdevlog.com/2019/12/04/Bomber-Man/Build_Settings.png)

* Player Settings 

{% asset_img Player_Settings.png Player Settings  %}

## GitHub 源码

<https://github.com/GameDevLog/GameDevLog>