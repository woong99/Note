# Spring Boot Annotations

| Annotation             | 의미                                                |
| ---------------------- | --------------------------------------------------- |
| @SpringBootApplication | Spring boot application으로 설정                    |
| @Controller            | View를 제공하는 controller로 설정                   |
| @RestController        | REST API를 제공하는 controller로 설정               |
| @RequestMapping        | URL 주소를 매핑                                     |
| @GetMapping            | Http GetMethod URL 주소 매핑                        |
| @PostMapping           | Http PostMethod URL 주소 매핑                       |
| @PutMapping            | Http PutMethod URL 주소 매핑                        |
| @DeleteMapping         | Http DeleteMethod URL 주소 매핑                     |
| @RequestParam          | Http Query Parameter 매핑                           |
| @RequestBody           | Http Body를 Parsing 매핑                            |
| @Valid                 | POJO Java class의 검증                              |
| @Configration          | 1개 이상의 bean을 등록할 때 설정                    |
| @Component             | 1개의 Class 단위로 등록할 때 사용                   |
| @Bean                  | 1개의 외부 library로부터 생성한 객체를 등록 시 사용 |
| @Autowired             | DI를 위한 곳에 사용                                 |
| @Qualifier             | @Autowired 사용 시 bean이 2개 이상일 때 명시적 사용 |
| @Resource              | @Autowired + @Qualifier의 개념으로 이해             |
| @Aspect                | AOP 적용 시 사용                                    |
| @Before                | AOP 메소드 이전 호출 지정                           |
| @After                 | AOP 메소드 호출 이후 지정, 예외 발생 포함           |
| @Around                | AOP 이전 / 이후 모두 포함, 예외 발생 포함           |
| @AfterReturning        | AOP 메소드의 호출이 정상일 때 실행                  |
| @AfterThrowing         | AOP시 해당 메소드가 예외 발생 시 지정               |
