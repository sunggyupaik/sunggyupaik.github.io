---
type:post
title:"Spring-Boot-9"
date:2020-04-30
---

# 5-1. 회원과 질문간의 관계 매핑 및 생성일 추가

User 객체는 보통 최상위이며, 따라서 다른 객체들과의 관계는 가능한 한 많이 맺지 않는다. 양이 너무 많다.   
대신 Question에서 User에 대한 관계를 맺는다. **객체 사용시 Get을 사용하지 않기 위한 것이다**   

```
<Question.java>
@ManyToOne
	@JoinColumn(foreignKey = @ForeignKey(name ="fk_question_wirter"))
	private User writer;
```
Question class에서 User의 객체를 사용하고 싶다. 따라서 Question Table에 Join으로 Column을 추가한다. 
foreign key 형태이며, 이름은 fk_question_writer이다. 이제 Question 테이블 생성 시 새로운 칼럼이 추가되어서 생성된다.

```
#

```
public Question(User writer, String title, String contents) {
		super();
		this.writer = writer;
		this.title = title;
		this.contents = contents;
		this.createDate = LocalDateTime.now();
	}
```
이제 생성자에 변화가 생겼다. writer가 String이 아닌 User가 타입이다!
#
public String getFormmatedCreateDate() {
		if(createDate == null)
			return "";
		return createDate.format(DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm:ss"));
	}
```
자바에서 날짜에 관한 형식은 **LocalDateTime**을 참조한다.   
**LocalDate currentDate = LocalDate.now();** 현재시간을 나타낸다.
#

```
{{formmatedCreateDate}}
```
이제 get을 제외한 부분에서 맨 앞에를 소문자를 만들고 넣으면 시간이 정상적으로 출력된다.
