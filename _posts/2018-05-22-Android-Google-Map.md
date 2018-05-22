---
layout: post
title: Android Google Map
category: Android
tags: [Android, GoogleMap, 導航, Marker, Polyline]
description: 介紹Android GoogleMap相關設定。
---
# 前言  

地圖是手機開發中，經常實作的功能。  
而非中國地區主要都使用Google Map(下面都叫他Map好了)，  
所以等等會介紹Android開發裡的Map環境架設，  
和Map中的繪製(Marker、Polyline)  
配合導航API的使用。

# 環境設定

在APP的gradle中加入以下資訊，(使用撰文時最新版本)  
*記得檢查Project Gradle中的  
classpath 'com.google.gms:google-services:xx.xx.xx'版本  
看到報錯可能是你版本還沒跟上，就去找一下當時的版本吧  
```gradle
dependencies {
	.
	.
implementation 'com.google.android.gms:play-services-maps:15.0.1'
	.
}
```

接著在[GoogleAPI Console](https://console.cloud.google.com/apis ) [^1]  
建立一個新專案for Android，  
記得把Maps SDK for Android 、 Directions API設為Enable，  
然後申請這兩個API需要的key。  
完成之後就可以開始準備寫Code了。

# 建立地圖元件

Google很貼心的把Map封裝成一個fragment，  
只要你會用fragment，就會用Map!  
在xml中加入
```xml

 <fragment xmlns:android="http://schemas.android.com/apk/res/android"
            android:name="com.google.android.gms.maps.SupportMapFragment"
            android:id="@+id/map"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"/>

```

然後在Activity中加入 OnMapReadyCallback  
並初始化Map物件

```java
public class MapDirectionsActivity extends FragmentActivity implements OnMapReadyCallback {
	private GoogleMap mMap;

	protected void onCreate(@Nullable final Bundle savedInstanceState) {
		...
		// Obtain the SupportMapFragment and get notified when the map is ready to be used.
			SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager().findFragmentById(R.id.map);
			mapFragment.getMapAsync(this);
		...
	}

	@Override
    public void onMapReady(GoogleMap googleMap) {
		mMap = googleMap;
	}
}
```
到這裡，己經可以得到熟悉的Map畫面了。  

# 其他設定
這個距離太遠，又沒有圖標的Map有點無聊，  
所以接著讓我們加上

```java
@Override
public void onMapReady(GoogleMap googleMap) {
	...
	LatLng startLatLng = new LatLng(25.0470289, 121.515987);
	mMap.addMarker(new MarkerOptions().position(startLatLng).title("start"));
	mMap.moveCamera(CameraUpdateFactory.zoomTo(14));
	mMap.moveCamera(CameraUpdateFactory.newLatLng(startLatLng));
	...
	}
```

到這裡會得到圖標定位在台北車站，  
並合理顯示範圍的地圖。  
詳情請見 [GoogleMap相機與檢視](https://developers.google.com/maps/documentation/android-api/views?hl=zh-tw) [^3] 

顯示層級		| 數值  
------------- |:-----
世界    		| 1
地塊/大陸    	| 5 
城市  			| 10
   街道  		| 15
   建築物  		| 20

# 未完待續--
## Marker Click
## polyline & Click
## Directions API & show

# 備註
在手機裡使用Map是不用收費的，    
但是像是導航……等其他的API，  
在使用上是可能要收費的，  
詳情請見 [GoogleMapAPI收費標準](https://developers.google.com/maps/pricing-and-plans/?hl=zh-tw) [^4]  
---------------------


[^1]: https://console.cloud.google.com/apis Google API Console
[^2]: https://developers.google.com/maps/?hl=zh-tw Google Map API
[^3]: https://developers.google.com/maps/documentation/android-api/views?hl=zh-tw GoogleMap相機與檢視
[^4]: https://developers.google.com/maps/pricing-and-plans/?hl=zh-tw 收費標準
