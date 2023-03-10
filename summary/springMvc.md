Spring MVC
- DispatcherServlet
  - 이전 버전들에서 진행한 프론트 컨트롤러 역할
  - 스프링 MVC의 핵심,,
  - 부모 클래스에서 HttpServlet을 상속받음
  - 스프링 부트는 DispatcherServlet을 서블릿으로 자동으로 등록,, 모든 경로에 대해서 매핑한다.
    - 자세한 경로가 우선순위가 높으므로 기존에 등록한 서블릿도 함께 동작
  - 요청 흐름
    - 서블릿이 호출되면 HttpServlet의 service 호출,,
    - DispatcherServlet의 부모인 FrameworkServlet의 오버라이드된 service 메서드 실행
    - 메서드 호출되며 doDispatch()가 호출,,
      - 핸들러 찾고,, 없으면 404 (컨트롤러)
        - Url 뿐만아니라 Http 헤더 등 추가 정보 활용
      - 핸들러 어댑터를 조회
        - 핸들러를 실행 할 수 있는 어댑터 조회
      - 핸들러 어댑터 실행 >> 핸들러 어댑터를 통해 핸들러 실행
      - ModelAndView 반환
      - ViewResolver 호출
        - 뷰의 논리이름을 물리이름으로 바꾸고
        - 렌더링 역할을 담당하는 뷰 객체 반환
      - View 반환
      - 뷰 렌더링 : JSP, 타임리프등을 활용하여 HTML로 변환
    - 스프링은 해당 코드들을 거의 다 인터페이스로 제공하여 JSP용, 타임리프용 등으로 확장성이 용이하게 제공함
  
초창기 Spring MVC
- 컨트롤러 인터페이스를 사용,,
- HandlerMapping(핸들러 매핑) 
  - 핸들러 매핑에서 이 컨트롤러를 찾을 수 있어야 한다.
  - 스프링 부트에서 구현된 핸들러 매핑 종류중 대표적인 2가지,,
    - 0 = RequestMappingHandlerMapping : 애노테이션 기반의 컨트롤러인 @RequestMapping에서 사용
    - 1 = BeanNameUrlHandlerMapping : 스프링 빈의 이름으로 핸들러를 찾는다. 
- HandlerAdapter(핸들러 어댑터)
  - 핸들러 매핑을 통해 찾은 핸들러를 실행할 수 있는 핸들러 어댑터
  - 스프링 부트에서 구현된 핸들러 어댑터 종류중 대표적인 3가지,,
    - 0 = RequestMappingHandlerAdapter : @RequestMapping에서 사용
    - 1 = HttpRequestHandlerAdapter : HttpRequestHandler 처리
    - 2 = SimpleControllerHandlerAdfapter : Controller 인터페이스 (과거) 처리
  - 뷰 리졸버
    - 논리경로를 물리경로로 변경해주는 객체
    - 스프링 부트에서 구현된 뷰 리졸버 중 대표적인 2가지
      - 1 = BeanNameViewResolver : 빈 이름으로 뷰를 찾아서 반환
      - 2 = InternalResourceViewResolver : JSP를 처리할 수 있는 뷰를 반환
- 전체적인 프로세스
  - 핸들러 매핑으로 핸들러 조회
    - HandlerMapping을 순서대로 실행해서 핸들러를 찾음 
    - 빈 이름으로 조회(BeanNameUrlHandlerMapping) >> OldController 반환
  - 핸들러 어댑터 조회
    - HandlerAdapter의 supports()를 순서대로 호출
    - SimpleControllerHandlerAdapter가 OldController을 내부에서 실행
    - 실무에선 RequestMapping만 사용하기 때문에 해당 RequestMapping 핸들러와 어댑터 모두 우선수위가 가장 높음

현재 Spring MVC
- 애노테이션 기반으로 동작
- V1
  - @Controller
    - 자동으로 스프링 빈에 등록(@Component 때문에)
    - 스프링 MVC에서 애노테이션 기반 컨트롤러로 인식
  - @RequestMapping
    - 요청 정보를 매핑,, (해당 url 호출시 해당 메서드 호출)
    - 애너테이션 기반이기 때문에 메서드는 상관없음
  - ModelAndView : 모델과 뷰 정보를 담아서 반환한다
- V3
  - Model 파라미터
    - save,members에서 받는 Model 파라미터,, 
    - Spring에서 제공하는 Model 사용 가능
  - ViewName 직접 반환
    - 뷰의 논리 이름을 반환 가능(String으로)
  - @RequestParam
    - HTTP 요청 파라미터를 받을 수 있다.
    - @RequestParam("username')은 request.getParameter("username")과 같은 코드
    - GET 쿼리 파라미터, POST Form 방식 모두 지원
  - @RequestMapping -> @GetMapping, @PostMapping
    - Spring 4.3부터 지원,,