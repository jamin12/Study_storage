# Spring Security @PreAuthorize 문자열 해부와 커스텀 권한 시스템 구축기

## 1. 출발점 – “이 문자열은 누가 실행하는 걸까?”

기본으로 제공되는 `hasPermission` 함수만으로는 `projectId`, `role`, `scope`, `detail`과 같은 여러 도메인 특정 정보를 유연하게 전달하고 검증하기 어려웠다. 이러한 한계를 극복하고 복잡한 권한 요구사항을 깔끔하게 처리하기 위해 커스텀 권한 시스템과 `@PreAuthorize`를 연동하는 방안을 모색하게 되었다.

```kotlin
@PreAuthorize("hasDomainPermission(#id, 'Project', 'READ', 'PROJECT', 'DETAIL')")
fun getProject(id: UUID) { ... }
```

그런데 여기서 의문이 생겼다.

1. `@PreAuthorize` 안의 **문자열은 누가 분석하는가?**
2. **문자열은 어떻게 실제 메서드 실행으로 이어지는가?**
3. 기본 함수 `hasRole` 말고 **내가 만든 함수 `hasDomainPermission`은 어떻게 연결되는가?**

---

## 2. Spring Method Security 전체 흐름

Spring Security의 메서드 보안은 다음과 같은 **4단계 파이프라인**으로 동작한다.

```
Interceptor → Handler → Root → Evaluator
```

이를 요청 흐름으로 풀면 다음과 같다.

1. **Interceptor**
   `@PreAuthorize`가 붙은 메서드 호출을 가로챈다.

2. **Handler**
   문자열을 해석하기 위한 SpEL 실행 환경(EvaluationContext)을 만들고
   그 중심이 될 **Root 객체**를 생성한다.

3. **Root**
   SpEL 문자열(`hasDomainPermission(...)`)을 실제 메서드 호출로 변환한다.

4. **Evaluator**
   DB 조회 등 실제 비즈니스 로직으로 권한을 판정한다.

---

## 3. 검문과 준비

### 3-1. 메서드를 가로채는 Interceptor

Spring 내부에서 `@PreAuthorize`는 다음 코드로 처리된다.

```java
// AbstractSecurityInterceptor.java
public Object invoke(MethodInvocation mi) throws Throwable {
    InterceptorStatusToken token = super.beforeInvocation(mi);

    Object result;
    try {
        result = mi.proceed();   // 실제 메서드 실행
    } finally {
        super.finallyInvocation(token);
    }

    return super.afterInvocation(token, result);
}
```

* `beforeInvocation()` → `@PreAuthorize` 검사
* `mi.proceed()` → 실제 서비스 메서드 실행

---

### 3-2. Handler가 Root를 생성한다

```java
// DefaultMethodSecurityExpressionHandler.java
public EvaluationContext createEvaluationContext(Supplier<Authentication> authentication, MethodInvocation mi) {
    MethodSecurityExpressionOperations root = createSecurityExpressionRoot(authentication, mi);
    MethodSecurityEvaluationContext ctx = new MethodSecurityEvaluationContext(root, mi,
            getParameterNameDiscoverer());
    ctx.setBeanResolver(getBeanResolver());
    return ctx;
}
```

여기서 핵심은 이 부분이다.

```java
createSecurityExpressionRoot(auth, mi)
```

나는 이 지점을 오버라이드하여 **내가 만든 Root**를 반환하도록 했다.

```kotlin
class CustomMethodSecurityExpressionHandler :
    DefaultMethodSecurityExpressionHandler() {

    override fun createSecurityExpressionRoot(
        authentication: Authentication,
        invocation: MethodInvocation,
    ): MethodSecurityExpressionOperations {

        val root = CustomSecurityExpressionRoot(authentication)

        root.setThis(invocation.`this`)
        root.setPermissionEvaluator(permissionEvaluator)
        root.setTrustResolver(trustResolver)
        root.setRoleHierarchy(roleHierarchy)
        root.setDefaultRolePrefix(defaultRolePrefix)

        return root
    }
}
```

즉, **Spring이 Root를 직접 new 하지 않고, 내가 만든 Handler에게 “만들어 달라”고 요청하는 구조**다.

---

## 4. 문자열은 어떻게 실행되는가? (SpEL)

Handler가 만들어 준 `Root`와 `Context`는 다음 코드로 실행된다.

```java
// ExpressionUtils.java
public static boolean evaluateAsBoolean(Expression expr, EvaluationContext ctx) {
    return expr.getValue(ctx, Boolean.class);
}
```

`expr.getValue(ctx)`가 호출되는 순간:

1. SpEL은 문자열 `hasDomainPermission(...)`을 파싱한다.
2. 메서드 이름을 **Root 객체에서 찾는다.**
3. `CustomSecurityExpressionRoot.hasDomainPermission()`을 실제로 호출한다.

즉, **`@PreAuthorize` 안의 문자열은 Root 객체의 public 메서드 호출로 변환된다.**

---

## 5. Root 객체의 정체와 3가지 변수

커스텀 Root는 다음 구조를 가진다.

