---
layout: post
title: Android Local Notification
category: Android
tags: [Android, Notification]
---

# 前言

在 Android 行動開發裡，通知是一個經常被使用的功能，  
通知在程式進入背景時，也可以成功發送，  
所以常被使用在提示使用者、或是相關的訊息提醒。  
例 : 體力制的遊戲在體力滿時的通知、養成類的通知使用者收成等…  

通知在實作上，  
經常會使用 CloudMessage[^1] 加上 本地端[^2] 的通知實現。  
例 : 收到推播時，用本地端通知客製支援特定頁面跳轉的通知。  

以下內容，主要會介紹如何由本地端發出通知，  
並介紹數種常見的使用情境。  
至於推播相關的實作，請見[Remote Notification](/android/2017/07/12/Android-Remote-Notification/)。  


# 使用的Class

NotificationCompat.Builder[^3] --  用來定義通知實際行為的類別，如Icon、Title、內容、聲響之類的參數。  

Notification -- 用來建構通知的實體物件。  

NotificationManager -- 用來管理通知的發送狀態\(新通知、清除通知等\)。  

# 基礎操作流程

1. 利用NotificationCompat.Builder 定義 Notification 的各項屬性。  
其中 setSmallIcon、setContentTitle、setContentText這三個方法，  
是定義一則通知最基礎的方法，Google的官方文件中也指出必須定義這三個方法。  

2. 使用NotificationCompat.Builder.build\(\)取得Notification的物件。  

3. 利用NotificationManager.notify\(\)將通知發送出去。  

如此，就可以發出一則最基本有圖有標題有內容的通知訊息了。如以下範例 :  

```java
notificationBuilder
	.setSmallIcon(R.drawable.yourNoticeIcon) //設定圖示
	.setContentTitle(noticeTitle) //設定標題
	.setContentText("Notification ID : " + String.valueOf(notifiID)); //設定內容

	//利用NotificationManager.notify()發送通知
	//傳入推播ID ,
	//NotificationCompat.Builder.build()建立通知物件
	manager.notify( notifiID , notificationBuilder.build());
```

# 範例用法

* 多筆通知
有些情況，會需要顯示多筆的通知，而Android也有保留這樣的彈性，  
只要在NotificationManager.notify\(\) 發送通知時，  
給予不同的NotificationID就可以不覆蓋其他的通知，  
達成多筆通知的效果。  

```java
notificationBuilder
	.setSmallIcon(R.drawable.yourNoticeIcon) //設定圖示
	.setContentTitle(noticeTitle) //設定標題
	.setContentText("Notification ID : " + String.valueOf(notifiID));//設定內容

//NotificationCompat.Builder.build()建立通知物件
notification = notificationBuilder.build();

//利用NotificationManager.notify()發送通知
//傳入推播ID
manager.notify( notifiID , notification);

//更新推播ID
notifiID++;
```

* 顯示多欄資訊
在發送通知的時候，如果只設定了setContentText，  
能讓使用者看到的訊息就只有短短的一欄。  
如果有要給予更多的訊息在同一條通知時，  
可以使用NotificationCompat.InboxStyle的類別，  
讓訊息得以放下多行訊息。  

```java
NotificationCompat.InboxStyle inboxStyle =
	new NotificationCompat.InboxStyle();

//利用inboxStyle定義比平常通知更多的資訊
inboxStyle.addLine("Line : " + 1) //加一欄資訊
	.addLine("Line : " + 2) //加一欄資訊
	.setBigContentTitle("BigContentTitle") //設定多欄資訊的標題
	.setSummaryText("+111"); //設定通知的額外資訊，型別為String

//利用NotificationBuilder定義通知屬性
notificationBuilder
	.setSmallIcon(R.drawable.ic_launcher)
	.setContentTitle(notifiTitle)
	.setContentText("InboxStyle ID : " + "-1")
	.setStyle(inboxStyle); //設定InboxStyle

notification = notificationBuilder.build(); //產生通知
manager.notify(notifiID, notification); //發送通知
```

* 自定義通知樣式
有時候內建的通知訊息不足以滿足需求，  
例如媒體播放器需要開始及暫停的功能之類的。  
這時候Android也可以使用自定義的樣式，  
只要使用setContent或setCustomBigContentView，  
並把需要的View轉換成RemotoViews[^4]，  
並傳入方法，就可以完成自定義的通知。  

```java
//建立自定義通知畫面，第二個參數為使用的layout元件
RemoteViews remoteViews = 
	new RemoteViews(getPackageName(), R.layout.notification_lauge_view);

notificationBuilder
	.setSmallIcon(R.drawable.ic_launcher)
	.setContentTitle(notifiTitle)
	.setContentText("InboxStyle ID : " + String.valueOf(retoveViewNotifiID))
	.setContent(remoteViews) //設定未全部展開的通知樣式
	.setCustomBigContentView(remoteViews); //設定展開的通知樣式

notification = notificationBuilder.build(); //建立通知
manager.notify(retoveViewNotifiID, notification); //發送通知
```

如果需要點擊時，也可以對RemotoViews，設定PendingIntent[^5]，  
如此可以設定點擊RemotoViews之後的動作。  

* 取消使用者手動移除通知、設定通知的警示程度  
在使用Android時，應該不難發現，有時候通知並不能被手動移除，  
或是盡量會卡在通知列靠上方的位置。  
其實這兩項都是Notification.Builder時，  
可以設定的參數。  

```java
notificationBuilder
	.setSmallIcon(R.drawable.ic_launcher)
	.setContentTitle(notifiTitle)
	.setContentText("InboxStyle ID : " + String.valueOf(retoveViewNotifiID))
	.setOngoing(true) //設定通知為持續性的通知，使用者不可左右滑動清除通知
	.setPriority(Notification.PRIORITY_MAX);//提高通知的優先程度，讓通知盡量靠上
```

* 其他設定  
如果在實作上希望利用Led、震動、或是聲響來提醒使用者，  
可以在NotificationBuilder中設定，使用的方法分別是 :  

	* 設定Led - setLights  
	* 設定震動 - setVibrate  
	* 設定聲響 - setSound  
詳情請見 : [https://developer.android.com/reference/android/app/Notification.Builder.html](https://developer.android.com/reference/android/app/Notification.Builder.html)

----------------------

# 其他注意事項

* ## 關於UI設計相關  
* 通知小Icon的大小，請參照 :  
[https://developer.android.com/guide/practices/ui\_guidelines/icon\_design\_status\_bar.html](https://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)  

* 另外關於設計風格，請背景留白\(透明\)，Android會將中間的圖示以純色顯示。  

* Large Content 高度最高256dp ，請參考下方網址 :
[https://documentation.onesignal.com/v3.0/docs/customize-notification-icons](https://documentation.onesignal.com/v3.0/docs/customize-notification-icons)  

-----------------------

[^1]: 指第三方網路供應商提供的雲端推播服務 如 : Google Fcm、百度推播、阿里巴巴的推播服務等。

[^2]: 指Android App 本身。

[^3]: Android 3.0 之後的類別，用來代替原本什麼都由NotificationManager做定義的推播。

[^4]: 在使用通知時，App 並不保證會存在於前景，所以需要用這個類別對通知的顯示做定義，而不是直接使用View。

[^5]: 詳情請見 [https://developer.android.com/reference/android/widget/RemoteViews.html](https://developer.android.com/reference/android/widget/RemoteViews.html)