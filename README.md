# Spring Boot를 활용한 게시판의 제작
### 목차
1. [프로젝트 이름](#1-프로젝트-이름)
2. [프로젝트 일정](#2-프로젝트-일정)
3. [기술 스택](#3-기술-스택)
4. [프로젝트 구조](#4-프로젝트-구조)
5. [MySQL 연동](#5-mysql-연동)
6. [게시판 CRUD 처리](#6-게시판-crud-처리)
7. [게시글 등록 구현](#7-게시글-등록-구현)
8. [게시글 리스트 구현](#8-게시글-리스트-구현)
9. [게시글 조회 구현](#9-게시글-조회-구현)
10. [게시글 삭제 구현](#10-게시글-삭제-구현)

---
### 1. 프로젝트 이름
* **게시글 등록, 조회, 수정, 삭제가 가능한 게시판**

---
### 2. 프로젝트 일정
* **2021.02.20 :** 프로젝트 생성 및 개발환경 설정

* **2021.02.22 :** MySQL 데이터베이스 연동 및 테스트

* **2021.02.23 ~ 2021.02.24 :** 게시물 CRUD 관련 Mapper 영역 구현 및 테스트

* **2021.02.24 ~ 2021.02.26 :** 게시물 CRUD 관련 Service, Controller 영역 구현 및 테스트

* **2021.02.27 :** 게시물 CRUD 관련 View 영역 구현

* **2021.03.04 ~ 2021.03.05 :** Paging 처리

* **2021.03.08 :** 게시물 검색 기능 구현

* **2021.03.09 ~ 2021.03.10 :** 댓글 관련 Mapper 영역 구현 및 테스트

* **2021.03.11 ~ 2021.03.14:** 댓글 관련 Service, Controller 영역 구현 및 테스트

* **2021.03.15 ~ 2021.03.17 :** 파일 처리 관련 Mapper 영역 구현 및 테스트

* **2021.03.18 ~ 2021.03.22 :** 파일 업로드 관련 Service, Controller 영역 구현 및 테스트

* **2021.03.23 ~ 2021.03.26 :** 파일 다운로드 관련 Service, Controller 영역 구현 및 테스트

* **2021.03.26 ~ 2021.03.30 :** 파일 처리 관련 기존 코드 보완

* **2021.04.02 :** 최종 테스트 및 시연

---
### 3. 기술 스택
* IDE
```
- IntelliJ
```
* Framework
```
- Spring Boot
- MyBatis
```
* Library
```
- Lombok
```
* Template Engine
```
- Thymeleaf
```
* Database
```
- MySQL
```

---
### 4. 프로젝트 구조
<img src="https://user-images.githubusercontent.com/61148914/124562382-ecc9f680-de79-11eb-8863-e4560bec3570.JPG" width="25%">
</br>

**1) src/main/java 디렉터리**   
ㆍ 클래스, 인터페이스 등 자바 파일이 위치하는 디렉터리   
</br>

**2) BoardApplication 클래스**   
ㆍ 해당 클래스 내의 main 메서드는 SpringApplication.run 메서드를 호출해서 웹 애플리케이션을 실행하는 역할을 함   
ㆍ "@SpringBootApplication"은 다음 3가지 애너테이션으로 구성   
|구성 요소|설명|
|---|---|
|@EnableAutoConfiguration|다양한 설정들의 일부가 자동으로 완료됨|
|@ComponentScan|자동으로 컴포넌트 클래스를 검색하고 스프링 애플리케이션 콘텍스트에 빈으로 등록함|
|@Configuration|해당 애너테이션이 선언된 클래스는 자바 기반의 설정 파일로 인식함|
</br>

**3) src/main/resources 디렉터리**   
|구성 요소|설명|
|---|---|
|templates|템플릿 엔진을 활용한 동적 리소스 파일이 위치|
|static|css, fonts, images, plugin, scripts와 같은 정적 리소스 파일이 위치|
|application.properties|WAS의 설정이나, 데이터베이스 관련 설정 등을 지정해서 처리 가능|
</br>

**4) src/test/java 디렉터리**   
ㆍ BoardApplicationTest 클래스를 이용해서 개발 단계에 따라 테스트를 진행할 수 있음   
</br>

**5) build.gradle 파일**   
ㆍ 빌드에 사용이 될 애플리케이션의 버전, 각종 라이브러리 등 다양한 항목을 설정하고 관리할 수 있는 파일   
</br>

---
### 5. MySQL 연동
**1) 데이터 소스 설정**   
ㆍ 스프링 부트에서 데이터 소스 설정 방법은 두 가지가 존재   
ㆍ "@Bean" 애너테이션 또는 "application.properties" 파일을 이용 가능 (이번 프로젝트에서는 후자의 방법을 사용)   
ㆍ src/main/resources 디렉터리의 application.properties 파일에 아래 코드를 입력
<details>
    <summary><b>코드 보기</b></summary>

```
spring.datasource.hikari.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.hikari.jdbc-url=jdbc:mysql://localhost:3306/board?serverTimezone=UTC&useUnicode=true&characterEncoding=utf8&useSSL=false
spring.datasource.hikari.username=username
spring.datasource.hikari.password=password
spring.datasource.hikari.connection-test-query=SELECT NOW() FROM dual
```
</details>

|구성 요소|설명|
|---|---|
|jdbc-url|데이터베이스의 주소를 의미하며, 포트 번호뒤의 board는 생성한 스키마의 이름|
|username|MySQL의 아이디를 의미|
|password|MySQL의 패스워드를 의미|
|connection-test-query|데이터베이스와의 연결이 정상적으로 이루어졌는지 확인하기 위한 SQL 쿼리문|
</br>

**2) 데이터베이스 설정**   
ㆍ configuration 패키지 내 DBConfiguration 클래스를 통해 데이터베이스 설정 완료
<details>
    <summary><b>코드 보기</b></summary>
	
```java
@Configuration
@PropertySource("classpath:/application.properties")
public class DBConfiguration {

	@Autowired
	private ApplicationContext applicationContext;

	@Bean
	@ConfigurationProperties(prefix = "spring.datasource.hikari")
	public HikariConfig hikariConfig() {
		return new HikariConfig();
	}

	@Bean
	public DataSource dataSource() {
		return new HikariDataSource(hikariConfig());
	}

	@Bean
	public SqlSessionFactory sqlSessionFactory() throws Exception {
		SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
		factoryBean.setDataSource(dataSource());
    		factoryBean.setMapperLocations(applicationContext.getResources("classpath:/mappers/**/*Mapper.xml"));
		return factoryBean.getObject();
	}

	@Bean
	public SqlSessionTemplate sqlSession() throws Exception {
		return new SqlSessionTemplate(sqlSessionFactory());
	}
}
```
</details>

|애너테이션 및 메서드|설명|
|---|---|
|@Configuration|해당 애너테이션이 지정된 클래스를 자바 기반의 설정 파일로 인식|
|@PropertySource|해당 클래스에서 참조할 properties 파일의 위치를 지정|
|@Autowired|빈으로 등록된 객체를 클래스에 주입하는 데 사용|
|ApplicationContext|스프링 컨테이너 중 하나로써 빈의 생성과 사용, 관계, 생명 주기 등을 관리|
|@Bean|해당 애너테이션으로 지정된 객체는 컨테이너에 의해 관리되는 빈으로 등록됨|
|@ConfigurationProperties|@PropertySource에 지정된 파일에서 prefix에 해당하는 설정을 읽어들여 메서드에 매핑|
|hikariConfig|커넥션 풀 라이브러리 중 하나인 히카리CP 객체를 생성|
|dataSource|커넥션 풀을 지원하기 위한 인터페이스인 데이터 소스 객체를 생성|
|sqlSessionFactory|데이터베이스 커넥션과 SQL 실행에 대한 중요한 역할을 하는 SqlSessionFactory 객체를 생성|
|setMapperLocations|getResources 메서드의 인자로 지정된 패턴에 포함하는 XML Mapper를 인식하는 |
|sqlSession|마이바티스와 스프링 연동 모듈의 핵심인 sqlSessionTemplate 객체를 생성|
</br>

---
### 6. 게시판 CRUD 처리
**1) 게시판 테이블 생성**   
ㆍ 게시판 테이블은 데이터베이스에 저장될 게시에 대한 정보를 정의한 것   
ㆍ MySQL Workbench을 실행하고 스키마를 생성한 후 아래에 스크립트를 실행
<details>
    <summary><b>코드 보기</b></summary>
	
```
CREATE TABLE tb_board (
    idx INT NOT NULL AUTO_INCREMENT COMMENT '번호 (PK)',
    title VARCHAR(100) NOT NULL COMMENT '제목',
    content VARCHAR(3000) NOT NULL COMMENT '내용',
    writer VARCHAR(20) NOT NULL COMMENT '작성자',
    view_cnt INT NOT NULL DEFAULT 0 COMMENT '조회 수',
    notice_yn ENUM('Y', 'N') NOT NULL DEFAULT 'N' COMMENT '공지글 여부',
    secret_yn ENUM('Y', 'N') NOT NULL DEFAULT 'N' COMMENT '비밀글 여부',
    delete_yn ENUM('Y', 'N') NOT NULL DEFAULT 'N' COMMENT '삭제 여부',
    insert_time DATETIME NOT NULL DEFAULT NOW() COMMENT '등록일',
    update_time DATETIME NULL COMMENT '수정일',
    delete_time DATETIME NULL COMMENT '삭제일',
    PRIMARY KEY (idx)
)  COMMENT '게시판';
```
</details>
</br>

**2) 도메인 클래스 생성**   
ㆍ 도메인 클래스는 위에서 생성한 게시판 테이블에 대한 구조화 역할을 함   
ㆍ 보통 도메인 클래스는 읽기 전용을 의미하는 xxxVO와 데이터의 저장 및 전송은 의미하는 xxxDTO로 네이밍을 함   
ㆍ domain 패키지에 BoardDTO 클래스를 추가하고 아래에 코드를 작성
<details>
    <summary><b>코드 보기</b></summary>
	
```java
@Getter
@Setter
public class BoardDTO {

	private Long idx;

	private String title;

	private String content;

	private String writer;

	private int viewCnt;

	private String noticeYn;

	private String secretYn;

	private String deleteYn;

	private LocalDateTime insertTime;

	private LocalDateTime updateTime;

	private LocalDateTime deleteTime;
}
```
</details>
</br>

**3) Mapper 인터페이스 생성**      
ㆍ Mapper 인터페이스는 데이터베이스와 통신 역할을 함   
ㆍ mapper 패키지에 BoardMapper 인터페이스를 생성하고 아래 코드를 작성
<details>
    <summary><b>코드 보기</b></summary>
	
