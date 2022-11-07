# Section 1. 프로젝트 환경설정
## 프로젝트 생성
## 라이브러리 살펴보기
## View 환경설정
## 빌드하고 실행하기


# Section 2. 스프링 웹 개발 기초
## 정적 컨텐츠
1. 웹 브라우저에서 localhost:8080/hello-static.html 입력
2. 내장 tomcat 서버
3-1. Controller에서 hello-static을 먼저 검색
3-2. 없으면 resources에서 검색
4. 웹 브라우저로 반환

## MVC와 템플릿 엔진
**서버에서 HTML을 변형하여 전달하는 방식**

1. 웹 브라우저에서 localhost:8080/hello-mvc 입력
2. 내장 tomcat 서버
3. Spring 컨테이너에서는 helloController에 hello-mvc가 mapping 되어있음을 확인.
4. helloController에서는 hello-template을 return 하고, model의 name 이라는 key 값에 spring 이라는 value 가 대응함을 알려줌
5. 화면을 찾아주고, 템플릿 엔진을 연결시켜주는 viewResolver에서 hello-mvc의 return 값인 hello-template를 찾아서 Thymeleaf 템플릿 엔진에게 처리를 요청
6. Thymeleaf 템플릿 엔진이 변환한 HTML을 웹 브라우저로 반환

★ 정적 컨텐츠와의 차이 : 템플릿 엔진이 변환하는 과정을 거친 후에 HTML을 웹 브라우저에 번환함.

*컨트롤러와 뷰를 분리하는 것은 기본*
뷰 => 화면과 관련된 일만.
컨트롤러 => 비즈니스 로직 / 서버와 관련된 일 처리

tip : 인텔리제이 인자값 즉시 보기 (Parameter Info) = Ctrl + P

## API
**원하는 데이터 포맷으로 변환하여 전달하는 방식**

@ResponseBody : HTTP 통신 프로토콜에서, 응답 BODY부에 데이터를 직접 입력하겠다.
- xml 방식 : <A></A>
- json 방식 : {key:value}

1. 웹 브라우저에서 localhost:8080/hello-api 입력
2. 내장 tomcat 서버
3. Spring 컨테이너에서는 helloController에 hello-api가 mapping 되어있음을 확인
4. @Responsebody라는 annotation이 있음을 확인. (Spring의 기본정책은 json 방식으로 데이터 반환)
5. @Responsebody를 통해 hello의 name 이라는 key 값에 spring 이라는 value 가 대응함을 알려줌
6. HttpMessageConverter가 동작하여 단순 문자일 경우 StringConverter가 / 객체일 경우 JsonConverter가 동작함
7. 이 코드에서는 @Responsebody를 통해 객체를 받아오기 때문에 JsonConverter가 동작하여 {name:spring}을 반환

※ MVC / 템플릿 엔진과의 차이 :
- HTTP의 BODY에 문자 내용을 직접 반환함
- viewResolver 대신에 HttpMessageConverter가 동작함

참고) HTTP의 Accept 헤더에서 특정 포맷으로 설정해두면, 해당 포맷의 Converter가 동작함.

# Section 3. 회원 관리 예제 - 백엔드 개발
## 비즈니스 요구사항 정리
- 데이터 : 회원 ID와 이름
- 기능 : 회원 등록, 조회
- 데이터 저장소는 X

- 컨트롤러 :  웹 MVC의 컨트롤러
- 서비스 : 핵심 비즈니스 로직
- 리포지토리 : DB에 접근, 도메인 객체를 DB에 저장하고 관리
- 도메인 : 비즈니스 도메인 객체. ex) 회원 정보, 주문 정보 등을 DB에 저장하고 관리함

**클래스 의존관계**
- 데이터 저장소가 없기 때문에, 인터페이스로 구현 클래스 변경 가능
- 개발 진행을 위해 초기 단계에서는 메모리 기반의 저장소 사용

## 회원 도메인과 리포지토리 만들기
**Member.java**
```java
package hello.hellospring.domain;

public class Member {
    private Long id;
    private String name;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

```
**MemberRepository.interface**
```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import java.util.List;
import java.util.Optional;

public interface MemberRepository {
    Member save(Member member);
    Optional<Member> findById(Long id);
    Optional<Member> findByName(String name);
    List<Member> findAll();
}
```

**MemoryMemberRepository.java**
```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import java.util.*;

public class MemoryMemberRepository implements MemberRepository{

    private static Map<Long, Member> store = new HashMap<>();
    private static long sequence = 0L;

    @Override
    public Member save(Member member) {
        member.setId(++sequence);
        store.put(member.getId(), member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        return Optional.ofNullable(store.get(id));
    }

    @Override
    public Optional<Member> findByName(String name) {
        return store.values().stream()
                .filter(member -> member.getName().equals(name))
                .findAny();
    }

    @Override
    public List<Member> findAll() {
        return new ArrayList<>(store.values());
    }
}

```
## 회원 리포지토리 테스트 케이스 작성
## 회원 서비스 개발
## 회원 서비스 테스트

# Section 4. 스프링 빈과 의존관계
## 컴포넌트 스캔과 자동 의존관계 설정
## 자바 코드로 직접 스프링 빈 등록하기
