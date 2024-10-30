---
title: "[Javascript] DynamicsCompressorNode를 통해 Video의 소리를 풍부하게 만들기"
description: null
author: supersunho
categories:
  - Dev
  - Javascript
tags:
  - Audio
  - DynamicsCompressorNode
  - Javascript
  - Video
  - Sound
date: 2024-10-30T04:32:55.556Z
toc: true
comments: true
pin: false
math: false
mermaid: false
render_with_liquid: false
image:
  path: https://i.imgur.com/vfiplwB.png
---
![](https://i.imgur.com/vfiplwB.png)

안녕하세요.

오늘은 **브라우저에서 재생 중인 Video의 소리를 압축해 음질을 풍부하게 만드는 방법**에 대해 알아보도록 하겠습니다.

## 들어가며
실시간 스트리밍을 통해 재생되는 사이트에서 **배경음이 너무 커서 목소리가 잘 들리지 않는 경우**를 경험해보신 적이 있으실 겁니다. 저의 경우, 헤드셋이나 이어폰을 통해 연결하면 그런 경우가 적지만, 스피커를 통해 재생하면 다른 소리에 목소리가 묻혀 들리지 않아 소리를 키우면 배경음도 함께 커져 조금 답답한 경우가 있었습니다. 이를 `DynamicsCompressorNode`를 통해 쉽게 개선하는 방법을 알려드리려고 합니다.

## DynamicsCompressorNode?
`DynamicsCompressorNode`는 하나의 입력과 하나의 출력을 갖는 Audio Node로, 여러 사운드가 동시에 재생될 때 발생할 수 있는 클리핑 및 왜곡을 방지하기 위해 **신호의 가장 큰 부분의 볼륨을 낮추는 압축 효과**를 제공합니다. 

## 압축 효과
`DynamicsCompressorNode`는 다음과 같은 효과를 제공합니다.
>가장 큰 소리의 볼륨을 낮추고 **가장 작은 소리의 볼륨을 높입니다**
<br />전체적으로 **더 크고 풍부한 사운드**를 만듭니다
<br />여러 개의 개별 사운드가 **동시에 재생되는 게임이나 음악 애플리케이션에서 특히 유용**합니다
<br />전체 신호 레벨을 제어하고 **오디오 출력의 클리핑(왜곡)을 방지**하는 데 도움이 됩니다

## DynamicsCompressorNode 생성
먼저 AudioContext를 생성하고 createDynamicsCompressor() 메서드를 사용하여 `DynamicsCompressorNode`를 만듭니다.
```javascript
const audioCtx = new AudioContext();
const compressor = audioCtx.createDynamicsCompressor();
```

## 압축 매개변수 설정
`DynamicsCompressorNode`의 주요 매개변수를 설정하여 압축 동작을 제어할 수 있습니다.
>**threshold**
<br/>압축이 시작되는 데시벨 값 (기본값: -24 dB)
<br/>**knee**
<br/>압축 곡선의 부드러운 전환 범위 (기본값: 30 dB)
<br/>**ratio**
<br/>입력 대 출력의 압축 비율 (기본값: 12)
<br/>**attack**
<br/>압축이 적용되는 시간 (기본값: 0.003초)
<br/>**release**
<br/>압축이 해제되는 시간 (기본값: 0.25초)

스크립트로 작성하면 아래와 같습니다.
```javascript
compressor.threshold.setValueAtTime(-50, audioCtx.currentTime);
compressor.knee.setValueAtTime(40, audioCtx.currentTime);
compressor.ratio.setValueAtTime(12, audioCtx.currentTime);
compressor.attack.setValueAtTime(0, audioCtx.currentTime);
compressor.release.setValueAtTime(0.25, audioCtx.currentTime);
```

## 오디오 소스 연결
현재 재생중인 **Video를 Compressor에 연결**하고, **Compressor를 출력 대상에 연결**합니다.
```javascript
const source = audioCtx.createMediaElementSource(audioElement);
source.connect(compressor); // Compressor 연결
compressor.connect(audioCtx.destination); // Compressor를 출력대상과 연결
```

## 압축 활성화/비활성화
필요에 따라 압축을 켜고 끌 수 있습니다. 모든 곳에서 사용하려면 if구문을 제거하세요.
```javascript
function toggleCompression(isActive) {
  if (isActive) { // 활성화
    source.disconnect(audioCtx.destination);
    source.connect(compressor);
    compressor.connect(audioCtx.destination);
  } else { // 비활성화
    source.disconnect(compressor);
    compressor.disconnect(audioCtx.destination);
    source.connect(audioCtx.destination);
  }
}
```

## 전체 코드
```javascript
const audioCtx = new AudioContext();
const source = audioCtx.createMediaElementSource(audioElement);
const compressor = audioCtx.createDynamicsCompressor();

// compressor 설정
compressor.threshold.setValueAtTime(-50, audioCtx.currentTime);
compressor.knee.setValueAtTime(40, audioCtx.currentTime);
compressor.ratio.setValueAtTime(12, audioCtx.currentTime);
compressor.attack.setValueAtTime(0, audioCtx.currentTime);
compressor.release.setValueAtTime(0.25, audioCtx.currentTime);

// 노드 연결
source.connect(compressor);
compressor.connect(gainNode);
```

## 마치며
위의 스크립트를 활용해 Tampermonkey와 같은 Script 실행 확장프로그램을 통해 적용하면 풍부한 소리를 들을 수 있습니다. 이를 확장해서 UI를 구현하고 값을 입맛에 맞게 조절하는 확장프로그램을 구현할 수도 있을거라 생각합니다.

긴 글 읽어주셔서 감사합니다.