```
@Mapper
public interface BoardMapper {

	public int insertBoard(BoardDTO params);

	public BoardDTO selectBoardDetail(Long idx);

	public int updateBoard(BoardDTO params);

	public int deleteBoard(Long idx);

	public List<BoardDTO> selectBoardList();

	public int selectBoardTotalCount();
}
```
</details>
	
|구성 요소|설명|
|---|---|
|@Mapper|마이바티스는 인터페이스에 @Mapper만 지정을 해주면 XML Mapper에서 메서드의 이름과 일치하는 SQL 문을 찾아 실행해줌|
|insert|게시글을 생성하는 INSERT 쿼리를 호출하는 메소드|
|selectBoardDetail|하나의 게시글을 조회하는 SELECT 쿼리를 호출하는 메소드|
|updateBoard|게시글을 수정하는 UPDATE 쿼리를 호출하는 메소드|
|deleteBoard|게시글을 삭제하는 DELETE 쿼리를 호출하는 메소드|
|selectBoardList|게시글 목록을 조회하는 SELECT 쿼리를 호출하는 메소드|
|selectBoardTotalCount|삭제 여부가 'N'으로 지정된 게시글의 개수를 조회하는 SELECT 쿼리를 호출하는 메소드|
</br>

**4) 마이바티스 XML Mapper 생성**   
ㆍ XML Mapper는 BoardMapper 인터페이스와 SQL문의 연결을 위한 역할이며, 실제 SQL 쿼리 문이 정의됨     
ㆍ src/main/resources 디렉터리에 mappers 폴더 생성 후 BoardMapper.xml 파일을 추가   
ㆍ BoardMapper.xml 파일에 아래에 소스코드를 작성
<details>
    <summary><b>코드 보기</b></summary>
	
