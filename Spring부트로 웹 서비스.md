**@SpringBootApplication**

- 스프링 부트의 자동 설정, 스프링 Bean 읽기와 생성을 모두 자동으로 설정.
- 특히 이 어노테이션 위치부터 설정을 읽어가기 때문에 이 클래스는 항상 프로젝트의 최상단 위치.



**SpringApplication.run** : 

run으로 내장 WAS를 실행한다.

내장 WAS를 사용해야 '언제 어디서나 같은 환경에서 스프링 부트를 배포'할 수 있다.



**@RestController**

- 컨트롤러를 JSON을 반환하는 컨트롤러로 만들어 줍니다.
- @ResponseBody를 각 메소드마다 선언했던 것을 한번에 사용할 수 있게 해준다.



**@RunWith**

- 테스트를 진행할 때 JUnit에 내장된 실행자 외에 다른 실행자를 실행시킵니다.
  @RunWith(SpringRunner.class)



**@WebMvcTest**

- 스프링 테스트 어노테이션 중 Web(Spring MVC)에 집중할 수 있는 어노테이션.



**@Autowired**

- 스프링 컨테이너에서 만들어지는 객체 = 스프링 빈(bean). Autowired는 이 bean을 주입 받는다.



**MockMvc mvc** 

웹 API를 테스트할 때 사용, 스프링 MVC 테스트의 시작점.



mvc.perform(get(urlTemplate: "/hello")) // MockMvc를 통해 /hello 주소로 HTTP GET 요청을 한다.

.andExpect(status().isOk()) // 200, 404, 500 상태를 검증한다. 여기선 Ok() 즉, 200인지를 검증.

.andExpect(content().string(hello)); // 응답 본문 내용을 검증한다.

**param** 

- API 테스트할 때 사용될 요청 파라미터를 설정합니다.
- 단, 값은 String만 허용되므로, 숫자/날짜 등의 데이터를 문자열로 변경해야한다.
  String.valueOf(변수)

**jsonPath**

- JSON 응답값을 필드별로 검증할 수 있는 메소드.
- $를 기준으로 필드명을 명시한다. 
  ex) $.name, $.amount



**@RequestParam**

- 외부에서 API로 넘긴 파라미터를 가져오는 어노테이션.
- 외부에서 name(@Requestparam("name"))이란 이름으로 넘긴 파라미터를
  메소드 파라미터 name(String name)에 저장하게 됩니다.



<hr/>

## 롬복(lombok)

롬복은 자바 개발할 때 자주 사용하는 코드 Getter, Setter, 기본생성자, toString 등을 어노테이션으로 자동 생성해 줍니다.

build.gradle에 compile('org.projectlombok:lombok') 이라고 코드를 작성해 의존성 추가.

혹은, 버전에 따라서 implementation 'org.projectlombok:lombok' 



**@Getter**

- 선언된 모든 필드의 get 메소드를 생성해 줍니다.

**@RequiredArgsConstructor**

- 선언된 모든 final 필드가 포함된 생성자를 생성해 줍니다.



<hr/>

###### 관계형 데이터베이스가 계속해서 웹 서비스의 중심이 되면서 모든 코드는 SQL 중심이 되어간다.

###### 현업에서 수십, 수백 개의 테이블이 있는데, 단순 반복적인 SQL이 애플리케이션 코드보다 많아지는 현상이 발생되었다.

그래서 JPA가 등장.

#### * JPA는 객체를 매핑하는 자바 표준 ORM(Object Relational Mapping) 기술이다.

반면, MyBatis는 쿼리를 매핑하는 SQL Mapper로 ORM이 아니다.

**스프링 부트에서 JPA로 데이터베이스를 다뤄보자!**



## JPA

서로 지향하는 바가 다른 2개 영역(객체지향 프로그래밍 언어와 관계형 데이터베이스)을 중간에서 **패러다임 일치**를 시켜주기 위한 기술.

즉, 개발자는 객체지향적으로 프로그래밍을 하고, JPA가 이를 관계형 데이터베이스에 맞게 SQL을 대신 생성해서 실행합니다. 

객체 중심으로 개발을 하게 되니 생산성 향상은 물론 유지 보수가 편하다.

**저장소 교체의 용이성** : 단순히 **의존성** 교체로, 관계형 데이터베이스 외에 다른 저장소로 쉽게 교체할 수 있다.



**@Entity**

- JPA 어노테이션으로, 테이블과 링크될 클래스임을 나타낸다. 테이블 이름을 매칭한다.

**@Id**

- 해당 테이블의 PK 필드를 나타냅니다.

**@GeneratedValue**

- PK의 생성 전략을 명시하는데 사용.
- 선택적 속성으로 generator와 strategy가 있다.
  - **generator**는 <u>SequenceGenerator</u>나 <u>TableGenerator</u> 어노테이션에 명시된 주키 생성자를 재사용할 때 쓰인다. 디폴트는 공백문자("").
  - **strategy**는 엔티티의 주키를 생성할 때 사용해야 하는 <u>주키생성 전략</u>을 의미한다. 디폴트는 **AUTO**
    - IDENTITY : 기본 키 생성을 데이터베이스에 위임하는 방법. (MySQL etc..)
    - SEQUENCE : 데이터베이스 시퀀스를 사용해서 기본 키를 할당하는 방법. (H2 etc..)
    - TABLE : 키 생성 전용 테이블을 하나 만들고 여기에 이름과 값으로 사용할 컬럼을 만듬.
    - AUTO : 데이터베이스에 따라서 IDENTITY, SEQUENCE, TABLE 방법 중 하나를 자동으로 선택해주는 방법.
- 스프링 부트 2.0 에서는 GenerationType.IDENTITY 옵션을 추가해야만 **auto_increment**가 된다.









