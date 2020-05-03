---
layout:  post
title:  "Spring Boot"
date:  2020-05-03
---

# 6-0. 여섯 번째 반복주기 학습 목표 및 과정 설명

JAVASCRIPT에서 사용하기 위해 만든 JSON. CLIENT와 SERVER 사이에 데이터 공유는 JSON을 사용하는것이 XML보다 편하다.   
즉, 댓글을 달 때 기존에는 전체화면을 다시 구성해서 댓글을 단다. 그러면 비용이 많이 든다. 그럴 때 AJAX 기술을 이용한다.
AJAX를 이용하여 데이터를 JSON 형식으로 주고받고, **댓글을 다는 부분만 동적으로 웹페이지를 수정하여 표시한다.**

# 6-1. AJAX를 활용한 답변 추가 1

```
function addAnswer(e) {
	e.preventDefault();
  ...
```
javascript 파일에서 다음과 같은 함수가 선언되어 있다. e.preventDefault()는 저 함수 실행 시, 서버로 데이터를 전송하는 기능을 막은 것이다. 
우리는 댓글다는 부분만 ajax 기술로 json파일 형식으로 데이터를 주고받을 것이기 때문에 전체 작업을 할 필요가 없어서 ajax를 구현한다.
#

```
@RestController
```
Json 데이터를 처리하기 위해서는 그냥 Controller를 사용하면 안되고 RestController를 사용해야 한다.
#

```
$.ajax({
		type : 'post',
		url:url,
		data:queryString,
		dataType : 'json',
		error : onError,
		success : onSuccess});
```
javascript 파일 안에, 원하는 함수 안에 ajax를 선언하다. ajax는 기본적으로 getter로 정의되어 있는 데이터만 변환시켜 준다.
**1. json의 라이브러리 jackson에서 이용 2. getter 추가** 를 통해서 ajax로 json의 데이터를 호출 할 수 있도록 한다.
#

```
String.prototype.format = function() {
    var args = arguments;
    return this.replace(/{(\d+)}/g, function(match, number) { 
      return typeof args[number] != 'undefined'
        ? args[number] 
        : match
        ;
    });
  };
```
javascript에서 String.format 형식의 데이터를 만들고 싶을 때, 정의하는 함수이다.
#

# 6-2. AJAX를 활용한 답변 추가 2

**@JsonProperty** 설정해야 Json 데이터를 ajax로 주고받을 때 인식 할 수 있다. 
비밀번호 같은 중요한 정보는 넘기면 안되므로 **@JsonIgnore**를 사용하여 Json정보를 넘기지 않도록 한다. 

```
var template = answerTemplate.format(data.writer.userId, data.formattedCreateDate, data.contents, data.id, data.id);
```
정의한 format 함수를 이용하여 내가 넘기고 싶은 data형태를 정렬한다.


```
var template = answerTemplate.format(data.writer.userId, data.formattedCreateDate, data.contents, data.id, data.id);
	$(".qna-comment-slipp-articles").prepend(template);
```
template으로 정보를 만들고나서, 이 정보를 html의 해당 class의 위치에 추가 해 준다. prepend는 append와 다르게 맨 앞에 붙여주는 것이다.   
{1} {2} {3}과 같이 해당 위치에 내가 format으로 설정한 data들이 넘어간다.
#

```
$("textarea[name=contents]").val("");
```
댓글을 달고 나서 댓글 입력란이 초기화 되어주도록 jQuery를 이용하여 textrea 부분을 val("")으로 설정한다.
#

# 6-3. AJAX를 활용해 답변 삭제 기능 구현
```
var deleteBtn = $(this);
var url = deleteBtn.attr("href");
```
내가 삭제버튼을 누르는데 비동기 적으로 처리하고 싶어서 href 주소를 가져오고 싶다. $(this)는 jQuery 언어로, "삭제"를 눌렀을 때 해당 객체를 얻도록 하는 명령어이다. 여기서 attr는 속성을 가져올 수 있으며 attr("href")를 통해 주소를 가져온다. 
```
deleteBtn.closest("article").remove();
```
closest는 가장 가까운 태그를 찾기 위한 것이며, remove()를 통해 댓글을 삭제 할 수 있다.

# 6-4. 질문 목록에 답변 수 보여주기 기능 추가

```
answerRepository.deleteById(Id);
Question question = questionRepository.findById(questionId).get();
question.deleteAnswer();
questionRepository.save(question);
```
목록에 담긴 댓글의 갯수를 보여 주려면 그것은 Question의 객체에 포함시키도록 한다. 그러면, question 객체를 호출해야 하므로 questionId를 통해
특정 question의 객체를 얻어온다. 댓글이 삭제된다면 deleteAnswer로 -1을 해준다. 그리고 꼭 해당 내용을 다시 저장한다.
#

```
question.addAnswer();
```
답글을 새롭게 작성할 때 1을 추가시킨다. Question은 Answer를 포함하고 있음을 기억한다!!!
#

# 6-5. 도메인 클래스에 대한 중복 제거 및 리팩토링

```
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class) 
public class AbstractEntity {
```
부모 추상클래스의 @EntityListeners(AuditingEntityListener.class) 는 Listener에서 createDate, modifiedDate 변경사항을 감지하면 자동으로 설정해준다. 항상 Listener를 하고 있는 상태이다. Entity는 테이블을 의미한다.
#

# 6-6. Swagger 라이브러리를 통한 API 문서화 및 테스트
Swagger는 수동으로 테스트할 수 있는 기능이다!

# 6-7. 쉘 스크립트를 활용한 배포 자동화
####배포순서
1. gitpull   
2. 메이븐 빌드(./mvnw clean package)   
3. tomcat 서버 종료   
4. tomcat/webapps/ROOT 삭제   
5. 빌드한 산출물을 tomcat/webapps/ROOT로 이동   
6. tomcat 서버 시작   
#

```
#!/bin/bash
```
bash 쉘을 기본으로 사용하며 사용하고 싶은 쉘 명령어를 그대로 엔터치면서 기록   
./desploy.sh로 실행!