```sql
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.board.mapper.BoardMapper">

	<sql id="boardColumns">
		  idx
		, title
		, content
		, writer
		, view_cnt
		, notice_yn
		, secret_yn
		, delete_yn
		, insert_time
		, update_time
		, delete_time
	</sql>

	<insert id="insertBoard" parameterType="BoardDTO">
		INSERT INTO tb_board (
			<include refid="boardColumns" />
		) VALUES (
			  #{idx}
			, #{title}
			, #{content}
			, #{writer}
			, 0
			, IFNULL(#{noticeYn}, 'N')
			, IFNULL(#{secretYn}, 'N')
			, 'N'
			, NOW()
			, NULL
			, NULL
		)
	</insert>

	<select id="selectBoardDetail" parameterType="long" resultType="BoardDTO">
		SELECT
			<include refid="boardColumns" />
		FROM
			tb_board
		WHERE
			delete_yn = 'N'
		AND
			idx = #{idx}
	</select>

	<update id="updateBoard" parameterType="BoardDTO">
		UPDATE tb_board
		SET
			  update_time = NOW()
			, title = #{title}
			, content = #{content}
			, writer = #{writer}
			, notice_yn = IFNULL(#{noticeYn}, 'N')
			, secret_yn = IFNULL(#{secretYn}, 'N')
		WHERE
			idx = #{idx}
	</update>

	<update id="deleteBoard" parameterType="long">
		UPDATE tb_board
		SET
			  delete_yn = 'Y'
			, delete_time = NOW()
		WHERE
			idx = #{idx}
	</update>

	<select id="selectBoardList" parameterType="BoardDTO" resultType="BoardDTO">
		SELECT
			<include refid="boardColumns" />
		FROM
			tb_board
		WHERE
			delete_yn = 'N'
		ORDER BY
			notice_yn ASC,
			idx DESC,
			insert_time DESC
	</select>

	<select id="selectBoardTotalCount" parameterType="BoardDTO" resultType="int">
		SELECT
			COUNT(*)
		FROM
			tb_board
		WHERE
			delete_yn = 'N'
	</select>

</mapper>
```
</details>

