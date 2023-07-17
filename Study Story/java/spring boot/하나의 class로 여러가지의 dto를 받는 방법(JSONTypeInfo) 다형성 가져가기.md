# 하나의 class로 여러가지의 dto를 받는 방법(JSONTypeInfo) 다형성 가져가기

## api 하나로 여러개의 json객체를 받아야한다고?
회사에서 작업을 하다가 테이블 Join전략을 만나게 되었다.<br/>
@Inheritance(strategy = InheritanceType.JOINED) 처음 보는 친구라 "이게 뭐지??" 하고 있었다.
``` java 
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
@Getter
@NoArgsConstructor
public abstract class A {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String titleName;

    public A(Long id, String titleName) {
        this.id = id;
        this.titleName = titleName;
    }
}

@Entity
@Getter
@NoArgsConstructor
public class A_a extends A{
    private String name;

    public A_a(Long id, String titleName, String name) {
        super(id, titleName);
        this.name = name;
    }
}

@Entity
@Getter
@NoArgsConstructor
public class A_b extends A{
    private String name;
    private Long price;

    public A_b(Long id, String titleName, String name, Long price) {
        super(id, titleName);
        this.name = name;
        this.price = price;
    }
}

```
이런식으로 상속을 받아 사용하는 친구인데 여러 클래스가 A를 상속받아서 사용하게 된다.

``` java 
@Getter
@NoArgsConstructor
public class ARequest {
	private A_aRequest aARequest;
	private A_bRequest aBRequest;

	public ARequest(A_aRequest aARequest, A_bRequest aBRequest) {
		this.aARequest = aARequest;
		this.aBRequest = aBRequest;
	}
}
```
그래서 이렇게 reqeust를 받는 방법을 생각했는데 request를 받을때 하나는 무조건 null이 되고<br/>
어떤게 null인지 체크를 한 후 저장하는 로직을 짜야했다.<br/>
근데 만약 상속받는 클래스가 두개가 아니고 10개라면?? 더 많다면?? 그때마다 어떤게 null인지 체크를 해주는 로직을 넣어줘야했다.<br/>
나는 그게 너무 불편하다고 생각해서 다른 방법이 없나 찾고 있다가 @JsonTypeInfo 이런 어노테이션을 찾을 수 있었고 바로 적용 시켜봤다.

``` java
@JsonTypeInfo(
	use = JsonTypeInfo.Id.NAME,
	property = "type"
)
@JsonSubTypes(value = {
	@JsonSubTypes.Type(value = A_aRequest.class, name = "A_a"),
	@JsonSubTypes.Type(value = A_bRequest.class, name = "A_b")
})
public interface AInterface {
	A toA();
}

@Getter
@NoArgsConstructor
public class A_bRequest implements AInterface {
	private Long id;
	private String titleName;
	private String name;
	private Long price;

	public A_bRequest(Long id, String titleName, String name, Long price) {
		this.id = id;
		this.titleName = titleName;
		this.name = name;
		this.price = price;
	}

	@Override
	public A toA() {
		return new A_b(id, titleName, name, price);
	}
}

@Getter
@NoArgsConstructor
public class A_aRequest implements AInterface {
	private Long id;
	private String titleName;
	private String name;

	public A_aRequest(Long id, String titleName, String name) {
		this.id = id;
		this.titleName = titleName;
		this.name = name;
	}

	@Override
	public A toA() {
		return new A_a(id, titleName, name);
	}
}
```

``` java
@JsonSubTypes(value = {
	@JsonSubTypes.Type(value = A_aRequest.class, name = "A_a"),
	@JsonSubTypes.Type(value = A_bRequest.class, name = "A_b")
})
```
@JsonTypeInfo는 인터페이스나 추상 클래스에 적용할 수 있다, 위 코드처럼 이렇게 설정을 하면 
![](img/하나의%20class로%20여러가지의%20dto를%20받는%20방법(JSONTypeInfo)%20다형성%20가져가기(1).png)
설정한 name을 type에 넣어서 request를 보내주게되면 알아서 매핑을 해준다.<br/> 
- type : "A_a" -> A_aRequest.class 
- type : "A_b" -> A_bRequest.class

``` java
@JsonTypeInfo(
	use = JsonTypeInfo.Id.NAME,
	property = "type"
)
@JsonSubTypes(value = {
	@JsonSubTypes.Type(value = A_aRequest.class, name = "A_a"),
	@JsonSubTypes.Type(value = A_bRequest.class, name = "A_b")
})
@Getter
@NoArgsConstructor
public abstract class AInterface {
	private Long id;
	private String titleName;
	private String name;

	protected AInterface(Long id, String titleName, String name) {
		this.id = id;
		this.titleName = titleName;
		this.name = name;
	}

	public abstract A toA();
}


@Getter
@NoArgsConstructor
public class A_bRequest extends AInterface {
	private Long price;

	public A_bRequest(Long id, String titleName, String name, Long price) {
		super(id, titleName, name);
		this.price = price;
	}

	@Override
	public A toA() {
		return new A_b(getId(), getTitleName(), getName(), price);
	}
}

@Getter
@NoArgsConstructor
public class A_aRequest extends AInterface {
	private String abc;

	public A_aRequest(Long id, String titleName, String name, String abc) {
		super(id, titleName, name);
		this.abc = abc;
	}

	@Override
	public A toA() {
		return new A_a(getId(), getTitleName(), getName());
	}
}
```
이렇게 abstract 클래스로 만들고 공통 변수를 모아서 사용할 수 있다.


