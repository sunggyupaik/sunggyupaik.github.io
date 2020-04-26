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
