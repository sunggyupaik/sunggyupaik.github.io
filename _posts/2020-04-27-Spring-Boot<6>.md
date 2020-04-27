---
type:post
title:"Spring Boot 2-2"
date:2020-04-27
---

# 2-2. 회원가입 기능 구현

client의 요청에 항상 상응하는 Controller를 만든다.(@Controller) 그리고 Browser URL과 매핑한다.(@GetMapping)

form 태그 안에 있는 정보를 submit하고 싶으면 name속성을 이용한다.

**form 태그 안에 name과 DAO의 name이 같아야 한다!!!**

# 2-3. 사용자 목록 기능 구현

devtools가 제대로 반영하지 못하는 경우가 있다.. 그럴경우 수동으로 재시작 한다.

# 2-4. 회원가입, 사용자 목록 기능을 원격 서버에 배포하기


>$ git push

>fatal: The current branch master has no upstream branch.

>To push the current branch and set the remote as upstream, use

branch가 있을 경우, 어느 계정으로 push를 할지 정해주어야 한다.
git push + origin master

>./mvnw clean package
clean = 그 전에 있었던 mvnw를 날린다.

package = 새로운 mvnw를 만들어준다.

local에서 spring boot 서버(8080)를 돌리고 있다면, 원격에서 접속할 수 없으므로 8080 포트 사용 여부를 확인한다.

```
ps -ef | grep java
```

현재 실행되고 있는 프로세스 중에 java라는 단어로 시작하는 프로세스 출력 
