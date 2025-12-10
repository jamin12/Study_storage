개발하다 보면 한 번쯤 마주치는, Timezone.

로컬에서 개발할 땐 아무 문제 없었는데, AWS나 Docker 환경에 배포하자마자 생성 시간이 9시간씩 밀리거나 당겨지는 현상을 겪었다.

단순히 `LocalDateTime`을 쓰고 `+9` 시간을 더하는 땜질식 처방이 아니라, **"어떤 환경에 배포해도 절대 꼬이지 않는"** 근본적인 해결책을 찾기 위해 고민하고 해결한 과정을 정리한다.

---
## 1. 문제 발생 (The Problem)

### "로컬에선 멀쩡했는데, 배포 서버에선 시간이 널뛰기를 한다?"

기능 개발을 마치고 로컬(Mac)에서 테스트할 때는 완벽했다. 한국 시간으로 **오후 1시(13:00)**에 생성한 데이터가 조회할 때도 정확히 **13:00**로 나왔다.

그런데 AWS에 배포한 뒤 데이터를 확인해보니 이상한 현상이 발생했다. 내가 분명 **13:00**에 저장했는데, 조회된 데이터는 **22:00(밤 10시)** 로 나오거나, 때로는 **새벽 4시**로 나오는 등 종잡을 수 없는 시차 문제가 발생했다.

---

## 2. 원인 분석 (Root Cause)

처음엔 DB 타임존 설정 문제인 줄 알았다. 하지만 코드를 뜯어보니 진짜 범인은 내가 과거에 짜 둔 `Converter`였다.

나는 엔티티에 `ZonedDateTime`을 사용하고 있었지만, DB(PostgreSQL)와의 통신을 위해 아래와 같은 **치명적인 컨버터**를 등록해 둔 상태였다.

``` Kotlin
@Converter(autoApply = true)
class ZonedDateTimeConverter : AttributeConverter<ZonedDateTime, LocalDateTime> {
  
  // [Write] 저장할 때: 타임존을 떼어버리고 숫자만 남김
  override fun convertToDatabaseColumn(zonedDateTime: ZonedDateTime?): LocalDateTime? = 
      zonedDateTime?.toLocalDateTime()

  // [Read] 읽어올 때: 서버의 기본 시간(SystemDefault)을 냅다 붙여버림
  override fun convertToEntityAttribute(localDateTime: LocalDateTime?): ZonedDateTime? =
      localDateTime?.atZone(ZoneId.systemDefault())
}
```

### 시나리오 재구성

1. **저장 (Write):**
    
    - 사용자가 `13:00 (KST)`에 글을 작성.
        
    - `convertToDatabaseColumn`: 타임존 정보(`Asia/Seoul`)를 삭제하고 **`13:00`**이라는 숫자만 DB에 저장. (여기서 데이터의 국적을 잃어버림)
        
2. **조회 (Read) - 내 PC (Mac/KST):**
    
    - DB에서 `13:00`을 꺼냄.
        
    - `convertToEntityAttribute`: `ZoneId.systemDefault()`가 **KST**이므로, `13:00`에 **KST** 명찰을 붙임.
        
    - 결과: `13:00 (KST)` -> **정상으로 보임 (착시)**
        
3. **조회 (Read) - 배포 서버 (Linux/UTC):**
    
    - DB에서 `13:00`을 꺼냄.
        
    - `convertToEntityAttribute`: 서버의 `ZoneId.systemDefault()`는 **UTC**임.
        
    - 드라이버는 `13:00`에 **UTC** 명찰을 붙여버림 -> `13:00 (UTC)`가 됨.
        
    - **대참사:** 한국 사용자는 `13:00 (UTC)`를 **한국 시간 22:00**로 보게 됨. (**9시간 미래로 가버림**)
---

## 2. 고민 (Consideration)

### 골든 룰(Golden Rule) 정립

해결책을 찾기 위해 여러 레퍼런스를 뒤지다 보니, 글로벌 서비스를 운영하는 곳들의 공통된 원칙이 있었다.

> **"DB에는 무조건 UTC(국제 표준시)로 저장하고, 애플리케이션에서 꺼내 볼 때만 로컬 시간(KST)으로 변환한다."**

---

## 3. 해결 과정 A: PostgreSQL (JPA)

처음엔 `ZonedDateTime`을 DB에 넣기 위해 별도의 `AttributeConverter`를 만들어야 하나 싶었다. 하지만 **PostgreSQL**과 **Hibernate**는 생각보다 똑똑했다.

### 시행착오

JPA `AttributeConverter`를 만들어서 `convertToDatabaseColumn`에서 강제로 시간을 빼고 더하는 로직을 짰었다. 하지만 이건 **하수**의 방법이었다. 최신 JDBC 드라이버는 이를 자동으로 지원한다.

