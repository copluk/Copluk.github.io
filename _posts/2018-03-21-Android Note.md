---
layout: post
title: Android Note
category: Android
tags: [Android, Layout]
description: 不常用但是時不時會用到的筆記
---

** Status Bar & Action Bar 顏色  
  
Status Bar相關設定，在開啟預設專案時，  
是定義在 /res/stytle.xml下的 <AppTheme>  
colorPrimary是ActionBar的顏色  
colorPrimaryDark是Status Bar的顏色  
  
而在Android版本大於23時，可以在<AppTheme>下設定  
```xml
<item name="android:windowLightStatusBar" tools:targetApi="23">true</item>  
```
讓Status Bar的顏色變成深色 (預設是白底)  
  
** Custom View Dialog Background  
這其實可以寫一篇啦(應該)  
只要指定一個View的元件給AlertDialogBilder  
基本上想幹嘛就可以幹嘛  
但是記得要把Dialog本身的Background設成透明  
不然會有一個白色的底，以至於樣式沒有正確顯示  
```java
yourDialog.getWindow().setBackgroundDrawable(new ColorDrawable(Color.TRANSPARENT));
```
  
** Activity task  
有時候需要一次關掉一列表的Task  
例 : 登入後進入主畫面之類的處理  
可以在導頁的Intent中新增flag  
```java
intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK | Intent.FLAG_ACTIVITY_CLEAR_TASK);  
```

FLAG_ACTIVITY_NEW_TASK -> 開啟一個新的Task  
FLAG_ACTIVITY_CLEAR_TASK -> 清除現有的Task  