|구성 요소|설명|
|---|---|
|&lt;mapper&gt;|해당 태그 namespace 속성에는 SQL 쿼리문과 매핑을 위한 BoardMapper 인터페이스의 경로가 지정|
|&lt;sql&gt;|공통으로 사용되거나 반복적으로 사용되는 테이블의 컬럼을 SQL 조각으로 정의하여 boardColumns라는 이름으로 사용|
|&lt;include&gt;|<sql>태그엥 정의한 boardColumns의 참조를 위해 사용되는 태그|
|parameterType|쿼리문 실행에 필요한 파라미터의 타입을 해당 속성에 지정|
|resultType|쿼리문 실행 결과에 해당하는 타입을 지정|
|파라미터 표현식|전달받은 파라미터는 #{} 표현식을 사용해서 처리|
</br>

**5) 마이바티스 SELECT 컬럼과 DTO 멤버 변수의 매핑**   
ㆍ BoardMapper.xml의 boardColumns SQL 조각은 스네이크 케이스를 사용하고 있고, BoardDTO 클래스의 멤버 변수는 카멜 케이스를 사용   
ㆍ 서로 다른 표현식 사용은 추가 설정을 통해 자동으로 매칭이 되도록 처리가 가능   
ㆍ application.properties 파일 하단에 아래 설정을 추가
<details>
    <summary><b>코드 보기</b></summary>
	
```
mybatis.configuration.map-underscore-to-camel-case=true
```
</details>
</br>

**6) DBConfiguration 클래스 처리**   
ㆍ application.properies 파일에 마이바티스 설정을 추가하였으니, 해당 설정을 처리할 빈을 정의해야 함   
ㆍ DBConfiguration 클래스에 아래 코드를 추가
<details>
    <summary><b>코드 보기</b></summary>
	
```java
@Configuration
@PropertySource("classpath:/application.properties")
public class DBConfiguration {

	@Autowired
	private ApplicationContext applicationContext;

	@Bean
	@ConfigurationProperties(prefix = "spring.datasource.hikari")
	public HikariConfig hikariConfig() {
		return new HikariConfig();
	}

	@Bean
	public DataSource dataSource() {
		return new HikariDataSource(hikariConfig());
	}

	@Bean
	public SqlSessionFactory sqlSessionFactory() throws Exception {
		SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
		factoryBean.setDataSource(dataSource());
		factoryBean.setMapperLocations(applicationContext.getResources("classpath:/mappers/**/*Mapper.xml"));
		factoryBean.setTypeAliasesPackage("com.board.domain");
		factoryBean.setConfiguration(mybatisConfg());
		return factoryBean.getObject();
	}

	@Bean
	public SqlSessionTemplate sqlSession() throws Exception {
		return new SqlSessionTemplate(sqlSessionFactory());
	}

	@Bean
	@ConfigurationProperties(prefix = "mybatis.configuration")
	public org.apache.ibatis.session.Configuration mybatisConfg() {
		return new org.apache.ibatis.session.Configuration();
	}
}
```
</details>

|구성 요소|설명|
|---|---|
|setTypeAliasesPackage|BoardMapper XML의 parameterType과 resultTrpe은 클래스의 풀 패키지 경로가 포함이 되어야함, 해당메서드를 사용함으로써 풀 패키지 경로 생략 가능|
|setConfiguration|마이바티스 설정과 관련된 빈을 설정 파일로 지정|
|mybatisConfig|application.properties 파일에서 mybatis.configuration으로 시작하는 모든 설정을 읽어 들여 빈으로 등록|
</br>

---
### 7. 게시글 등록 구현
**1) Service 영역**   
ㆍ Service 영역은 비즈니스 로직을 담당   
ㆍ service 패키지에 BoardService 인터페이스를 생성하고 아래 코드를 작성   
<details>
	<summary><b>코드 보기</b></summary>
	
```java
public interface BoardService {

	public boolean registerBoard(BoardDTO params);

	public BoardDTO getBoardDetail(Long idx);

	public boolean deleteBoard(Long idx);

	public List<BoardDTO> getBoardList();
}
```
</details>
	
ㆍ service 패키지에 BoardServiceImpl 클래스 생성   
ㆍ BoardServiceImpl 클래스는 BoardService 인터페이스의 구현 클래스 역할을 함   
ㆍ BoardServiceImpl 클래스에 아래 코드를 작성   
<details>
	<summary><b>코드 보기</b></summary>
	
```java
@Service
public class BoardServiceImpl implements BoardService {

	@Autowired
	private BoardMapper boardMapper;

	@Override
	public boolean registerBoard(BoardDTO params) {
		int queryResult = 0;

		if (params.getIdx() == null) {
			queryResult = boardMapper.insertBoard(params);
		} else {
			queryResult = boardMapper.updateBoard(params);
		}

		return (queryResult == 1) ? true : false;
	}

	@Override
	public BoardDTO getBoardDetail(Long idx) {
		return boardMapper.selectBoardDetail(idx);
	}

	@Override
	public boolean deleteBoard(Long idx) {
		int queryResult = 0;

		BoardDTO board = boardMapper.selectBoardDetail(idx);

		if (board != null && "N".equals(board.getDeleteYn())) {
			queryResult = boardMapper.deleteBoard(idx);
		}

		return (queryResult == 1) ? true : false;
	}

	@Override
	public List<BoardDTO> getBoardList() {
		List<BoardDTO> boardList = Collections.emptyList();

		int boardTotalCount = boardMapper.selectBoardTotalCount();

		if (boardTotalCount > 0) {
			boardList = boardMapper.selectBoardList();
		}

		return boardList;
	}
}
```
</details>
	
