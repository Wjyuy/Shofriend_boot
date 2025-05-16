# 🛍️ 친구와 함께 쇼핑해요! SHOFRIEND

**Spring Boot 기반 온라인 상품 판매 플랫폼**
<br>

빅데이터 기반(파이썬·자바·웹) 엘라스틱 검색엔진 개발자과정 `2025. 01. 15 ~ 2025. 07. 10`

<br>

1조 3차 프로젝트 진행 기간 `2025. 05. 07 ~ 2025. 05. 14`

## 프로젝트 포스터

![포스터](./img/poster.PNG)

## 💡 프로젝트 선정 배경

본 프로젝트는 **소셜 네트워킹 서비스(SNS)의 연결성과 커뮤니티 기능**과 **온라인 쇼핑몰의 편리한 상품 구매 경험**을 융합하여 새로운 형태의 온라인 플랫폼을 구축하고자 하는 아이디어에서 시작

- 친구의 쇼핑 경험을 공유, 함께 상품을 추천, 관심사를 기반으로 새로운 관계를 형성, 공동 구매를 통해 혜택을 누리는 등 기존 쇼핑몰에서는 경험하기 어려웠던 **소셜 쇼핑의 가치**를 제공

- **SNS와 쇼핑몰의 강점을 결합**하여 사용자들에게는 더욱 풍부하고 즐거운 온라인 쇼핑 경험을 제공하고 판매자에게는 새로운 마케팅 및 판매 채널 제시

![shofriend](./img/shofriend.png)

## 기술 구상도

![기술구상도](./img/programpic.png)

  ### 사용 기술 스택
  * **Backend:** Spring Boot
  * **Database:** MySQL
  * **ORM:** MyBatis
  * **Version Control:** Git, Sourcetree
  * **Build Tool:** Gradle
  * **Web Server:** Tomcat
  * **Communication:** Slack, Jira

## 설계

### DB diagram을 사용한 erd 테이블 설계
![erd](./img/erd.png)

### 업무 흐름도
<table>
  <tr>
    <td align="center">메인</td>
    <td align="center">마이페이지</td>
    <td align="center">장바구니</td>
  </tr>
  <tr>
    <td><img src="./img/flow1.jpeg" alt="flow1" width="300"></td>
    <td><img src="./img/flow2.jpeg" alt="flow2" width="300"></td>
    <td><img src="./img/flow3.jpeg" alt="flow3" width="300"></td>
  </tr>
</table>

### 서비스 설계단 흐름
<table>
  <tr>
    <td align="center">사용자</td>
    <td align="center">친구</td>
  </tr>
  <tr>
    <td><img src="./img/userflow.png" alt="사용자 흐름도" width="400"></td>
    <td><img src="./img/friendflow.png" alt="친구 흐름도" width="400"></td>
  </tr>
</table>

## Spring Boot에서 구현한 Spring Security구현 과정

### 기본 설정 및 구성

1.  **Spring Boot 프로젝트 생성**
    *   **Project**: Gradle
    *   **Language**: Java
    *   **Spring Boot**: 최신 버전
    *   **Dependencies**: 'Spring Web', 'Spring Security'
    *   **Generate** 버튼 클릭하여 프로젝트 생성
    *   생성된 프로젝트를 IDE (Eclipse)로 불러온다

