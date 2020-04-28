---
type: post
title : "Spring Boot<7>
date : 2020-04-28
---

# 3-3. 새로운 HTML 적용 및 URL 리팩토링

**데이터베이스가 서버 재시작 때마다 초기화 하는 문제를 어떻게 해결 할 것인가?**

**Mapping을 일목요연하게 하는 방법은?**   
@Controller에서 **@RequestMapping**을 클래스에 설정하고 안에 **@GetMapping**과 **@PostMapping**을 설정하면 깔끔하게 정리 가능하다.   
@RequestMapping("/user")을 해놓았다면 **@GetMapping("")**  **@PostMapping("")**을 하면 알아서 get, post방식 때 해당 메소드로 이동한다.

**중복되는 코드를 줄이는 방법은?**   
중복되는 코드는 mustache에서 제공하는 **>**를 사용한다. 예를 들어 navigation bar는 반복이므로 template 안에 include라는 폴더 안에
navigation bar에 해당하는 코드를 붙여넣고 **>**를 사용해 참조하도록 한다.

**template은 꼭 Controller를 통해서 접근이 되도록 해야한다.**