|구성 요소|설명|
|---|---|
|@Service|해당 클래스가 비즈니스 로직을 담당하는 서비스 클래스임을 지정|
|registerBoard|1. params의 idx가 null이라면, insertBoard 메서드가 실행 </br>2. params의 idx가 null이 아니라면, updateBoard 메서드가 실행</br>3. queryResult 변수에는 쿼리를 실행한 횟수 1이 저장</br>4. 쿼리의 실행 결과를 판단해 true 또는 false를 반환|
|getBoardDetail|하나의 게시글을 조회하는 selectBoardDetail 메서드의 결과값을 반환|
|deleteBoard|1. 파라미터로 입력받은 idx에 해당하는 게시물을 조회</br>2. 해당 게시물이 null이 아니거나, 이미 삭제된 게시물이 아니라면 deleteBoard 메서드 실행</br>3. queryResult 변수에는 쿼리를 실행한 횟수 1이 저장</br>4. 쿼리의 실행 결과를 판단해 true 또는 false를 반환|
|getBoardList|1. 비어있는 리스트를 선언</br>2. 삭제되지 않은 게시글들을 비어있는 리스트에 삽입</br>3. 해당 리스트를 반환|
</br>

**2) Controller 영역**   
ㆍ Controller 영역은 Model 영역과 View 영역을 연결해주고, 사용자의 요청과 응답을 처리해 줌   
ㆍ controller 패키지에 BoardController 클래스를 생성하고 아래에 코드를 작성
<details>
	<summary><b>코드 보기</b></summary>
	
```java
@Controller
public class BoardController {

	@Autowired
	private BoardService boardService;

	@GetMapping(value = "/board/write.do")
	public String openBoardWrite(@RequestParam(value = "idx", required = false) Long idx, Model model) {
		if (idx == null) {
			model.addAttribute("board", new BoardDTO());
		} else {
			BoardDTO board = boardService.getBoardDetail(idx);
			if (board == null) {
				return "redirect:/board/list.do";
			}
			model.addAttribute("board", board);
		}

		return "board/write";
	}
	
	@PostMapping(value = "/board/register.do")
	public String registerBoard(final BoardDTO params) {
		try {
			boolean isRegistered = boardService.registerBoard(params);
			if (isRegistered == false) {
				// 게시글 등록에 실패하였다는 메시지 전달
			}
		} catch (DataAccessException e) {
			// 데이터베이스 처리 과정에 문제가 발생하였다는 메시지 전달

		} catch (Exception e) {
			// 시스템에 문제가 발생하였다는 메시지 전달
		}
		return "redirect:/board/list.do";
	}
}
```
</details>

|구성 요소|설명|
|---|---|
|@Controller|해당 클래스가 컨트롤러 클래스임을 지정|
|@GetMapping|1. get 방식으로 매핑을 처리할 수 있는 애너테이션</br>2. get 방식은 파라미터가 주소창에 노출이되며, 주로 데이터를 조회할 때 사용|
|@RequestParam|1. 화면에서 전달받은 파라미터를 처리하는 데 사용</br>2. required 속성이 false라면 반드시 필요한 파라미터가 아니라는 의미|
|Model|메서드의 파라미터로 지정된 Model 객체는 데이터를 뷰로 전달하는 데 사용|
|리턴 타입|1. 컨트롤러 메서드의 리턴타입은 String으로 사용자에 보여줄 화면의 경로를 반환</br>2. 반환된 경로를 자동으로 연결하여 사용자에게 제공|
|@PostMapping|1. post 방식으로 매핑을 처리할 수 있는 애너테이션</br>2. post 방식은 파라미터가 주소창에 노출되지 않으며, 주로 데이터를 생성할 때 사용|
|params|BoardDTO의 멤버 변수명과 사용자 입력 필드의 name 속성 값이 동일하면, params의 각 멤버 변수에 전달된 값들이 자동으로 매핑됨|
</br>

---
### 8. 게시글 리스트 구현
**1) Controller 영역**   
ㆍ 게시글 목록을 보여줄 리스트 페이지에 대한 Controller 영역의 처리가 필요   
ㆍ BoardController 클래스에 아래의 코드를 작성   
<details>
	<summary><b>코드 보기</b></summary>
	
```java
@GetMapping(value = "/board/list.do")
public String openBoardList(Model model) {
	List<BoardDTO> boardList = boardService.getBoardList();
	model.addAttribute("boardList", boardList);

	return "board/list";
}
```
</details>
	
|구성 요소|설명|
|---|---|
|boardList|BoardService에서 호출한 getBoardList 메서드의 실행 결과를 담아 View 영역으로 전달하는데 사용|
</br>