2.  **Spring Security 설정**
    *   **기본 보안 설정** : `application.properties`에 기본 보안 설정을 추가(기본 로그인 페이지를 비활성화하거나 특정 경로에 대한 인증 설정)

        ```properties
        # application.properties 파일 예시
        spring.security.user.name=user
        spring.security.user.password=password
        ```

    *   **Custom Security Configuration** (본 프로젝트에서는 적용하지 못 했지만, 더 복잡한 보안 설정은 `SecurityConfig` 클래스를 작성해 세부 설정 커스터마이징 가능 : 특정 URL 패턴 접근 제어, 사용자 정의 로그인 페이지 설정 등)

        ```java
        import org.springframework.context.annotation.Bean;
        import org.springframework.context.annotation.Configuration;
        import org.springframework.security.config.annotation.web.builders.HttpSecurity;
        import org.springframework.security.config.annotation.web.builders.AuthenticationManagerBuilder;
        import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
        import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
        import org.springframework.security.core.userdetails.User;
        import org.springframework.security.core.userdetails.UserDetailsService;
        import org.springframework.security.provisioning.InMemoryUserDetailsManager;

        @Configuration
        @EnableWebSecurity
        public class SecurityConfig extends WebSecurityConfigurerAdapter {

            @Override
            protected void configure(HttpSecurity http) throws Exception {
                http
                    .authorizeRequests()
                    .antMatchers("/public/**").permitAll() // 공용 경로 
                    .anyRequest().authenticated() // 나머지 요청은 인증 필요 
                    .and()
                    .formLogin()
                    .loginPage("/login")
                    .permitAll()
                    .and()
                    .logout() 
                    .permitAll();
            }

            @Override
            @Bean
            public UserDetailsService userDetailsService() {
                UserDetailsService userDetailsService = new InMemoryUserDetailsManager(); // 인메모리 사용자 정보 서비스 
                userDetailsService.createUser(User.withUsername("user").password("{noop}password").roles("USER").build()); // 사용자 생성
                return userDetailsService;
            }
        }
        ```
        위 예제에서는 `/public/**` 경로는 인증 없이 접근 가능, 나머지 경로는 인증 요구.

3.  **사용자 인증과 권한 부여** : `UserDetailsService` 구현하여 사용자 정보 제공 ( 다음 프로젝트에서는 데이터베이스와 연동하여 볼 예정)

    *   **UserDetailsService 예제** :

        ```java
        import org.springframework.security.core.userdetails.UserDetails;
        import org.springframework.security.core.userdetails.UserDetailsService;
        import org.springframework.security.core.userdetails.UsernameNotFoundException;
        import org.springframework.stereotype.Service;
        import org.springframework.security.core.userdetails.User; // User 클래스

        @Service
        public class CustomUserDetailsService implements UserDetailsService {

            @Override
            public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
                // DB 로직
                if ("user".equals(username)) { // "user" 이름 처리 
                    return User.withUsername("user")
                               .password("{noop}password") // {noop} 필요 
                               .roles("USER")
                               .build();
                } else {
                    throw new UsernameNotFoundException("User not found"); // 사용자 없을 시 예외 발생 
                }
            }
        }
        ```
        예제는 사용자 이름이 "user"인 경우만 처리하며, 실제 환경에서는 데이터베이스에서 사용자 정보를 조회해야 한다

### 에러 처리 및 해결 방법

*   **에러: 403 Forbidden**
    *   **해결 방법**:
        1.  `HttpSecurity` 설정에서 URL 패턴, 권한 설정 확인
        2.  로그인 페이지나 로그인 처리 올바른지 확인
        ```java
        @Override protected void configure(HttpSecurity http) throws Exception {
            http
                .authorizeRequests()
                .antMatchers("/public/**").permitAll() // 확인
                .anyRequest().authenticated() // 확인
                .and()
                .formLogin()
                .loginPage("/login") // 확인
                .permitAll()
                .and()
                .logout()
                .permitAll();
        }
        ```
