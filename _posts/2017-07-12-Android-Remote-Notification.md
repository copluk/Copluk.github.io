---
layout: post
title: Android Remote Notification
category: Android
tags: [Android, Notification]
---
# 前言  

手機端的通知有兩種，一種是本地端發出的通知，  
另一種是由伺服器發出的通知\(以下簡稱為推播  

本地端的通知，比較即時，不需等待，  
但是難以控制該發送的時間。  
另外需要隨時監控App的狀態，  
會造成電力容易耗損之類的問題。[^1]  

所以在實作上會配合推播實作，  
推播可以由伺服器告知手機端，  
所以可以在伺服器有資料更新時發送。[^2]  
但因為有許多因素會影響伺服器與手機間的溝通，  
所以推播本身並不保證推送的成功與時間。  

本文會介紹實作推播功能時，  
Android手機端應配合的設定，  
Server部份的設定，  
以及手機端幾種可行的作法。[^3]  

# 推播設定  

請至 [https://console.firebase.google.com/](https://console.firebase.google.com/)  建立專案，  
並註冊Android專案後，依照Firebase教學進行設定。  
接著在build.gradle\(Moudle:app\)的dependencies內  
加上 :  
compile 'com.google.firebase:firebase-messaging:10.0.0'  
\(版本號請自行修正  

然後覆寫Firebase的兩個Service，  
分別是FirebaseMessagingService 、 FirebaseInstanceIdService  
並在Manifast加上對應的資訊。[^4]  

# 推播Payload  

在使用FCM時，Android會依照特定的Payload，執行不同的動作。  
雖然Payload是Server向FCM發送請求時所設定的格式，  
但是Android需要做相對應的處理，以下是格式範例。  
可參考 : https://firebase.google.com/docs/cloud-messaging/http-server-ref#notification-payload-support  

```js
{
	"registration_ids" : ["bk3RNwTe3H0:CI2k_HHwgIpoDKCIZvvDMExUdFQ3P1...","......"],
		//"to" : "bk3RNwTe3H0:CI2k_HHwgIpoDKCIZvvDMExUd1...",
	"notification" : {
		"body" : "Message Body",
		"title" : "Notification Title"
		},
	"data" : {
		"data_key" : "data_value",
		"data_key2" : "data_value2"
		}
}
```  

在進行推播時，需要設定發送的PushToken以及推播的內容。  

## PushToken  
### registration_ids :  
可設定多筆PushToken，一次上限1000筆。  
### to :  
可設定單筆PushToken，與registration_ids只與設定其中一個。  

## 推播內容  
### notification :  
FCM的預設格式，App在背景時，  
可以依照不同的設定修改手機端通知的行為。  
如 :  
tag : 未設定時，可以不覆蓋以往的推播。  
設定任意值時，可取代相同tag的推播。  

### data :  
可以自定義的推播內容，物件裡需填入自定義的keyValue。  
收到推播時，  
會在FirebaseMessagingService內的onMessageReceived取得資料，  
之後可以用資料進行相對應的處理。  
***App在背景時，如果有設定notification的值，**  
**App不會啟動，會直接用notification的設定發送推播。**  

---------------------


[^1]: 如 : 體力制的手遊，在體力滿的時候跳出通知

[^2]: 如 : 即時通訊軟體、新信件等…

[^3]: 本文使用FCM \(Firebase Cloud Message\) 做為範例的推播服務提供者。

[^4]: 可以參考Google提供的資源 : [https://github.com/firebase/quickstart-android/tree/master/messaging/app/src/main](https://github.com/firebase/quickstart-android/tree/master/messaging/app/src/main) 