---
### 9. 게시글 조회 구현
**1) Controller 영역**   
ㆍ 특정 게시물을 조회해 출력해 주는 Controller 영역의 처리가 필요   
ㆍ BoardController 클래스에 아래의 코드를 작성
<details>
	<summary><b>코드 보기</b></summary>
	
```java
@GetMapping(value = "/board/view.do")
public String openBoardDetail(@RequestParam(value = "idx", required = false) Long idx, Model model) {
	if (idx == null) {
		// 올바르지 않은 접근이라는 메시지를 전달하고, 게시글 리스트로 이동
		return "redirect:/board/list.do";
	}

	BoardDTO board = boardService.getBoardDetail(idx);
	if (board == null || "Y".equals(board.getDeleteYn())) {
		// 없는 게시글이거나, 이미 삭제된 게시글이라는 메시지를 전달하고, 게시글 리스트로 이동
		return "redirect:/board/list.do";
	}
	model.addAttribute("board", board);

	return "board/view";
}
```
</details>
	
|구성 요소|설명|
|---|---|
|board|getBoardDetail 메서드의 인자로 idx를 전달해서 게시글 정보를 담아 View 영역으로 전달|
</br>
	
---
### 10. 게시글 삭제 구현
**1) Controller 영역**   
ㆍ 특정 게시물을 삭제해 주는 Controller 영역의 처리가 필요   
ㆍ BoardController 클래스에 아래의 코드를 작성   
<details>
	<summary><b>코드 보기</b></summary>
	
```java
@PostMapping(value = "/board/delete.do")
public String deleteBoard(@RequestParam(value = "idx", required = false) Long idx) {
	if (idx == null) {
		// 올바르지 않은 접근이라는 메시지를 전달하고, 게시글 리스트로 이동
		return "redirect:/board/list.do";
	}

	try {
		boolean isDeleted = boardService.deleteBoard(idx);
		if (isDeleted == false) {
			// 게시글 삭제에 실패하였다는 메시지를 전달
		}
	} catch (DataAccessException e) {
		// 데이터베이스 처리 과정에 문제가 발생하였다는 메시지를 전달
	} catch (Exception e) {
		// 시스템에 문제가 발생하였다는 메시지를 전달
	}
	return "redirect:/board/list.do";
}
```
</details>

|구성 요소|설명|
|---|---|
|isDeleted|deletedBoard 메서드의 인자로 idx를 전달해서 해당 게시글을 삭제 후 true 또는 false 값을 저장|
</br>

---
### 11. 경고 메시지 처리
**1) Enum 클래스**   
ㆍ constatnt 패키지를 추가한 후, Method라는 이름으로 다음의 Enum 클래스를 추가   
ㆍ Enum 클래스는 상수를 처리하는 목적으로 사용  
<details>
	<summary><b>코드 보기</b></summary>
	
```java
public enum Method {
    GET, POST, PUT, PATCH, DELETE
}
```
</details>
</br>

**2) 공통 컨트롤러 생성**   
ㆍ util 패키지를 생성한 후 UiUtils 클래스를 추가   
ㆍ UiUtils 클래스에 아래 코드를 작성   
<details>
	<summary><b>코드 보기</b></summary>
	
```java
@Controller
public class UiUtils {

	public String showMessageWithRedirect(@RequestParam(value = "message", required = false) String message,
										  @RequestParam(value = "redirectUri", required = false) String redirectUri,
										  @RequestParam(value = "method", required = false) Method method,
										  @RequestParam(value = "params", required = false) Map<String, Object> params, Model model) {

		model.addAttribute("message", message);
		model.addAttribute("redirectUri", redirectUri);
		model.addAttribute("method", method);
		model.addAttribute("params", params);

		return "utils/message-redirect";
	}

}
```
</details>
	
|구성 요소|설명|
|---|---|
|message|사용자에게 전달할 메시지|
|redirectUri|이동할 페이지의 URI|
|method|Enum 클래스에 선언한 HTTP 요청 메서드|
|params|View 영역으로 전달할 파라미터|
</br>

**3) BoardController 변경**   
ㆍ BoardController에 사용자에게 출력할 메시지에 대한 처리 필요   
ㆍ BoardController는 UiUtils 클래스를 상속 받음   
ㆍ 경고 메시지에 대한 주석 처리 부분에 아래 코드 추가   
<details>
	<summary><b>코드 보기</b></summary>
	
