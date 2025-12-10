---
title: "Unity6-ARで画像から3Dモデル表示"
emoji: "👓"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Unity", "AR", "Android"]
published: true
---
# 私たちの研究室(Nislab)

https://nisk.doshisha.ac.jp/

# アドベントカレンダー 10 日目~

https://nislab-advendcallender-2025.vercel.app/

## Unity 6
Unity6は2024年10月にリリースされた、Unityの新バージョンです。
以前のversionはセキュリティの保護がされていないため、このversionを使う事が推奨されています。
一方で、Unity6でのAR表示を説明している動画や文献が少ないです。
そのため、実際に画像からのAR表示に成功した例をご紹介します。
https://unity.com/ja/releases/unity-6

### Project
"Universal 3D URP"の新規プロジェクトを開きます。
UnityはARのプロジェクトが既に用意されていますが、初期設定をいじるよりも、一から作ったほうが楽です。

### Package Manager
インストールするもの:
- AR Foundation
- Google ARCore XR Origin
- XR Plugin Management

## Android接続
今回、Androidを使ってbuildを行います。
Android上で実行しないとカメラが使えないので、AndroidかMacであればiPhoneを用意する事をお勧めします。
この時、Androidは開発者モードになって、USB Debuggingがonになっている事を確認しましょう。
詳しい設定については、参考文献においてあるyoutube動画を見ていただければと思います。

## 3D表示方法
Sceneに、XR OriginとAR Sessionを用意します。
この時、AR Sessionはカメラの役割を果たします。

XR Originの中に"Add Component"から"AR Tracked Image Manager"を追加します。
![alt text](https://github.com/poe125/zenn_content/blob/main/articles/images/image.png?raw=true)

更に、Project->Assetsフォルダの中にCardImageLibrary (XR Reference Image Library)を追加します。
CardImageLibraryでは、認識したい画像と名前とサイズを設定します。
![alt text](https://github.com/poe125/zenn_content/blob/main/articles/images/image-1.png?raw=true)
このCardImageLibraryを、AR Tracked Image ManagerのSerialized Libraryに入れます。
![alt text](https://github.com/poe125/zenn_content/blob/main/articles/images/image-2.png?raw=true)

以下のコードが入ったスクリプト、show_multiple_image.csを作り、XR ORiginに追加します。
```
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.XR.ARFoundation;
using UnityEngine.XR.ARSubsystems;

public class show_multiple_image: MonoBehaviour
{
    // Prefabs to spawn
    [SerializeField] List<GameObject> prefabsToSpawn = new List<GameObject>();

    // ARTrackedImageManager reference
    private ARTrackedImageManager _trackedImageManager;

    // Dictionary to reference spawned prefabs with tracked image name
    private Dictionary<string, GameObject> _arObjects;

    // Initialization and references assigning
    private void Start()
    {
        _trackedImageManager = GetComponent<ARTrackedImageManager>();
        if (_trackedImageManager == null) return;
        _trackedImageManager.trackablesChanged.AddListener(OnImagesTrackedChanged);
        _arObjects = new Dictionary<string, GameObject>();
        SetupSceneElements();
    }

    private void OnDestroy()
    {
        _trackedImageManager.trackablesChanged.RemoveListener(OnImagesTrackedChanged);

    }

    // Setup Scene Elements
    private void SetupSceneElements()
    {
        foreach (var prefab in prefabsToSpawn){
            var arObject = Instantiate(prefab, Vector3.zero, Quaternion.identity);
            arObject.name = prefab.name;
            arObject.gameObject.SetActive(false);
            _arObjects.Add(arObject.name, arObject);


        }
    }
    // Update tracked images and prefabs

    private void OnImagesTrackedChanged(ARTrackablesChangedEventArgs<ARTrackedImage> eventArgs)
    {
        foreach (var trackedImage in eventArgs.added)
        {
            UpdateTrackedImages(trackedImage);
        }
        foreach(var trackedImage in eventArgs.updated)
        {
            UpdateTrackedImages(trackedImage);
        }
        foreach(var trackedImage in eventArgs.removed)
        {
            UpdateTrackedImages(trackedImage.Value);
        }
    }
    private void UpdateTrackedImages(ARTrackedImage trackedImage)
    {
        if (trackedImage == null) return;
        if(trackedImage.trackingState is TrackingState.Limited or TrackingState.None)
        {
            _arObjects[trackedImage.referenceImage.name].gameObject.SetActive(false);
            return;
        }
        _arObjects[trackedImage.referenceImage.name].gameObject.SetActive(true);
        _arObjects[(trackedImage.referenceImage.name)].transform.position = trackedImage.transform.position;
        _arObjects[(trackedImage.referenceImage.name)].transform.rotation = trackedImage.transform.rotation;
    }
}
```
show_multiple_imageの中の、Prefab to Spawnに表示したい3Dモデルを追加します。
この時、3Dモデルの名前がCardImageLibraryで設定した、3Dモデルを表示したい画像の名前と一致するようにしましょう。

## もし表示されない場合
表示されない場合、3Dモデルが大きすぎる可能性があります。一度サイズを0.0001くらいまで下げてみると良いかもしれません。

## 最後に
実際に3D表示した画面です。
![fushicho](https://github.com/poe125/zenn_content/blob/main/articles/images/fushicho.jpg?raw=true)
![forest dragon](https://github.com/poe125/zenn_content/blob/main/articles/images/forestdragon.jpg?raw=true)

## 参照
[作ったもの]ARを使ったカードバトルゲーム
https://github.com/poe125/ar_card_battle

## 参考文献
https://www.youtube.com/watch?v=bARrOv48ZSQ&t=602s&pp=ygUJVW5pdHkgQVIg