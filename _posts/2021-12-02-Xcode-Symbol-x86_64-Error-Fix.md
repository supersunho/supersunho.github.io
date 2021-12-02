---
title: "ld: symbol(s) not found for architecture x86_64"
categories:
  - React
tags:
  - Dev
  - React
  - React Native
  - iOS
  - Xcode
toc: true  
toc_label: "목차"  
toc_icon: "bars"  
toc_sticky: true  
---

React Native에 새로운 모듈 ([react-native-photo-editor](https://github.com/baronha/react-native-photo-editor)) 을 `npm install` 후 `pod install` 을 통해   
iOS에 모듈을 설치를 하였고 동작확인을 위해 `npm run ios`를 실행하는데 오류가 발생되었습니다

## Error Message
```
Undefined symbols for architecture x86_64:
	...생략
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```
---
<div class="notice--primary" markdown="1">
해당 오류는 각자 환경, 모듈에 따라 다르겠지만 구글링을 통해 오류해결법을 검색하였고 많은 오류해결법들을 적용하였지만 해결되지 않았습니다. 여러가지 해결 방법들이 있었고 그 내용들을 한 곳에 정리하기 위해 이 글을 작성합니다.  
</div>

---

## 해결한 방법 
`Xcode > Build Settings > Library Search Path`에 Swift 버전 변경    
`"$(TOOLCHAIN_DIR)/usr/lib/swift-5.0/$(PLATFORM_NAME)"`을  
`"$(TOOLCHAIN_DIR)/usr/lib/swift-5.3/$(PLATFORM_NAME)"`으로 변경하고   
빌드 오류가 해결되고 앱에서 실행되었습니다 

---
이하 시도해봤던 방법들을 공유합니다.

## Solution #1
`Xcode > Build Phases > Link Library With Libraries`에 `JavaScriptCore.framework` 추가

## Result #1
`Build error`

---

## Solution #2
`Development Target`을 변경
```c
  platform :ios, '11.0' # Update this to set a minimum iOS DEPLOYMENT_TARGET
  post_install do |installer|
    installer.pods_project.targets.each do |target|
      target.build_configurations.each do |config|
        config.build_settings.delete 'IPHONEOS_DEPLOYMENT_TARGET'
      end
    end
  end
```
[출처] - https://stackoverflow.com/questions/69321998/undefined-symbols-for-architecture-x86-64-xcode-simulator 

## Result #2
`Build error`

---