```java
@Controller
public class BoardController extends UiUtils {

	@Autowired
	private BoardService boardService;

	@GetMapping(value = "/board/write.do")
	public String openBoardWrite(@RequestParam(value = "idx", required = false) Long idx, Model model) {
		if (idx == null) {
			model.addAttribute("board", new BoardDTO());
		} else {
			BoardDTO board = boardService.getBoardDetail(idx);
			if (board == null) {
				return "redirect:/board/list.do";
			}
			model.addAttribute("board", board);
		}

		return "board/write";
	}

	@PostMapping(value = "/board/register.do")
	public String registerBoard(final BoardDTO params, Model model) {
		try {
			boolean isRegistered = boardService.registerBoard(params);
			if (isRegistered == false) {
				return showMessageWithRedirect("게시글 등록에 실패하였습니다.", "/board/list.do", Method.GET, null, model);
			}
		} catch (DataAccessException e) {
			return showMessageWithRedirect("데이터베이스 처리 과정에 문제가 발생하였습니다.", "/board/list.do", Method.GET, null, model);

		} catch (Exception e) {
			return showMessageWithRedirect("시스템에 문제가 발생하였습니다.", "/board/list.do", Method.GET, null, model);
		}

		return showMessageWithRedirect("게시글 등록이 완료되었습니다.", "/board/list.do", Method.GET, null, model);
	}

	@GetMapping(value = "/board/list.do")
	public String openBoardList(Model model) {
		List<BoardDTO> boardList = boardService.getBoardList();
		model.addAttribute("boardList", boardList);

		return "board/list";
	}

	@GetMapping(value = "/board/view.do")
	public String openBoardDetail(@RequestParam(value = "idx", required = false) Long idx, Model model) {
		if (idx == null) {
			// 올바르지 않은 접근이라는 메시지를 전달하고, 게시글 리스트로 이동
			return "redirect:/board/list.do";
		}

		BoardDTO board = boardService.getBoardDetail(idx);
		if (board == null || "Y".equals(board.getDeleteYn())) {
			// 없는 게시글이거나, 이미 삭제된 게시글이라는 메시지를 전달하고, 게시글 리스트로 이동
			return "redirect:/board/list.do";
		}
		model.addAttribute("board", board);

		return "board/view";
	}

	@PostMapping(value = "/board/delete.do")
	public String deleteBoard(@RequestParam(value = "idx", required = false) Long idx, Model model) {
		if (idx == null) {
			return showMessageWithRedirect("올바르지 않은 접근입니다.", "/board/list.do", Method.GET, null, model);
		}

		try {
			boolean isDeleted = boardService.deleteBoard(idx);
			if (isDeleted == false) {
				return showMessageWithRedirect("게시글 삭제에 실패하였습니다.", "/board/list.do", Method.GET, null, model);
			}
		} catch (DataAccessException e) {
			return showMessageWithRedirect("데이터베이스 처리 과정에 문제가 발생하였습니다.", "/board/list.do", Method.GET, null, model);

		} catch (Exception e) {
			return showMessageWithRedirect("시스템에 문제가 발생하였습니다.", "/board/list.do", Method.GET, null, model);
		}

		return showMessageWithRedirect("게시글 삭제가 완료되었습니다.", "/board/list.do", Method.GET, null, model);
	}
}
```
</details>
</br>
	
---
### 12. 인터셉터 적용
**1) 인터셉터란?**   
ㆍ 인터셉터(Interceptor)의 의미는 "가로챈다."라는 의미가 있음   
ㆍ 컨트롤러의 URI에 접근하는 과정에서 무언가를 제어할 필요가 있을 때 사용   
ㆍ 예를 들어, 특정 페이지에 접근할 때 로그인이나 계정의 권한과 관련된 처리를 인터셉터를 통해 효율적으로 해결 가능   
</br>

**2) 인터셉터 구현**   
ㆍ 스프링에서 인터셉터는 "HandlerInterceptor" 인터페이스를 상속받아 구현할 수 있음   
ㆍ 해당 인터페이스는 preHandle, postHandle, afterCompletion, afterConcurrentHandlingStarted 총 네 개의 메서드를 포함   
ㆍ interceptor 패키지에 LoggerInterceptor 클래스를 추가한 후 다음의 코드를 작성   
<details>
	<summary><b>코드 보기</b></summary>
	
```java
@Slf4j
public class LoggerInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        log.debug("");
        log.debug("==================== BEGIN ====================");
        log.debug("Request URI ===> " + request.getRequestURI());

        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        log.debug("==================== END ======================");
        log.debug("");
    }
}
```
</details>

|구성 요소|설명|
|---|---|
|preHandle|컨트롤러의 메서드에 매핑된 특정 URI를 호출했을 때 컨트롤러에 접근하기 전에 실행되는 메서드|
|postHandle|컨트롤러를 경유한 다음 화면으로 결과를 전달하기 전에 실행되는 메서드|
</br>

**3)LoggerInterceptor 클래스를 빈으로 등록**   
ㆍ configuration 패키지에 MvcConfiguration 클래스를 생성 후, 아래 코드를 작성   
<details>
	<summary><b>코드 보기</b></summary>
	
```java
@Configuration
public class MvcConfiguration implements WebMvcConfigurer {

	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		registry.addInterceptor(new LoggerInterceptor())
		.excludePathPatterns("/css/**", "/fonts/**", "/plugin/**", "/scripts/**");
	}
}
```
</details>

|구성 요소|설명|
|---|---|
|addInterceptor|특정 인터셉터를 빈으로 등록하기 위한 메서드|
|excludePathPatterns|특정 패턴의 주소를 인터셉터에서 제외하는 메서드|
</br>