### 최종 해결 코드

컨버터? 다 필요 없다. DB 컬럼 타입을 `TIMESTAMP WITH TIME ZONE`으로 맞추고, 엔티티에는 `ZonedDateTime`만 선언하면 된다.


``` Kotlin
@MappedSuperclass
abstract class BaseEntity {

    @CreationTimestamp // Hibernate가 알아서 넣어줌
    @Comment("생성일")
    @Column(nullable = false, updatable = false, columnDefinition = "TIMESTAMP(3) WITH TIME ZONE")
    var createdAt: ZonedDateTime? = null
        protected set

    @UpdateTimestamp
    @Comment("수정일")
    @Column(nullable = false, columnDefinition = "TIMESTAMP(3) WITH TIME ZONE")
    var updatedAt: ZonedDateTime? = null
        protected set
}
```

- **저장할 때:** JDBC 드라이버가 내 서버의 시간(`KST`)을 보고 알아서 `UTC`로 변환해서 저장한다.
    
- **조회할 때:** DB에 저장된 `UTC` 값을 가져와서, 다시 내 서버 설정에 맞게 `+9시간`을 더해서 반환해 준다.
    

즉, **개발자가 시차 계산을 할 필요가 아예 없다.**

---

## 4. 해결 과정 B: MongoDB

문제는 몽고DB였다. PostgreSQL처럼 드라이버가 알아서 해주지 않았다.

MongoDB는 기본적으로 데이터를 UTC Date로 저장한다. 그래서 "저장할 땐 UTC로 세탁하고, 꺼낼 땐 KST로 옷을 입히는" 컨버터가 필요했다.

### 1) Config 설정

`@EnableMongoAuditing`을 설정하고, 커스텀 컨버터를 등록했다.

``` Kotlin
@Configuration
@EnableMongoAuditing(dateTimeProviderRef = "auditingDateTimeProvider")
class MongoConfig {

    // 저장 시점은 무조건 UTC 기준
    @Bean
    fun auditingDateTimeProvider() = DateTimeProvider {
        Optional.of(ZonedDateTime.now(ZoneId.of("UTC")))
    }

    @Bean
    fun customConversions(): MongoCustomConversions {
        return MongoCustomConversions(
            listOf(
                ZonedDateTimeToDateConverter(), // Write
                DateToZonedDateTimeConverter()  // Read
            )
        )
    }
}
```

### 2) Converter 구현



``` Kotlin
// [Write] 갈 때: ZonedDateTime -> Date (무조건 UTC로 변환되어 저장됨)
@WritingConverter
class ZonedDateTimeToDateConverter : Converter<ZonedDateTime, Date> {
    override fun convert(source: ZonedDateTime): Date {
        return Date.from(source.toInstant()) 
    }
}

// [Read] 올 때: Date -> ZonedDateTime (서버 설정에 맞춰서 시간 변환)
@ReadingConverter
class DateToZonedDateTimeConverter : Converter<Date, ZonedDateTime> {
    override fun convert(source: Date): ZonedDateTime {
        return source.toInstant().atZone(ZoneId.systemDefault())
    }
}
```

이렇게 하면 DB에는 깔끔하게 UTC로 들어가고, 코드에서 사용할 땐 한국 시간으로 편하게 쓸 수 있다.

---

## 5. 마지막 함정: 서버 시간 설정 (SystemDefault)

코드상으로 완벽하다고 생각했는데, 배포 서버에서 **조회 시 시간이 다시 9시간 밀리는 현상**이 발생했다.

범인은 코드 내의 ZoneId.systemDefault()였다.

내 로컬 PC는 한국 시간이라 잘 작동했지만, AWS 서버(Linux)는 기본 시간이 UTC였던 것. 그러니 systemDefault()가 UTC를 반환해서 +9시간 보정을 안 해준 것이다.

### 해결: JVM 옵션 추가

코드를 고칠 게 아니라, 서버가 시작될 때 **"너는 무조건 한국이야"** 라고 세뇌를 시켜야 한다.

Bash

```
java -Duser.timezone=Asia/Seoul -jar my-application.jar
```

---

## 6. 요약 및 결론

1. **타입:** 날짜 필드는 웬만하면 `LocalDateTime` 대신 **`ZonedDateTime`**을 쓰자.
    
2. **PostgreSQL:** `TIMESTAMPTZ` (`TIMESTAMP WITH TIME ZONE`)를 쓰면 드라이버가 알아서 다 해준다. 컨버터 짜지 말자.
    
3. **MongoDB:** `Date`로 저장하되, `Reading/Writing Converter`를 등록해서 입출구 조사를 확실히 하자.
    
4. **배포:** 서버 실행 시 `-Duser.timezone=Asia/Seoul` 옵션은 필수다.
    