---
layout: post
title: "Spring-Boot<5>"
date: 2020-04-26
---

**javac의 위치를 알고 싶다면?**


which javac

**/usr/bin/javac는 심볼릭 링크이다. 어떻게 해야 심볼릭 링크에 있는 파일의 위치를 알 수 있을까?**


readlink -f /usr/bin/javac

**java 환경변수 설정 방법은?**

vi /etc/profile에 들어가서 export JAVA_HOME= "파일경로 위치",  source /etc/profile


**echo의 의미는?**

echo $JAVA_HOME , echo를 통해 환경변수의 위치를 알 수 있다.


**linux에서 webserver를 가동시키는 방법은?**

maven build 이후 jar 파일 위치로 이동해서 java -jar xxxxx.jar


**하지만 인터넷이 제대로 가동되지 않는 이유는?**

기본적으로 8080 port는 방화벽으로 막혀있기 때문에 방화벽 설정을 해주어야 한다.

**방화벽 설정 방법과 코드는?**

ufw status : 방화벽 상태 확인


ufw enable : 방화벽 활성화

ufw disable : 방화벽 비활성화


ufw allow 8080/tcp : 허용하기

ufw deny 8080/tcp : 거부하기


ufw delete deny 8080/tcp : deny 룰 삭제
ufw delte allow 8080/tcp : allow 룰 삭제

(aws ec2같은 경우 인바운드 규칙에서 수정해야함)

# 2-0. 두 번째 반복주기 학습목표 및 과정 설명

#### 동적인 HTML 웹 페이지 개발
#### Spring MVC의 Model, View, Controller 기반 개발

#### Controller 추가 및 mustache에 인자 전달
#### 회원가입 페이지 구현
#### 원격 서버에 소스 코드 배포
#### 이전 상태로 완복 후 반복 구현

**git에서 이전 상태로 되돌리는 기능은?**

branch를 이용해 다시 코드공부와 입력을 반복하도록 한다.

branch를 만든다는 것은? 지금 내가 가지고 있는 정보들을 다른 저장소에 복제한다는 것일까? 
그렇다면 따른 저장소를 생성해야겠지? 그 저장소만 따로 git push를 할 수는 없을까?(이러면 git에서는 어떻게 인식할까)

git branch <이름> : 새로운 branch 생성
git checkout <이름> : 해당 branch로 이동
git branch : branch 계정명 확인

mustache를 이용하고 싶으면 무조건 Controller가 선행!

Controller에서 return에 해당하는 단어가 자동으로 .html을 붙여서 templates에서 찾는다.

html을 입력하지 않아도 앞에 제목만 입력하면 mustache는 기본적으로 html파일로 만든다. 