---
### 12. AOP 적용
**1) AOP란?**   
ㆍ AOP는 Aspect Oriented Programming의 약자   
ㆍ 관점 지향 프로그램으로써 자바와 같은 객체 지향 프로그래밍을 더욱 객체 지향스럽게 사용할 수 있도록 도와줌   
ㆍ 핵심 비즈니스 로직 외에 공통으로 처리해야 하는 로그 출력, 보안 처리, 예외 처리와 같은 코드를 별도로 분리하는 모듈화의 개념   
ㆍ AOP에서 관점을 핵심적인 관점과 부가적인 관점으로 나눌 수 있음   
ㆍ 핵심적인 관점은 핵심 비즈니스 로직을 의미하고, 부가적인 관점은 핵심 비즈니스 로직 외에 공통으로 처리해야 하는 부분을 의미   
<img src="https://blog.kakaocdn.net/dn/pD57t/btqDLEZKQib/1KOdMZKJgFY06WMwxNydkk/img.png" width="50%">   
ㆍ 위 사진은 일반적인 객체 지향 프로그래밍의 동작과정을 보여줌   
ㆍ 각각의 화살표는 하나의 기능을 구현하는 게 필요한 작업을 의미   
ㆍ 로그 출력, 보안 처리와 같은 부가적인 기능들이 각각의 작업에 추가됨으로써 코드가 복잡해지고, 생산성이 낮아짐   
<img src="https://blog.kakaocdn.net/dn/DWbbY/btqGfN6LnPh/DVVAcqmplI6UEqZVjkhnyK/img.png" width="50%">   
ㆍ 위 사진은 관점 지향 프로그래밍의 동작과정을 보여줌   
ㆍ 객체 지향 프로그래밍과 달리 부가적인 기능들이 핵심 비즈니스 로직 바깥에서 동작   
ㆍ 이와 같이 공통으로 처리해야하는 기능들을 별도로 분리하여 중복되는 코드를 제거하고, 재사용성을 극대화 할 수 있음   
</br>

**2) AOP 용어**   
|구성 요소|설명|
|---|---|
|Aspect|1. 공통으로 적용될 기능</br>2. 부가적인 기능을 정의한 코드인 Advice와 Advice를 어느 곳에 적용할지 결정하는 Pointcut의 조합으로 만들어짐|
|Advice|실제로 부가적인 기능을 구현한 객체|
|JoinPoint|Advice를 적용할 위치|
|Pointcut|1. Advice를 적용할 JoinPoint를 선별하는 과정이나, 그 기능을 정의한 모듈</br>2. 어떤 JoinPoint를 사용할지 결정|
|Target|1. 실제로 비즈니스 로직을 수행하는 개체</br>2. 즉, Advice를 적용할 대상을 의미|
|Proxy|Advice가 적용되었을 때 생성되는 객체|
|Introduction|Target에는 없는 새로운 메서드나 인스턴스 변수를 추가하는 기능|
|Weaving|Pointcut에 의해 결정된 Target의 JoinPoint에 Advice를 적용하는 것|
</br>

**3) AOP 구현**   
ㆍ aop 패키지를 추가하고 LoggerAspect 클래스 생성 후 아래 코드를 작성   
<details>
	<summary><b>코드 보기</b></summary>
	
```java
@Component
@Aspect
public class LoggerAspect {

	private final Logger logger = LoggerFactory.getLogger(this.getClass());

	@Around("execution(* com.Board..controller.*Controller.*(..)) or execution(* com.Board..service.*Impl.*(..)) or execution(* com.Board..mapper.*Mapper.*(..))")
	public Object printLog(ProceedingJoinPoint joinPoint) throws Throwable {

		String type = "";
		String name = joinPoint.getSignature().getDeclaringTypeName();

		if (name.contains("Controller") == true) {
			type = "Controller ===> ";

		} else if (name.contains("Service") == true) {
			type = "ServiceImpl ===> ";

		} else if (name.contains("Mapper") == true) {
			type = "Mapper ===> ";
		}

		logger.debug(type + name + "." + joinPoint.getSignature().getName() + "()");
		return joinPoint.proceed();
	}
}
```
</details>

|구성 요소|설명|
|---|---|
|@Component|1. 스프링 컨테이너에 빈으로 등록하기 위한 애너테이션</br>2. @Bean은 개발자가 제어할 수 없는 외부 라이브러리를 빈으로 등록할 때 사용</br>3. @Component는 개발자가 직접 정의한 클래스를 빈으로 등록할 때 사용|
|@Aspect|AOP 기능을 하는 클래스에 지정하는 애너테이션|
|@Around|Advice의 종류 중 한 가지로 Target 메서드 호출 이전과 이후에 모두 적용됨을 의미|
|execution|1. Pointcut을 지정하는 문법</br>2. 즉, 어떤 위치에 공통 기능을 적용할 것인지 정의|
|getSignature( )|실행되는 대상 객체 메서드에 대한 정보를 가지고 옴|
