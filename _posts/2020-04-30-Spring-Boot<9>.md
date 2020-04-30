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
**LocalDate currentDate = LocalDate.now();** 현재시간을 나타낸다.
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