## api 하나에 여러개의 객체를 보내야한다고?
저장을 다형성을 가지게 되었으니 데이터를 내보내는것도 다형성을 가지면 좋겠다고 생각해다.
``` java

@JsonTypeInfo(
	use = JsonTypeInfo.Id.NAME,
	property = "type"
)
@JsonSubTypes(value = {
	@JsonSubTypes.Type(value = A_aResponse.class, name = "A_a"),
	@JsonSubTypes.Type(value = A_bResponse.class, name = "A_b")
})
public abstract class AResponse {
	public static AResponse getA(A a) {
        // 겍체 확인
		if (a instanceof A_a) {
			return A_aResponse.from(a);
		} else {
			return A_bResponse.from(a);
		}
	}
}

@Getter
@NoArgsConstructor
public class A_bResponse extends AResponse {
	private Long id;
	private String titleName;
	private String name;
	private Long price;

	public A_bResponse(Long id, String titleName, String name, Long price) {
		this.id = id;
		this.titleName = titleName;
		this.name = name;
		this.price = price;
	}

    // 객체변환
	public static AResponse from(A a) {
		A_b a_b = (A_b)a;
		return new A_bResponse(a_b.getId(), a_b.getTitleName(), a_b.getName(), a_b.getPrice());
	}
}

@Getter
@NoArgsConstructor
public class A_aResponse extends AResponse {
	private Long id;
	private String titleName;
	private String name;

	public A_aResponse(Long id, String titleName, String name) {
		this.id = id;
		this.titleName = titleName;
		this.name = name;
	}

    // 객체 변환
	public static AResponse from(A a) {
		A_a a_a = (A_a)a;
		return new A_aResponse(a_a.getId(), a_a.getTitleName(), a_a.getName());
	}

}
```
구성은 저장을 할때와 거의 비슷하다.<br/>
getA() 메소드에서 각 객체가 어떤 객체인지 확인을 한 후 그 객체안에서 변환과정을 거쳐 반환하게 된다.<br/>

``` java
@JsonTypeInfo(
	use = JsonTypeInfo.Id.NAME,
	property = "type"
)
@JsonSubTypes(value = {
	@JsonSubTypes.Type(value = A_aResponse.class, name = "A_a"),
	@JsonSubTypes.Type(value = A_bResponse.class, name = "A_b")
})
```
사실 response에는 이 어노테이션이 없어도 되긴 하는데 그렇게 되면 
![](img/하나의%20class로%20여러가지의%20dto를%20받는%20방법(JSONTypeInfo)%20다형성%20가져가기(2).png)
swagger에서 이러한 정보를 얻지 못하게되어서 넣는게 좋다고 생각한다.

## option 설명
- ``` java
    // use = NAME 일떄
    @JsonTypeInfo(
        use = JsonTypeInfo.Id.NAME,
        include = JsonTypeInfo.As.EXISTING_PROPERTY,
        property = "type",
        visible = true,
        defaultImpl = MainClass.class
    )
    @JsonSubTypes({
        @JsonSubTypes.Type(value = Sub1MainClass.class, name = "Sub1"),
        @JsonSubTypes.Type(value = Sub2MainClass.class, name = "Sub2")
    })

    // use = CLASS 일때 
    @JsonTypeInfo(
        use = JsonTypeInfo.Id.CLASS,
        include = JsonTypeInfo.As.PROPERTY,
        property = "className"
    )
    @JsonSubTypes({
        @JsonSubTypes.Type(value = Sub1MainClass.class),
        @JsonSubTypes.Type(value = Sub2MainClass.class)
    })

    ```
    - @JsonTypeInfo: 이 애노테이션이 붙은 클래스를 기반으로 만들어진 객체를 직렬화/역직렬화할 때, 어떻게 객체의 유형 정보를 처리할 것인지를 정의합니다.

    - use: 객체의 유형을 어떻게 식별할 것인지를 지정합니다. JsonTypeInfo.Id.NAME는 유형 정보를 문자열로 처리하겠다는 뜻입니다.

    - include: 유형 정보를 어디에 포함시킬 것인지를 지정합니다. JsonTypeInfo.As.EXISTING_PROPERTY는 기존의 속성에 유형 정보를 포함하겠다는 뜻입니다.

    - property: 유형 정보로 사용할 속성의 이름을 지정합니다. 이 예제에서는 "type" 속성을 유형 정보로 사용합니다.

    - visible: 유형 속성이 직렬화된 JSON에서 보여질지 여부를 지정합니다. true인 경우, 유형 속성이 보여집니다.

    - defaultImpl: 이 속성은 역직렬화할 때 유형 정보를 찾지 못하면 사용할 기본 구현 클래스를 지정합니다. 여기서는 MainClass가 기본 구현 클래스로 지정되어 있습니다.

    - @JsonSubTypes: @JsonTypeInfo 애노테이션이 붙은 클래스가 다형성을 갖는 경우, 어떤 서브타입들이 있는지를 정의합니다. 각 서브타입은 @JsonSubTypes.Type 애노테이션으로 정의합니다.
    - value: 서브타입의 클래스를 지정합니다.

    - name: 서브타입의 이름을 지정합니다. 이 이름이 @JsonTypeInfo의 property에서 지정한 속성의 값으로 사용됩니다.

