---
type: post
title : "Spring Boot<7>
date : 2020-04-28
---

# 3-3. 새로운 HTML 적용 및 URL 리팩토링

**데이터베이스가 서버 재시작 때마다 초기화 하는 문제를 어떻게 해결 할 것인가?**

**Mapping을 일목요연하게 하는 방법은?**   
@Controller에서 **@RequestMapping**을 클래스에 설정하고 안에 **@GetMapping**과 **@PostMapping**을 설정하면 깔끔하게 정리 가능하다.   
@RequestMapping("/user")을 해놓았다면 **@GetMapping("")  @PostMapping("")**을 하면 알아서 get, post방식 때 해당 메소드로 이동한다.

**중복되는 코드를 줄이는 방법은?**   
중복되는 코드는 mustache에서 제공하는 **>**를 사용한다. 예를 들어 navigation bar는 반복이므로 template 안에 include라는 폴더 안에
navigation bar에 해당하는 코드를 붙여넣고 **>**를 사용해 참조하도록 한다.

**template은 꼭 Controller를 통해서 접근이 되도록 해야한다.**

# 3-4. 개인정보 수정 기능 구현 1

**MVC모델링에서 view에 접근하기 위해서는 꼭 Controller를 거치도록 한다.**

* 정보 수정하기 클릭!

> @GetMapping("/{Id}/form")
우리가 primary key로 설정하였던 Id에 대하여 값을 확인하고 각자의 form으로 넘겨준다.

> public String updateForm(@PathVariable Long Id, Model model) {
@Pathariable를 Id에 설정해, Id를 통해 내가 원하는 정보를 찾겠다는 의미이다.

> User user = userRepository.findById(Id).get();
@PathVariable의 Id는 primary key이므로 해당 Id의 User 정보를 가져온다.

> model.addAttribute("user", user);
model에 해당 정보를 저장해 내가 가고 싶은 페이지에서 mustache 문법으로 조회하게 한다.

**정보를 수정한다는 의미는 수정해야 할 정보가 애초에 넘어왔다는 의미이고, 그것은 Id이다.**   
ex)<td><a href= ** "/users/{{Id}}/form" ** class="btn btn-success" role="button">수정</a></td>


