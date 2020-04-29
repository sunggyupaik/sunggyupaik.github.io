---
type : post
title: "Spring Boot <8>"
date : 2020-04-29
---

# 4-1. 로그인 기능 구현

```
public String login(String userId, String password, HttpSession Session) {
```
로그인하고 싶다면? 내가 입력한 userId와 password를 form에서 name으로 넘기고 매개변수로 받는다.   
아이디와 비밀번호를 저장해야 하므로 Session을 이용한다.
#
```
User user = userRepository.findByUserId(userId);
```
역시나 내가 원하는 userId를 통해서 해당 user의 내용을 조회해야 하므로 다음과 같은 과정을 따른다.
#
```
public interface UserRepository extends JpaRepository<User, Long>{
	User findByUserId(String userId);
}
```
그러기 위해서는 다음과 같이 interface에서 userId로 조회가 가능하도록 메서드를 추가해야 한다.   
매개변수에 무엇을 넣어야 하는지만 알면 조회하는 것이 가능하다.   
**어떻게 이렇게 쉽게 가능하지?**

#
```
if(user == null) {
			System.out.println("Login Failed");
			return "rediret:/users/loginForm";
		}
```
user의 정보가 없는경우 돌아가!
#
```
if(!password.equals(user.getPassword())) {
			System.out.println("Login Failed");
			return "rediret:/users/loginForm";
		}
```
비밀번호가 다른 경우에도 돌아가!
#
```
System.out.println("Login Success");
		Session.setAttribute("user", user);
		return"redirect:/";
```
**로그인 정보가 맞다면 Session.setAttribute()를 통해 Session에 저장!**
#

# 4-2. 로그인 상태에 따른 메뉴 처리 및 로그아웃

**spring-boot에서는 apllication.properties에 여러 설정을 하면 구현하지 않고 이용할 수 있는 장점이 있으므로 공식 문서 확인**   
ex)mustache를 통해 session을 사용하려면 어떻게 해야 하는가?   

**어떻게하면, static/index.html이 아니라 templates/index.html로 시작할 수 있을까?**

```
@Controller
@RequestMapping("/")
public class PageController {
	public String index() {
		return "index";
	}
}
```
시작이 static의 index.html이므로, template에 있는 index.html이 시작위치에 있도록 지정한다.

```
@GetMapping("logout")
	public String logout(HttpSession session) {
		session.removeAttribute("user");
		return "redirect:/";
	}
```
**로그 아웃의 의미는?**   
Session을 제거해 주는 것이다.
#

# 4-3. 자기 자신에 한해 개인정보 수정
```
@GetMapping("/{Id}/form")
	public String updateForm(@PathVariable Long Id, Model model, HttpSession session) {
		Object tempUser = session.getAttribute("sessionedUser");
		System.out.println(tempUser);
		if(tempUser==null) {
			return "redirect:/users/loginForm";
		}
```   
개인정보 수정 주소에 들어갈 때, URL주소가 Id(primay key)로 구분되어진다. 그래서 개인 정보수정을 클릭할 때, 로그인 된 Session의 Id와 내가 접속한 URL의 Id가 같은지 확인한다. 그렇지 않으면, 다른 계정으로 로그인하고 URL에서 남의 Id를 입력하고 개인정보를 수정할 수도 있기 때문이다.   
session.getAttribute는 반환형이 **Object**이므로 그 상황에서 비교해준다.
#
```
User sessionUser = (User) tempUser;
		if(!sessionUser.getId().equals(Id)) {
			throw new IllegalStateException("자신의 정보만 수정 할 수 있습니다.");
		}
```
Object형 tempUser를 User로 형변환하고, 로그인 된 session의 Id와 접속하려는 URL의 Id와 비교한다.(primary key이므로 구분 가능)
#

```
return "redirect:/users/loginForm"      
return "/user/login"
```
첫번째는 Controller의 @RequestMapping인 users에 **다시 주소를 보낸다**는 것이고,   
두번째는 templates 디렉토리 안에 잇는 **user/login 파일을 띄운다**는 의미이다.   
#

**백엔드 개발자는 내가 로그인 하지 않는 경우 정보수정 등 잘못된 접근을 하지 못하도록 꼭 보안을 잘 신경쓰고 코딩해야 한다.**   
#

```
public String update(@PathVariable Long Id, User updatedUser, HttpSession session) {
...   

User user = userRepository.findById(Id).get();
		user.update(updatedUser);
		userRepository.save(user);
```
개인정보 수정 후 완료를 누를 시,   
다음과 같이 Id로 원래 저장되어 있던 정보들을 불러 올 수 있다. findById(Id)로 가능하다. 이후 매개변수로 주어진 updatedUser의 정보들로 바꿔준다.(update)  그리고 저장소에 그 값들을 저장한다. 

**form 태그 안에서 name명과 User의 변수들의 이름을 맞춰 주면, update()의 매개변수에 User updatedUser가 자동으로 그 값을 읽어 올 수 있는걸까? 그래서 user.update(updatedUser)가 가능한 것일까?** 

# 4-4. 중복 제거, clean code, 쿼리 보기 설정

jpa는 테이블이 자동으로 생성되어서, 쿼리가 내가 원하는대로 실행되는지 안되는지 알 수가 없다. 어떻게 하면 쿼리를 볼 수 있을까?   
application.properties에서 수정..   
```
if (!password.equals(user.getPassword())) {
	System.out.println("Login Failed");
	return "rediret:/users/loginForm";
}
		
// 비밀번호가 다르면 다시 로그인창
if (!user.matchPassword(password)) {
	System.out.println("Login Failed");
	return "rediret:/users/loginForm";
}
```
위에 있는 코드는 password를 알기 위해서 직접 접근하는 경우이다. 이것은 객체지향프로그래밍의 **캡슐화**에 저해되는 것이니 하지 않고 아래를 이용한다. 우리는 단지 user에게 비밀번호가 맞는지 아닌지 메시지를 전달하며 matchPassword()라는 메소드를 통해서 확인해 줄 것을 요청하고 있다. 
#

# 4-5. 질문하기, 질문 목록 기능 구현

```
public Question() {}
```
**jpa에서는 default 생성자를 꼭 만들어놓아야 다른 생성자를 만들 수 있다.**
#

```
@Autowired
private QuestionRepository questionRepository;
```
**@Autowired는 해당 Repository의 값을 할당하라는 의미이다.** Autowired없이 그냥 선언만 하면 null 상태이다!   
Interface만 선언해도, 그 안에 **구현체와 isntance는 모두 Spring이 생성해 주고 있기 때문에** 사용자는 구현 내용을 알 필요가 없다.
#

```
public String create(String title, String contents, HttpSession session) {
...
User sessionUser = HttpSessionUtils.getUserfromSession(session);
```
매개변수로 받아온 session을 이용하여 현재 로그인 되어있는 session의 정보를 얻어온다.
#
```
Question newQuestion = new Question(sessionUser.getUserId(), title, contents);
```
매개변수로 받아온 String과 contents, session에서 불러온 값중에 sessionId를 이용해 Question 테이블에 넣기 위해 객체를 만든다.
#
```
questionRepository.save(newQuestion);
```
questionRepository에서 제공하는 save 기능을 통해 테이블 객체를 저장한다.
#
```
public interface QuestionRepository extends JpaRepository<Question, Long>{

}
```
Question 테이블을 저장할 수 있도록 QuestionRepository는 JpaRepository를 상속받고 있기 때문에 save가 가능하다.


