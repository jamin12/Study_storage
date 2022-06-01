# 어노테이션
## __<span style="color:#9999ff">@Configuration</span>__
- 가시적으로 설정파일이야~ bean등록할꺼야라고 알려주는 어노테이션이다.
- bean을 등록할때 싱글톤이 되도록 보장해준다.
- 스프링 컨테이너에 bean을 관리할 수 있게 됨
## __<span style="color:#9999ff">@PostConstruct</span>__
- component클래스는 autowired시 초기화 메소드를 실행할 수 있다.
- 이 메소드는 다른 곳에서 호출 없어도 무조건 실행된다.
- 생성자가 호출되었을때 bean이 초기화 되지 않은 상태인데, PostConstruct 어노테이션을 사용하면 bean이 초기화 되면 동시에 의존성 확인이 가능하다.
## __<span style="color:#9999ff">@ControllerAdvice</span>__
- @Controller나 @ RestController에서 발생한 예외를 한 곳에서 관리하고 처리할 수 있게 도와주는 어노테이션이다. 즉 스프링에서 예외처리를 전역으로 핸들링하기 위해 @ControllerAdvicde 어노테이션을 사용할 수 있다.
## __<span style="color:#9999ff">@ExceptionHandler</span>__
- @ExceptionHandler(xxException.class) : 발생한 xxException에 대해서 처리하는 메소드를 작성한다.  
  
## __<span style="color:#9999ff">@SpringBootApplication</span>__
- main메소드가 선언된 클래스를 기준으로 실행된다.
- @SrpginbootApplication 어노테이션은 스프링 부트의 가장 기본적인 설정을 선언해줍니다.
- ``` java
    @Target(value = {ElementType.TYPE})
    @Retention(value = RetentionPolicy.RUNTIME)
    @Documented
    @Inherited
    @SpringBootConfiguration
    @EnableAutoConfiguration
    @ComponentScan(excludeFilters = {
        @ComponentScan.Filter(type = FilterType.CUSTOM, classes = {TypeExcludeFilter.class}),
        @ComponentScan.Filter(type = FilterType.CUSTOM, classes = {AutoConfigurationExcludeFilter.class})})
    public @interface SpringBootApplication {

        @AliasFor(annotation = EnableAutoConfiguration.class)
        public Class<?>[] exclude() default {};

        @AliasFor(annotation = EnableAutoConfiguration.class)
        public String[] excludeName() default {};

        @AliasFor(annotation = ComponentScan.class, attribute = "basePackages")
        public String[] scanBasePackages() default {};

        @AliasFor(annotation = ComponentScan.class, attribute = "basePackageClasses")
        public Class<?>[] scanBasePackageClasses() default {};

        @AliasFor(annotation = ComponentScan.class, attribute = "nameGenerator")
        public Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;

        @AliasFor(annotation = Configuration.class)
        public boolean proxyBeanMethods() default true;
    }
- 여기서 눈여겨 볼 것은 **@ComponentSacn** 과 **@EnableAutoConfiguration**이다.
  - @ComponentScan : @component 어노테이션 및 @service @repository, @controller 등의 어노테이션은 스캔하여 bean으로 등록해주는 어노테이션 이다.
  - @EnableAutoConfiguration : 사전에 정의한 라이브러리들을 Bean으로 등록해주는 어노테이션 이다.</br> 사전에 정의한 라이브러리들 모두가 등록되는 것은 아니고 특정 Condition(조건)이 만족될 경우에 Bean으로 등록한다.
- 사전 정의 파일 위치 </br>Dependencies > spring-boot-autoconfigure > META-INF > spring.factories
- ``` factories
    # spring.factories 파일 내용

    # Initializers
    org.springframework.context.ApplicationContextInitializer=\
    org.springframework.boot.autoconfigure.SharedMetadataReaderFactoryContextInitializer,\
    org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener

    # Application Listeners
    org.springframework.context.ApplicationListener=\
    org.springframework.boot.autoconfigure.BackgroundPreinitializer

    # Auto Configuration Import Listeners
    org.springframework.boot.autoconfigure.AutoConfigurationImportListener=\
    org.springframework.boot.autoconfigure.condition.ConditionEvaluationReportAutoConfigurationImportListener

    # Auto Configuration Import Filters
    org.springframework.boot.autoconfigure.AutoConfigurationImportFilter=\
    org.springframework.boot.autoconfigure.condition.OnBeanCondition,\
    org.springframework.boot.autoconfigure.condition.OnClassCondition,\
    org.springframework.boot.autoconfigure.condition.OnWebApplicationCondition

    # Auto Configure
    org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
    org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
    org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
    org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
    org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
    org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
    org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
    org.springframework.boot.autoconfigure.cloud.CloudServiceConnectorsAutoConfiguration,\
    ...
- "org.springframework.boot.autoconfigure.EnableAutoConfiguration="에 등록된 클래스들이 자동으로 등록되는 Bean이다. 각 Bean은 OnBeanCondition, OnClassCondition, OnWebApplicationCondition 어노테이션의 조건에 의해 등록 여부가 결정된다

- OnBeanCondition : 특정 Bean이 사전에 생성되어있지 않을 경우에 조건이 만족됩니다.
- OnClassCondition : 특정 Bean이 이미 생성되어있을 경우에 조건이 만족 됩니다.
- OnWebApplicationCondition : Classpath에 특정 class가 존재할 경우에 조건이 만족됩니다.
# 핵심 원리  
## __스프링 빈의 이벤트 사이클__
 - 스프링 컨테이너 생성 -> 스프링 빈 생성 -> 의존관계 주입 -> 초기화 콜백 -> 사용 -> 소멸전 콜백 -> 스프링 종료
 - 초기화 콜백: 빈이 생성되고 빈의 의존관계 주입이 완료된 후 호출
 - 소멸전 콜백: 빈이 소멸되기 직전에 호출

## __싱글톤 패턴 (singleton pattern)__
- 소프트웨어 디자인 패턴에서 싱글턴 패턴을 따르는 클래스틑, 생성자가 여러차례호출되더라도 **실제로 생성되는 객체는**  하나이고 최초 생성 이후에 호출된 생성자는 최초의 생성자가 생성한 객체를 리턴한다.
- 만약 우리가 만들었던 DI 컨테이너인 요청을 할 때마다 새로운 객체를 생성한다면 요청이 엄청나게 많은 트래픽 사이트에서는 계속 객체를 생성하게 되면 **메모리 낭비가** 심하기 때문이다.
- **조금 더 공부하자 ㅇㅋ?**

# 참고
- [@SpringBootApplication](https://bamdule.tistory.com/31)