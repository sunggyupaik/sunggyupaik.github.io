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
**로그인 정보가 맞다면 Session.setAttribute()를 통해 Session에 저장!
