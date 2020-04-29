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
개인정보 수정 시에 사이트 URL주소가 Id(primay key)가 들어가 있다. 그래서 개인 정보수정을 클릭할 때, 로그인 된 Session의 Id와 내가 접속한 URL의 Id가 같은지 확인한다. 그렇지 않으면, 다른 계정으로 로그인하고 URL에서 남의 Id를 입력하고 개인정보를 수정할 수도 있기 때문이다.   
session.getAttribute는 반환형이 **Object**이므로 그 상황에서 비교해준다.

```
User sessionUser = (User) tempUser;
		if(!sessionUser.getId().equals(Id)) {
			throw new IllegalStateException("자신의 정보만 수정 할 수 있습니다.");
		}
```
Object형 tempUser를 User로 형변환하고, 로그인 된 session의 Id와 접속하려는 URL의 Id와 비교한다.(primary key이므로 구분 가능)