## Spring legacy -> Spring boot 마이그레이션 과정

  ### 1. 스프링 부트 의존성 추가

  *   Gradle 프로젝트 : `build.gradle` 파일에 스프링 부트 플러그인 및 관련 의존성 추가
      *   `id 'org.springframework.boot' version '2.7.13'` 버전 다운
      *   `implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.3.1'` 버전 다운
      *   `implementation 'org.springframework.boot:spring-boot-starter-web'` 추가
      *   `implementation 'javax.servlet:jstl:1.2'` 추가

  레거시의 servlet-context.xml 

  ```xml

	<!-- Resolves views selected for rendering by @Controllers to .jsp resources in the /WEB-INF/views directory -->
	<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<beans:property name="prefix" value="/WEB-INF/views/" />
		<beans:property name="suffix" value=".jsp" />
	</beans:bean>
	
	<context:component-scan base-package="com.lgy.ShoFriend" />
	
	<!-- 	dataSource 객체는 데이터베이스 정보 포함 -->
	<beans:bean name="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<beans:property name="driverClassName" value="com.mysql.cj.jdbc.Driver"></beans:property>
		<beans:property name="url" value="jdbc:mysql://localhost:3306/atom"></beans:property>
		<beans:property name="username" value="bts"></beans:property>
		<beans:property name="password" value="1234"></beans:property>
	</beans:bean>

	<beans:bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<beans:property name="dataSource" ref="dataSource"></beans:property>
		<!-- 		sql 로 구성된 xml 경로 설정 -->
		<beans:property name="mapperLocations" value="classpath:com/lgy/ShoFriend/dao/mapper/*.xml"></beans:property>
	</beans:bean>
	
	<!-- 	SqlSessionTemplate 타입의 sqlSession 객체는 sqlSessionFactory 객체를 포함한다. -->
	<beans:bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
		<beans:constructor-arg index="0" ref="sqlSessionFactory"></beans:constructor-arg>
	</beans:bean>	
	
	<!-- 	2025.04.08 파일입출력 우주연 -->
	<beans:bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<beans:property name="defaultEncoding" value="UTF-8"/>
		<beans:property name="maxUploadSize" value="10485760"/>
	</beans:bean>
  ```

  변경된 Gradle 설정
  ```gradle
  plugins {
    id 'java'
  //	id 'org.springframework.boot' version '3.4.4'
    id 'org.springframework.boot' version '2.7.13'
    id 'io.spring.dependency-management' version '1.1.7'
  }
  dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    //	implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:3.0.4'
    implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.3.1'
    implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
    implementation 'javax.servlet:jstl:1.2'
    implementation group: 'net.coobird', name: 'thumbnailator', version: '0.4.20'
    implementation 'org.bgee.log4jdbc-log4j2:log4jdbc-log4j2-jdbc4.1:1.16'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    runtimeOnly 'com.mysql:mysql-connector-j'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter-test:3.0.4'
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    //25.05.08 홍길동 websocket 의존성 추가 (채팅 기능에 필요)
    implementation 'org.springframework.boot:spring-boot-starter-websocket'
    //25.05.08 홍길동 카카오페이용 의존성 추가 
    implementation 'org.apache.httpcomponents:httpclient'
    implementation 'com.fasterxml.jackson.core:jackson-databind'
  }
  ```

## 🚀 주요 기능

### 사용자

  * **🔍 (헤더) 상품 검색:** 헤더의 검색창을 통해 원하는 상품을 무슨 페이지에서든 검색
  * **👤 (헤더) 로그인 및 회원 정보:** 사용자 인증 및 개인 정보 관리  
  * **🛒 (헤더) 장바구니:** 관심 있는 상품을 담고 한 번에 결제
  * **💳 결제:** 카카오페이를 통한 결제 시스템 지원
  * **⭐ 리뷰:** 상품에 대한 사용자들의 후기를 확인하고, 작성
  * **🏷️ 카테고리:** 카테고리별 상품 목록 탐색, 검색, 정렬, 페이징

### 판매자

  * **🏪 점포 등록,수정:** 판매자 자신의 상점 개설, 정보 관리
  * **📝 상품 리스트 관리:** 등록된 상품 목록 확인, 수정, 삭제

### 소셜

  * **🧑‍🤝‍🧑 친구 추가 및 삭제:** 사용자 사이에 친구를 맺고 관리
  * **💬 친구 채팅:** 친구와 실시간 채팅 지원
  * **🎁 친구 추천 상품:** 친구가 구매한 상품기반 맞춤형 상품 추천
  * **🎁 친구 상품 공유:** 상품 링크 공유

## ⏳ 추후 구현 예정

다음과 같은 기능들이 추가로 구현될 예정

### 소셜 기능 강화

  * **🤝 공동구매:** 친구와 함께 상품을 구매가능, 할인 혜택 등 지원
  * **👀 실시간 친구 쇼핑:** 친구가 현재 어떤 상품을 보고 있는지 실시간으로 확인 (예: "OO님이 이 상품을 보고 있어요!")
  * **🧑‍🤝‍🧑 관심사 기반 친구 추천:** 사용자의 관심사를 분석해 새로운 친구 추천
  * **🔔 알림 기능:** 친구 활동, 이벤트 정보, 상품 업데이트 등 다양한 알림 제공
  * **Wish List 공유:** 서로의 Wish List를 확인하고 관심사를 공유

### 사용자 편의 기능 강화

  * **🔑 소셜 로그인:** Google, Kakao 등 다양한 플랫폼 계정을 통해 간편하게 로그인할 수 있도록 지원
  * **❤️ 찜 기능:** 관심 있는 상품을 찜 목록에 추가, 찜 목록을 기반으로 개인화된 상품 추천(추천 알고리즘 로직 적용 예정)
  * **🎁 개인화된 상품 추천 강화:** 사용자들의 구매, 검색, 찜 목록 등을 분석하여 더욱 정확하고 다양한 상품을 추천

