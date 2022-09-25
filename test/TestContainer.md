# TestContainer
* 테스트에서 도커 컨테이너를 실행할 수 있는 라이브러리
    - 테스트 실행시 DB를 설정하거나 별도의 프로그램 또는 스크립트를 실행할 필요 없다
    - 보다 Production에 가까운 테스트를 만들 수 있다
    - 테스트가 느려짐

## 예제

### 의존성 추가
```gradle
testImplementation "org.testcontainers:testcontainers:1.17.3"
testImplementation "org.testcontainers:junit-jupiter:1.17.3"
```

### MySQL Module 추가
```gradle
testImplementation "org.testcontainers:mysql:1.17.3"
```

### Testcontainers 설정
```property
# == Database ==
db.username=admin
db.password=1234
db.url=jdbc:tc:mysql:///lotification

# == jpa
jpa.ddl-auto=create-drop


=====================

spring:
  datasource:
    driver-class-name: org.testcontainers.jdbc.ContainerDatabaseDriver
    username: ${db.username}
    password: ${db.password}
    url: ${db.url}

  jpa:
    open-in-view: false
    hibernate:
      ddl-auto: ${jpa.ddl-auto}
    properties:
      hibernate:
        show_sql: true
        format_sql: true
      database-platform: org.hibernate.dialect.MySQL8Dialect
    database: mysql
```

### Test 클래스 생성 및 작성
```java
@Testcontainers
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@Transactional
class UserRepositoryTest {

    @PersistenceContext
    EntityManager em;

    @Autowired
    UserRepository userRepository;

    @Container
    static MySQLContainer mySQLContainer = new MySQLContainer("mysql:8")
            .withDatabaseName("lotification")
            .withUsername("admin")
            .withPassword("1234");

    @BeforeEach
    void before(){
        userRepository.deleteAll();
    }

    @Test
    void test1(){
        userRepository.save(new User("test1"));
        userRepository.save(new User("test2"));

        em.flush();
        em.clear();

        List<User> users = userRepository.findAll();

        Assertions.assertThat(users.size()).isEqualTo(2);
    }
}
```