```kotlin
class CustomSecurityExpressionRoot(authentication: Authentication)
    : SecurityExpressionRoot(authentication),
      MethodSecurityExpressionOperations {

    private var filterObject: Any? = null
    private var returnObject: Any? = null
    private var target: Any? = null

    override fun setFilterObject(filterObject: Any?) {
        this.filterObject = filterObject
    }

    override fun setReturnObject(returnObject: Any?) {
        this.returnObject = returnObject
    }

    override fun setThis(target: Any?) {
        this.target = target
    }

    override fun getFilterObject() = filterObject
    override fun getReturnObject() = returnObject
    override fun getThis() = target
}
```

### 각 변수의 의미

| 변수                 | 의미                 | 사용 시점            |
| ------------------ | ------------------ | ---------------- |
| `target` (`#this`) | 현재 메서드를 가진 서비스 객체  | 모든 SpEL          |
| `returnObject`     | 메서드의 **최종 리턴값 전체** | `@PostAuthorize` |
| `filterObject`     | 컬렉션 리턴 시 **요소 하나** | `@PostFilter`    |

나는 `@PreAuthorize`만 사용하므로 실질적으로는 `target`만 필요하지만,
**프레임워크 규약을 지키기 위해 나머지도 구현해 두었다.**

---

## 6. hasPermission 파라미터 불일치

### 내가 원한 시그니처

```kotlin
hasDomainPermission(
  targetId,
  targetType,
  scope,
  domain,
  detail
)
```

### Spring 표준 시그니처

```java
hasPermission(authentication, targetId, targetType, permission)
```

> “개수를 늘릴 수 없으면, **하나로 묶어서 보내자.**”

```kotlin
fun hasDomainPermission(
    targetId: Any,
    targetType: String,
    scope: String,
    domain: String,
    detail: String,
): Boolean =
    super.hasPermission(
        targetId,
        targetType,
        PermissionRequirement.of(scope, domain, detail)
    )
```
---

## 7. 최종 판사 – CustomPermissionEvaluator

```kotlin
@Component
class CustomPermissionEvaluator(
    permissionEvaluatorStrategy: List<PermissionEvaluatorStrategy>,
) : PermissionEvaluator {
    private val strategyMap =
        permissionEvaluatorStrategy.associateBy { it.supportType }

    override fun hasPermission(
        authentication: Authentication?,
        targetId: Serializable?,
        targetType: String?,
        permission: Any?,
    ): Boolean {
        val result =
            strategyMap[findTarget]
                ?.hasPermission(memberId, UUID.fromString(targetId), permission)
                ?: false

        return result
    }
}
```

여기가 **실제 DB를 조회하고 비즈니스 로직을 수행하는 최종 판결 지점**이다.

---

## 8. 왜 Root에 직접 구현하지 않았는가?

여기서 "직접 구현하지 않았다"는 것은, **DB 조회나 복잡한 비즈니스 로직을 포함한 실제 권한 검사 로직**을 의미한다.

`CustomSecurityExpressionRoot` 안에 `repository.findById(...)` 같은 코드를 직접 넣지 않은 이유는 다음과 같다.

1.  **Root 객체는 Spring Bean이 아니다.**
    *   Handler에 의해 **요청마다 `new`로 생성되는 POJO**이다.
    *   따라서 `@Autowired`를 통한 의존성 주입을 받을 수 없다.
    *   Repository를 쓰려면 생성자로 일일이 넘겨줘야 하는데, 이는 코드를 지저분하게 만든다.

2.  **반면 `PermissionEvaluator`는 Spring Bean이다.**
    *   `@Component`로 등록되어 있어 Repository, Service를 자유롭게 주입받을 수 있다.

따라서 역할이 자연스럽게 나뉘게 된다.

*   **Root**: "문자열 파싱"과 "위임"만 담당 (가벼운 문지기)
*   **Evaluator**: "DB 조회"와 "실제 판결" 담당 (무거운 판사)

그래서 구조는 다음과 같이 분리되었다.

| 역할      | 위치                                      |
| ------- | --------------------------------------- |
| SpEL 해석 | `CustomSecurityExpressionRoot`          |
| 비즈니스 로직 | `CustomPermissionEvaluator`             |
| 둘을 연결   | `CustomMethodSecurityExpressionHandler` |

---

## 9. 최종 결과 – 선언적 보안의 완성

이제 서비스 코드는 이렇게 바뀌었다.

```kotlin
@PreAuthorize("hasDomainPermission(#id, 'Project', 'READ', 'PROJECT', 'DETAIL')")
fun getProject(id: UUID) { ... }
```

* 더 이상 서비스 로직에 권한 if문이 없다.
* 권한 정책은 **어노테이션 한 줄**로 표현된다.
* 도메인이 늘어나도 **Strategy만 추가하면 된다.**

---

## 10. 정리 (Retrospective)

이번 작업을 통해 얻은 결론은 다음과 같다.

1. `@PreAuthorize`의 문자열은 **SpEL에 의해 Root 객체의 메서드로 변환되어 실행된다.**
2. Root는 해석만 담당하고, 실제 판단은 **PermissionEvaluator(Bean)** 로 위임하는 구조가 정석이다.
3. 파라미터 개수 불일치는 **VO 래핑 전략**으로 깔끔하게 해결할 수 있다.
4. 현재 구조는 Spring Security가 의도한 **최정석 확장 구조**에 해당한다.