### 보안 강화

  * **🛡️ 보안 기능 강화:** Spring Security, JWT (JSON Web Token) 등을 활용해 사용자 데이터, 시스템 보안강화 예정

## 협업툴 자동화 활용

  <table>
    <tr>
      <td align="center">slack</td>
      <td align="center">jira</td>
    </tr>
    <tr>
      <td><img src="./img/slack.png" alt="slack" width="400"></td>
      <td><img src="./img/jira.png" alt="jira" width="400"></td>
    </tr>
  </table>

## 결과

  ### 메인 화면
  ![메인](./img/main.png)
  ![상품](./img/product.png)
  ### 상품 상세
  ![상세](./img/content.png)
  ### 리뷰
  ![리뷰](./img/review.png)
  ### 친구
  ![friend](./img/friend.png)
  ![chat](./img/chat.png)
  ### 결제
  ![cart](./img/cart.png)
  ![checkout](./img/checkout.png)
  ![kakaopay](./img/kakaopay.png)

## 📜 프로젝트 후기 

  * **팀장(우주연):** 구현하고 싶었던 기능들이 많은데 전부 다 완성하지 못해서 아쉬웠지만, 제작한 백앤드 기능들이 계획한대로 동작하게끔 만들어져 얻어가는게 많은 프로젝트라고 생각됩니다. 다음 프로젝트에는 ai를 활용한 기능들을 추가해 보고 싶고, 로그인 기능을 만들게 된다면 Spring Security를 활용한 보안을 적용해 보고 싶습니다!
  * **팀원(권준우):** WebSocket을 이용한 채팅 기능을 처음 시도해봤는데, 잘 작동되도록 구현한것 같아 만족스럽습니다. 시간이 부족해 초반에 계획했던 보안쪽 설계 시도를 하지 못해 아쉬었지만, 프로젝트 자체는 수업시간에 배웠던 대부분의 기능을 직접 다루고 적용해 볼 수 있어서 많이 배울 수 있는 기회가 되었습니다!
  * **팀원(김채윤):** 구현하고 싶었던 기능들이 많았지만 모두 완성하지 못해 아쉽습니다. 그러나 친구와 함께 쇼핑한다는 초기 기획과 함께 일반적인 웹사이트와는 차별화된 웹페이지를 만들 수 있었습니다. 신선한 프로젝트에 참여하여 매우 뜻깊은 시간이었습니다!
  * **팀원(성유리):** 기능 구현을 이행하기에 시간이 걸려, 많이 하지 못한부분은 아쉽습니다.  전 프론트단을 디자인하는데 코드가 너무 많아져 추후 추가 기능, 프론트 쪽은 타입스크립트나 리액트사용 에 집중하고싶습니다 . 팀원들이 잘해주셔서 잘 마무리 한 것 같습니다!

## 🔗 관련 링크
  * **GitHub Repository:** 
  [[팀장 우주연-GitHub Repository URL](https://github.com/Wjyuy/Shofriend_boot)]
  [[팀원 성유리-GitHub Repository URL](https://github.com/yuriuser126/ShoFriend_project3)]
  [[팀원 김채윤-GitHub Repository URL](https://github.com/Chaeyoon-k/pilotproject_03)]
  [[팀원 권준우-GitHub Repository URL](https://github.com/kjo5191/ShoFriend)]
  * **API 문서:** [[카카오페이 API URL](https://developers.kakaopay.com/)]
  * **API 문서:** [[카카오 API URL](https://apis.map.kakao.com/)]
  * **Jira 프로젝트:** [[Jira 프로젝트 URL](https://khproject3.atlassian.net/)]
  * **Slack 채널:** [[Slack 채널 URL](https://khproject3hq.slack.com/)] 

  * **발표자료**

  <a href="./img/pptx.pdf" download="document.pdf">PDF 미리보기</a>
  [최신 발표 자료 다운로드 (릴리스)](https://github.com/Wjyuy/ShoFriend_Final/releases/latest)

  * **시연영상 보기**

  [![Video Label](http://img.youtube.com/vi/2gDThXVyRGg/0.jpg)](https://www.youtube.com/watch?v=2gDThXVyRGg)