### include Option 
- JsonTypeInfo.As.WRAPPER_ARRAY: 유형 정보를 포함하는 배열을 생성하여 유형 정보와 인스턴스 데이터를 감쌉니다. 예를 들어, 인스턴스가 {"property": "value"}라면, 이 옵션을 사용하면 ["typeValue", {"property": "value"}]와 같이 됩니다.

- JsonTypeInfo.As.PROPERTY: 유형 정보를 인스턴스의 한 속성으로 취급합니다. 이 경우, 유형 정보는 JSON 객체 내에 추가되며, 이 속성의 이름은 property 속성에 지정된 값입니다. 예를 들어, 인스턴스가 {"property": "value"}이고, property 속성의 값이 "type"이라면, 이 옵션을 사용하면 {"type": "typeValue", "property": "value"}와 같이 됩니다.

- JsonTypeInfo.As.WRAPPER_OBJECT: 유형 정보를 포함하는 새 객체를 생성하여 인스턴스 데이터를 감쌉니다. 예를 들어, 인스턴스가 {"property": "value"}라면, 이 옵션을 사용하면 {"typeValue": {"property": "value"}}와 같이 됩니다.

- JsonTypeInfo.As.EXTERNAL_PROPERTY: 유형 정보는 인스턴스와 같은 수준의 외부 속성으로 취급됩니다. 예를 들어, 인스턴스가 {"property": "value"}이고, property 속성의 값이 "type"이라면, 이 옵션을 사용하면 {"type": "typeValue", "property": "value"}와 같이 됩니다. 하지만 이 경우, 유형 정보와 인스턴스 데이터는 서로 다른 JSON 객체로 취급됩니다.

- JsonTypeInfo.As.EXISTING_PROPERTY: 이 옵션은 JsonTypeInfo.As.PROPERTY와 유사하지만, 유형 정보가 이미 존재하는 속성에 묶인다는 점이 다릅니다. 이 속성이 지정된 경우, 역직렬화 시에 해당 속성이 이미 있는지 확인하고, 없다면 역직렬화에 실패합니다.


### use Option
- JsonTypeInfo.Id.NAME: 이 값은 객체의 유형 정보를 문자열로 식별한다는 것을 의미합니다. 이때 문자열은 보통 클래스의 간단한 이름(Simple Name) 또는 @JsonTypeName 애노테이션이 적용된 이름을 사용합니다. 예를 들어, @JsonTypeName("myClass")가 적용된 MyClass 클래스의 경우, 유형 정보는 "myClass"라는 이름으로 저장됩니다.

- JsonTypeInfo.Id.CLASS: 이 값은 객체의 유형 정보를 전체 클래스 이름(Fully Qualified Class Name, FQCN)으로 식별한다는 것을 의미합니다. 예를 들어, MyClass가 "com.mycompany.MyClass" 패키지에 있다면, 유형 정보는 "com.mycompany.MyClass"가 됩니다.

- JsonTypeInfo.Id.MINIMAL_CLASS: 이 값은 객체의 유형 정보를 최소한의 클래스 이름으로 식별한다는 것을 의미합니다. 즉, 패키지 이름을 제외한 클래스 이름만을 사용합니다. 예를 들어, MyClass가 "com.mycompany.MyClass" 패키지에 있다면, 유형 정보는 ".MyClass"가 됩니다.

- JsonTypeInfo.Id.CUSTOM: 이 값은 사용자 정의 유형 식별자를 사용한다는 것을 의미합니다. 이 경우, @JsonTypeIdResolver 애노테이션을 사용하여 사용자 정의 유형 식별자 리졸버를 제공해야 합니다.

- JsonTypeInfo.Id.NONE: 이 값은 유형 정보를 저장하지 않는다는 것을 의미합니다. 이는 기본적으로 다형성을 지원하지 않습니다.

# 참고
- https://kdohyeon.tistory.com/99
- https://www.logicbig.com/tutorials/misc/jackson/json-type-info-with-logical-type-name.html