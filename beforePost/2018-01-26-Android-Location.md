* 行前準備  
本文使用Google Location API
Project 層級的gradle內請確認

allprojects {
    repositories {
        google()
    }
}

gradle內加入
dependencies {
compile "com.google.android.gms:play-services-location:11.8.0"
}

在Manifest內加入
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

