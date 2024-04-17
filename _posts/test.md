---
title: "[Docker] 즉시 종료되는 docker contatiner 해결방법"
date: 2024-04-17T08:08:58.856Z
categories:
  - docker
---

안녕하세요.

오늘은 정말 간단하게 docker container를 올리자마자 즉시 종료되는 문제를 해결하는 방법을 알아보겠습니다.

## 들어가며: 시작 후 즉시 종료되는 컨테이너

docker 사용자가 가장 많이 겪는 고통스러운 문제 중 하나는 컨테이너가 시작된 후 즉시 종료되는 경우입니다. 직접 Dockerfile을 빌드하며 **보통은 휴먼 에러에 의해 발생** 하기도 하고 **설정 오류, 리소스 제약 또는 내부의 오류** 로 인해 즉시 종료되는 경우가 있습니다. 이러한 상황에 docker logs 명령어를 통해 확인할 수 있다면 확인할 수 있겠지만 즉시 사라져 버리는 경우에는 확인도 불가해 끔찍한 상황에 놓여 있을 수 있습니다. 이 문제를 해결하려면 docker container가 의도한 대로 실행될 수 있도록 하는 전략을 구현해야 합니다.

## 첫 번째 전략 - 정석적인 방법으로 해결하기

docker run 명령어의 다양한 옵션을 활용하여 **즉시 종료되지 않게** 할 수 있습니다.

```bash
Bashdocker run -it your/image -d
```

> **-it**
> 대화식 모드로 동작시킵니다
> **-d**
> 백그라운드에서 동작시킵니다

### 재시작 정책
--restart의 옵션값을 지정하여 docker container의 재시작 정책을 지정할 수 있습니다.

> **no (Default)**
> 컨테이너를 자동으로 다시 시작하지 않습니다. (기본값)
>
> **always**
> 컨테이너가 중지되면 항상 다시 시작합니다. Docker 데몬이 다시 시작되거나 컨테이너 자체가 수동으로 다시 시작될 때만 다시 시작됩니다.
>
> **on-failure [:max-retries]**
> 오류로 컨테이너가 종료되는 경우에만 컨테이너를 다시 시작합니다. 선택적으로 :max-retries 옵션을 사용하여 횟수를 지정하지 않으면 제한 없이 다시 시작됩니다. 데몬이 다시 시작되어도 컨테이너를 다시 시작하지 않습니다.
>
> **unless-stopped**
> 컨테이너가 중지되면 Docker 데몬이 다시 시작되어도 다시 시작되지 않는다는 점을 제외하면 always 옵션과 유사합니다.

## 두 번째 전략 - 꼼수(?)로 해결하기

### Dockerfile에서 죽지 않게 만들기

아래의 코드를 Dockerfile **최하단** 에 적어주시면 됩니다.

```bash
BashCMD tail -f /dev/null
```

또는

```bash
BashENTRYPOINT ["tail", "-f", "/dev/null"]
```

### Docker-compose.yml에서 죽지 않게 만들기

Dockerfile에서 작성했던 명령어를 docker-compse에 맞게 작성해 주시면 됩니다.

```yaml
Bashversion: '3'

services:
  servicename:
    image: your/image
    command: tail -f /dev/null
```

## 결론
위의 방법을 활용하면 docker container에서 docker image가 종료되지 않고 계속 동작중인 것을 확인할 수 있습니다.  서술한 방법들 외에 다른 방법들도 있지만 제일 간단하게 해결할 수 있는 방법을 알아보았습니다. 중지되는 이유를 찾는게 먼저이지만 오류를 확인할 수 없을 때 이용하시면 될 것 같습니다.

## 마치며
저는 위의 서술한 방법들을 활용해 **ubuntu:latest image** 를 사용하려 했을 때 즉시 종료되는 상황에 적지 않게 당황한 기억이 있습니다. 이 문제를 해결하기 위해 웹서칭을 통해 얻은 기록들이 있어 정리하고자 글을 작성하게 되었습니다. 비슷한 상황에 계신 분들께 조금이라도 도움이 되었으면 좋겠습니다.

글 읽어주셔서 감사합니다.

## 레퍼런스
[Docker.docs](http://docker.docs/ "https://docs.docker.com/config/containers/start-containers-automatically/")

> 오타 혹은 잘못된 내용이 있다면 부담 없이 댓글로 알려주세요 :)
> 문장을 다듬는데 ChatGPT의 도움을 받았습니다.
