---
layout:post
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

```
public String getFormmatedCreateDate() {
		if(createDate == null)
			return "";
		return createDate.format(DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm:ss"));
	}
```
자바에서 날짜에 관한 형식은 **LocalDateTime**을 참조한다.   
**LocalDate createDate = LocalDate.now();** 현재시간을 나타낸다.
#

```
{{formmatedCreateDate}}
```
이제 get을 제외한 부분에서 맨 앞에를 소문자를 만들고 넣으면 시간이 정상적으로 출력된다.

# 5-2. 질문 상세보기 기능

```
@GetMapping("/{Id}")
	public String show(@PathVariable Long Id, Model model) {
		Question question = questionRepository.findById(Id).get();
		model.addAttribute("question", question);
		
		return "/qna/show";
	}
```
**질문을 상세하게 보고싶다는 의미는?**   
질문을 클릭 시, 해당 내용을 불러오는 것이다. 따라서 Repositories에서 저장되어 있는 정보를 불러와서 model에 추가하고 사용한다.

# 5-3. 질문 수정, 삭제 기능 구현

**질문을 수정, 삭제한다는 의미는?**   
질문을 수정하면, Repositories에서 해당 내용을 불러와 update하고 다시 저장, 삭제는 해당 Repositories의 기록을 delete하면된다. 

```
<input tpye="hidden" name="_method" value="put" />
...
//게시글 수정을 눌렀을 때
	@PutMapping("/{id}")
	public String update(@PathVariable Long Id, String title, String contents) {
		Question question = questionRepository.findById(Id).get();
		question.update(title,contents);
		questionRepository.save(question);
		return String.format("redirect:/questions/", Id);
	}
```
input type를 hidden으로, value를 put으로 해두면, form태그 완료 시, PutMapping으로 넘어온다.   
**form action의 주소로 @GetMapping을 사용하거나, input hidden put을 @PutMapping으로 사용 할 수 있는가?**
Id를 이용해 저장소에서 객체를 불러온다음, update시키고 다시 저장한다.
#

```
<input type="hidden" name="_method" value="delete" >
...
//게시글 삭제를 눌렀을 때
	@DeleteMapping("/{Id}")
	public String delete(@PathVariable Long Id) {
		questionRepository.deleteById(Id);
		return "redirect:/";
	}
```
input type을 hidden으로 value를 delete로 설정하고 **@DeleteMapping**을 사용한다. 딱히 Delete라고 다른건 없다. 직접 삭제해주어야 한다.   
**Annotation으로 @DeleteMapping은 따로 delete 기능이 있는 것이 아닌 개발자 입장에서 편하게 확인하기 위해 명시적인가?**   
Id를 이용해 저장소에 있는 객체 중, 해당 Id(primary key)를 가진 정보를 삭제한다.   

@GetMapping("/{Id}") @DeleteMapping("/{Id}") @PutMapping("/{Id}") 모두 같은 url을 가리키지만, html파일에서 value="put" , value="delete"로 설정했기에 가능하다. 하지만 계속 Method Not Allowed라는 오류르 발생시켜서 PostMapping으로 일괄처리했다.

# 5-4. 수정/삭제 기능에 대한 보안 처리 및 LocalDateTime 설정
```
@ManyToOne
	@JoinColumn(foreignKey = @ForeignKey(name ="fk_question_writer"))
	private User writer;
```
5-1 설명처럼, User class에서 객체 관계를 만들지 않고, Question class에서 객체의 관계를 만들어주고 있다. Question 테이블에 User 타입으로 writer를 추가해 주고 있다. 누가 질문을 올렸는지 검증하는 과정에서 사용된다. 
#

```
public String getFormattedCreateDate() {
		if(createDate == null)
			return "";
		return createDate.format(DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm:ss"));
	}
```
get메소드를 사용해서 현재 시간을 계산하는 String 문자열을 반환한다. createDate은 5-1에서와 같이 LocalDate.now()으로 선언되어 있으며, Java의 라이브러리에 정의된 코드를 사용하여 날짜를 표시하도록 한다. mustache에서 사용할 때, **{{formattedCreateDate}}** 라고 쓰면, 사용 가능하다.
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
{{formattedCreateDate}}를 사용할 수 있는 이유는 Question에 createDate를 포함시켜서 객체를 만들기 때문에 , {{#question}}에 createDate라 포함되어 있어서 가능하다.
#

```
return "/users/loginForm"; (X)
return "redirect:/users/loginForm"; (O)
```
Controller로 Mapping을 하는 경우 무조건 redirect:를 쓰자! 그렇지 않을 때 오류 발생

```
public boolean isSameWriter(User loginUser) {
		// TODO Auto-generated method stub
		return this.writer.equals(loginUser);
	}
```
writer와 loginUser 모두 User 타입이다. 하지만, 이 메소드는 Question.java에 선언되어 있다. **equals를 정확하게 해 주기 위해 User에 equals를 재정의 해주고 primary key인 Id만 비교하면 된다.**
#

# 5-5. 답변 추가 및 목록 기능 구현

**답변이 달린다면, 로그인여부와 해당 글은 어느 글인지 확인할 필요가 있다**

QuestionController에서 Answer의 Repository를 호출하고 메시지를 보낼 수도 있지만 Question.class에서 해결한다.
#

```
<Answer.java>
//여러개의 Answer이 Question에 달릴 수 있다.
	@ManyToOne
	@JoinColumn(foreignKey = @ForeignKey(name ="fk_answer_to_question"))
	private Question question;
...
<Question.java>
//하나의 질문은 많은 답변을 가진다.
	@OneToMany(mappedBy="question").
	@OrderBy("Id ASC")
	private List<Answer> answers;
```
Answer.java에서는 여러개의 답변이 하나의 질문에 달릴 수 있다. "그 해당 질문"이 **mappedBy**를 통해 가리켜지고 @OneToMany로 서로 반대가 되는 것이다. Id값이 오른차순으로 정렬되고, answers들은 list에 담긴다. **Question의 객체가 생성된다면, 해당 글의 Answer가 매핑되었으므로 Answer가 달릴때마다 자동으로 추가되어 있다. 따라서 다른 사이트로 나갔다가 다시 들어가도 댓글들을 표시 할 수 있다.**
#

```
//답글등록
@PostMapping("")
	public String create(@PathVariable Long questionId, String contents, HttpSession session) {
		if (!HttpSessionUtils.isLoginUser(session))
			return "redirect:/users/loginForm";

User loginUser = HttpSessionUtils.getUserfromSession(session);
		Question question = questionRepository.findById(questionId).get();
		Answer answer = new Answer(loginUser, question, contents);
		answerRepository.save(answer);
		return String.format("redirect:/questions/%d", questionId);
```
답변을 추가하는 것은 2가지가 필요하다. **User 정보**와 **Question**정보. 현재 로그인한 User정보는 session으로 획득할 수 있다. Question은 질문 번호인 questionId를 매개변수로 넘기기 떄문에 이 고유한 번호로 구분해서 객체를 얻을 수 있다. User정보와 Question 정보를 담은 answer 객체를 생성하고 repository에 저장한다. 내부적으로 저장해 주기 때문에 코드를 구현 할 필요가 없다. **외래키는 다른 릴레이션의 기본키를 참조하는 속성을 말한다.** 즉, 외래키는 다른 테이블에서 유일하게 식별되는 PK이므로 User의 정보는 누가 댓글을 쓰는지 식별하고, 어떤 Question에 다는 글인지 식별한다. 이제 댓글을 달면 User_id , Question_id, Answer_id(답변들을 구분) 총 3개의 ID값을 갖는다.






