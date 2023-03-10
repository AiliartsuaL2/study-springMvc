MVC 패턴
- Model
  - View에 출력할 데이터를 담아둠,, 
  - HttpServletRequest 객체를 사용,, 
  - Request 객체는 요청의 처리부터 끝날때까지 사용자의 파라미터+ 내부에서 사용하는 파라미터를 저장함,,
  - Response 객체는 응답을 만들어 생성하는데에 사용
- View
  - 모델에 있는 데이터를 활용하여 화면에 렌더링 하는 일에 집중
  - webapp/WEB-INF/views/ 의 파일은 직접 조회 불가능,, 반드시 컨트롤러를 거쳐야 조회 할 수 있다.
- Controller
  - HTTP 요청을 받아서 파라미터를 검증하고, 비즈니스 로직을 실행함
  - 비즈니스 로직은 Service에 담아두고, DB는 Repository에서 처리한다.
  - Controller는 HTTP를 받아 비즈니스 로직에 해당하는 Service를 호출만 함. 
- 레거시 MVC의 한계
  - 공통처리가 어려움
    - 중복 코드가 많아 이걸 공통 기능으로 메서드로 뽑아도 해당 메서드를 호출하는것 자체도 중복,,
    - 따라서 중복되는 코드인 수문장 역할을 하는 프론트 컨트롤러 패턴을 도입,,
    - 스프링 MVC의 핵심..

- FrontController 패턴 사용
  - 프론트 컨트롤러 서블릿 하나로 클라이언트의 요청을 받음
  - 프론트 컨트롤러가 요청에 맞는 컨트롤러를 찾아 호출
  - 입구를 하나로 두어 공통 처리가 가능,, 나머지 컨트롤러는 서블릿 사용 x
  - Spring Web MVC의 핵심,, DispatcherServlet이 프론트 컨트롤러 패턴

V1
- 기존 구조를 최대한 유지하며 프론트 컨트롤러를 도입

V2
- View를 분류하여 단순 반복되는 뷰의 로직을 분리함

V3
- 서블릿 종속성 제거
  - 컨트롤러 입장에선 Parameter 정보만 필요하지 서블릿에 종속적인 request, response 객체가 필요하진 않음
  - 따라서 해당 파라미터 정보를 Map으로 대신 넘겨서 컨트롤러가 서블릿 객체를 몰라도 동작 가능,,
  - request 객체를 Model로 사용하여 별도의 Model 객체를 만들어 반환
- 뷰 이름 중복 제거
  - 논리 경로만 반환시킴 (컨트롤러에서 반환하는 뷰의 물리적인 경로만 다 들어갔던 V2) 
    - 'WEB-INF/views/new-form.jsp' -> new-form
    - 해당 논리 이름을 다시 물리로 바꿔야 하는데 이건 viewResolver에서 처리한다.
    - 모든 파일을 변경해야하는데, 변경 지점이 하나만 있으면 좋은 설계!!
  - 

V4
- 단순하고 실용적인 컨트롤러
  - V3이 되면서 구조적으로 잘 되어있으나 항상 ModelView 객체를 생성하고 반환해야하는 부분이 번거로움
    - 좋은 프레임워크는 아키텍쳐도 중요하지만, 개발자가 사용하기 좋아야함,,
  - 컨트롤러가 ModelView를 반환하지않고, ViewName만 반환한다.

V5
- 유연한 컨트롤러
  - 한 프로젝트 내에서 인터페이스를 두개 이상 갖고싶은 경우 기존 코드로는 적용 불가,,
  - 어댑터 패턴
    - 핸들러 어댑터를 통해 프론트 컨트롤러에서 다양한 인터페이스 로직으로 처리 할 수 있게끔 처리
    - 기존에는 컨트롤러를 바로 호출했으나, 이번 버젼부터는 어댑터를 통해서 컨트롤러를 호출해줌
    - 핸들러 어댑터 : 중간에 어댑터 역할을 하는 어댑터 
      - 처리 가능 여부를 먼저 확인하고(어댑터 목록에 컨트롤러 있는지 확인)
      - 가능하면 해당 컨트롤러를 호출해줌.
    - 핸들러 : 컨트롤러의 이름을 더 넓은 범위로 적용