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


ufw delete deny 8080/tcp : 룰 삭제
