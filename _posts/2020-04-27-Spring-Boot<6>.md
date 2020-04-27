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

git reset으로 나의 local에서 commit을 되돌리고, 원격에 git push를 했다면 되지 않는다!   
왜냐하면 버전이 구버전이 되었는데 git에서는 이것을 제대로 된 최신업데이트라고 생각하지 않는다.   

따라서 **git push--force** 하면 원격 저장소에 git push가 되고 이전 버전으로 되돌린다. 삭제된 정보는 전혀 남지 않는다.

# 3-0. 세 번째 반복주기 학습 목표 및 과정 설명

#### 데이터베이스에 사용자 데이터 추가
#### 개인정보 수정 기능 구현
#### 질문하기, 질문목록 기능 구현

# 3-1. HTML 템플릿 추가, H2 데이터베이스 설치, 관리툴 확인

STS에서 Import로 maven 항목을 이용해 [web-application-server]를 이동시킨다.   
pom.xml을 통해서 maven이라는 도구가 eclipse 버전으로 만들어준다.

**H2 데이터베이스를 다운받는 방법은?**
mvnrepository 사이트에서 dependency를 복사해 pom.xml에 넣어준다.!

# 3-2. 자바 객체와 테이블 매핑, 회원가입 기능 구현   
JPA 설정 상, 서버를 재시작하면 저장해 두었던 자료들이 다 날라간다.!!
