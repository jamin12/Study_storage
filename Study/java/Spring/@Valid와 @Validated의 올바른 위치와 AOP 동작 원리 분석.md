
Service 레이어에서 유효성 검사를 수행할 때, @Validated와 @Valid를 어디에 배치해야 할지는 항상 고민거리다.

테스트를 해보면 인터페이스에 @Validated를 붙여도 유효성 검사가 잘 동작한다.

그렇다면 굳이 구현체(Service Class)에 `@Validated`를 옮겨 적는 이유는 무엇일까? 단순히 "남들이 그렇게 해서"가 아니라, **스프링 AOP의 동작 원리와 아키텍처 설계의 관점**에서 그 이유를 정리해 본다.

## 1. 문제 제기 (Context)

헥사고날 아키텍처를 적용하면서 Port(Interface)와 Adapter(Implementation)를 분리했다. 처음에는 "검증도 계약의 일부"라고 생각하여 인터페이스에 모든 애노테이션을 몰아넣었다.

``` kotlin
// [초기 코드] 인터페이스에 Trigger(@Validated)와 Rule(@Valid)을 모두 넣음
@Validated // AOP 활성화 (Spring 기술)
interface CreateRoleInPort {
    fun execute(@Valid command: CreateRoleCommand): CreateRoleResult
}

@Service
class CreateRoleUseCase(...) : CreateRoleInPort {
    override fun execute(command: CreateRoleCommand): CreateRoleResult { ... }
}
```

이 코드는 정상 작동한다.

스프링의 MethodValidationPostProcessor가 인터페이스에 붙은 애노테이션까지 스캔(AnnotationUtils 사용)하여 프록시를 생성해주기 때문이다.

하지만 나는 이 코드를 리팩토링하여 **`@Validated`를 구현체로 옮기기로 결정**했다. 그 이유는 **'작동 여부'가 아니라 '역할의 모호함'** 때문이었다.

---

## 2. 왜 구현체로 옮겼는가? (Why Implementation?)

이유는 크게 두 가지다. **"기술과 계약의 분리"** 그리고 **"AOP 타겟의 명확성"**이다.

### 2.1. 관심사의 분리 (Separation of Concerns)

우리가 사용하는 애노테이션의 성격을 뜯어보면 답이 나온다.

- **`@Valid` (JSR-380):** "이 데이터는 유효해야 한다"는 **비즈니스 계약(Contract)**이다. 이는 인터페이스(설계도)에 존재하는 것이 자연스럽다.
    
- **`@Validated` (Spring AOP):** "이 클래스에 AOP 프록시를 씌워라"라는 **프레임워크 동작 지시(Infrastructure Instruction)** 다.
    

인터페이스는 **"무엇(What)을 할 것인가"** 를 정의하는 곳이다. 반면, **"어떻게(How) 동작할 것인가"**—즉, 스프링 AOP를 켤지 말지, 트랜잭션을 걸지 말지 등은 실제 런타임에 객체를 생성하는 **구현체의 책임**이다.

따라서 순수한 설계 관점에서 **스프링 프레임워크의 동작을 제어하는 스위치(`@Validated`)를 인터페이스에 노출하는 것은 설계의 누수(Leakage)**라고 판단했다.

### 2.2. AOP 동작의 명확성 (Explicitness)

기술적으로 파고들면, 스프링 부트의 기본 프록시 방식인 **CGLIB**는 **클래스 기반의 프록시**를 생성한다.

``` java
// Spring 6.x - MethodValidationPostProcessor.java

// checkInherited = true : 인터페이스까지 뒤져보겠다는 뜻
Pointcut pointcut = new AnnotationMatchingPointcut(this.validatedAnnotationType, true);
```

최신 스프링은 `checkInherited=true` 옵션을 통해 인터페이스에 붙은 `@Validated`도 찾아내어 프록시를 적용해 준다.

하지만 이는 **"스프링이 똑똑해서 찾아주는 것"**이지, 자바의 표준 상속 규칙(인터페이스 애노테이션 상속 불가)을 따르는 것은 아니다. 개발자가 코드를 읽을 때 "이 빈은 AOP가 적용되는가?"를 판단하려면, **빈을 정의하는 클래스(`@Service` 붙은 곳)에 명시적으로 `@Validated`가 붙어있는 편이 훨씬 직관적**이다.

---

## 3. 내부 동작 원리

그렇다면 `@Valid`는 왜 인터페이스에 둬도 괜찮은 걸까? 이것은 AOP 프록시가 생성된 **이후**의 동작이기 때문이다.

1. **Bean 등록 시점 (기동):**
    
    - 스프링은 **구현체(`CreateRoleUseCase`)**를 보고 빈을 만든다.
        
    - 이때 `@Validated`가 구현체에 붙어있으면 **"아, 얘는 확실히 검증이 필요한 녀석이구나"** 하고 즉시 프록시를 씌운다. (명확함)
        
2. **요청 처리 시점 (런타임):**
    
    - 프록시가 요청을 가로채고 `Hibernate Validator`를 호출한다.
        
    - Hibernate Validator는 검증을 위해 **해당 클래스의 족보(Interface 포함)** 를 리플렉션으로 조회한다.
        
    - 이때 인터페이스에 붙은 **`@Valid`(검사 규칙)**를 발견하고 검증을 수행한다.
        

즉, **"기능을 켜는 스위치"는 빈(Bean) 자체인 구현체에 달고, "검사 규칙"은 설계도인 인터페이스에 적어두는 구조**가 된다.

---

## 4. 결론 (Best Practice)

"작동하니까 쓴다"가 아니라 "의도에 맞게 쓴다"를 선택했다.

- **인터페이스(Port):** 도메인 로직의 계약을 정의한다.
    
    -  **`@Valid`** 사용 (규칙 명시)
        
- **구현체(Service):** 애플리케이션의 동작 방식을 정의한다.
    
    -  **`@Validated`** 사용 (기능 활성화)
        

``` Kotlin
// [Port] "입력값은 반드시 유효해야 한다" (계약)
interface CreateRoleInPort {
    fun execute(@Valid command: CreateRoleCommand): CreateRoleResult
}

// [Service] "이 빈은 검증 기능을 수행한다" (구현)
@Service
@Validated
class CreateRoleUseCase(...) : CreateRoleInPort {
    override fun execute(command: CreateRoleCommand): CreateRoleResult {
        // ...
    }
}
```