---
title: "AAPT: error: resource android:attr/lStar not found"
categories:
  - React
tags:
  - Dev
  - React
  - React Native
  - Android
---
최근 Android 정책으로 인해 `targetSdkVersion`를 `30`으로 변경해서 기존 앱을 빌드하는 과정에서 Build Error가 발생되었습니다. 해결하는데 시간을 오래 소비했고 너무 허무하게 해결되어 방법을 공유드립니다.

## Error Message

```shell
AAPT: error: resource android:attr/lStar not found
```

## Solution 1

> `compileSdkVersion`과 `targetSdkVersion`를 `31`로 변경 
  
   
## Solution 2

> `app/build.gradle`에 `androidx.core`버전을 `1.6.0`으로 고정

```javascript
androidx.core:core-ktx:+ ==> androidx.core:core-ktx:1.6.0
```

또는

```java
ext {
    buildToolsVersion = "29.0.2"
    minSdkVersion = 21
    compileSdkVersion = 29
    targetSdkVersion = 29
    androidXCore = "1.6.0"
}
```
 