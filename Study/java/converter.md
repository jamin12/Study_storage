# converter
## Converter와 Formatter
- PropertyEditor는 스프링 초창기에 썼던 데이터 바인딩 인터페이스였지만 후에 이를 대체할 Converter와 Formatter란 데이터 바인딩 인터페이스가 나오게 되었다.
- Converter는 S타입을 T 타입으로 변환할 수 있는 일반적인 변환기이며 Thread-safe하다. Formatter는 Object와 String간의 변환을 담당하며 문자열을 Locale에 따라 다국화하는 기능을 제공한다. 웹 어플리케이션을 제작할 때는 주로 Formatter를 사용한다
## Converter
- Converter 인터페이스의 T Convert(S source) 메서드를 오버라이딩하여 타입 변환
- 타입 S -> 타입 T로 변환시켜 주는 로직을 구현하면 된다.
- ### 인터페이스
    - ``` java
      @FunctionalInterface
      public interface Converter<S, T> {

      	@Nullable
      	T convert(S source);

      	default <U> Converter<S, U> andThen(Converter<? super T, ? extends U> after) {
      		Assert.notNull(after, "After Converter must not be null");
      		return (S s) -> {
      			T initialResult = convert(s);
      			return (initialResult != null ? after.convert(initialResult) : null);
      		};
      	}

      }
- ### 구현체
    - 스프링에서 기ㅏ본적으로 제공하는 컨벝너 중에 String -> Integer으로 타입 변환 해주는 컨버터 구현
    - ``` java 
      @Slf4j
      public class StringToIntegerConverter implements Converter<String, Integer> {

          @Override
          public Integer convert(String source) {

              log.info("StringToIntegerConverter source = {}", source);
              return Integer.parseInt(source);
          }
      }
    - 사용자가 정의한 타입(클래스)으로 변환해주는 컨버터 구현
    - ``` java
      @Getter
      @EqualsAndHashCode
      public class IpPort {

          private String ip;
          private int port;

          public IpPort(String ip, int port) {
              this.ip = ip;
              this.port = port;
          }
      }
      @Slf4j
      public class StringToIpPortConverter implements Converter<String, IpPort> {

          @Override
          public IpPort convert(String source) {
              log.info("StringToIpPortConverter source = {}", source);
              String[] split = source.split(":");
              String ip = split[0];
              Integer port = Integer.parseInt(split[1]);
              return new IpPort(ip, port);
          }
      }
## Formmatter
- 포맷터( Formatter )는 객체를 문자로 변경하고, 문자를 객체로 변경하는 두 가지 기능을 모두 수행합니다.
- String print(T object, Locale locale) : 객체를 문자로 변경합니다.
- T parse(String text, Locale locale) : 문자를 객체로 변경합니다.
- ### 인터페이스
  - ``` java 
      public interface Printer<T> {
      	String print(T object, Locale locale);
      }

      public interface Parser<T> {
      	T parse(String text, Locale locale) throws ParseException;
      }

      public interface Formatter<T> extends Printer<T>, Parser<T> {
      }
- ### 구현체
  - ``` java
      @Slf4j
      public class MyNumberFormatter implements Formatter<Number> {
          @Override
          public Number parse(String text, Locale locale) throws ParseException {
              log.info("text={}, locale={}", text, locale);
              NumberFormat format = NumberFormat.getInstance(locale);
              return format.parse(text);
          }

          @Override
          public String print(Number object, Locale locale) {
              log.info("object={}, locale={}", object, locale);
              return NumberFormat.getInstance(locale).format(object);
          }
      }
## Converter 등록
  - Converter를 등록하기 위해서는 WebMvcConfigurer가 제공하는 addConverter()메서드를 사용하여 Converter를 등록할 수 있다.
  - addConverter(FormatterRegistry registry)
     - FormatterRegistry : 인터페이스 ConveterRegistry의 구현 클래스
  - 참고로 위에서 만든 컨버터(String→Integer)를 등록하면 기본 컨버터가 아닌 등록한 컨버터가 호출된다.
  - ``` java
      @Configuration
      public class WebConfig  implements WebMvcConfigurer {
          @Override
          public void addFormatters(FormatterRegistry registry) {
              registry.addConverter(new StringToIpPortConverter());
              registry.addConverter(new IpPortToStringConverter());
              //추가
              registry.addFormatter(new MyNumberFormatter());
          }
      }
## ConversionService
  -  Converter들을 하나하나 직접 찾아서 타입 변환에 사용하는 것은 매우 불편하기 때문에 스프링은 컨버터들을 모아서 편리하게 사용할 수 있는 기능을 제공하는데 그것이 바로 ConversionService 이다.
  - 등록된 컨버터로 타입 변환을 해주는 기능을 제공한다.
  - Converter와 Formatter 이 두 데이터 바인딩 인터페이스는 위에서 WebMvcConfigurer의 메서드를 통해 ConversionService에 등록된다. ConversionService는 실제 데이터 변환이 일어나는 곳이며 이 ConversionService를 통해 데이터 바인딩이 일어나게 된다. 참고로 스프링 부트에서는 ConversionService는 WebConversionService 스프링 빈으로 자동 주입된다. (WebConversionService는 DefaultFormattingConversionService를 상속받은 클래스)
  - ``` java
      public interface ConversionService {

      	boolean canConvert(@Nullable Class<?> sourceType, Class<?> targetType);
      	boolean canConvert(@Nullable TypeDescriptor sourceType, TypeDescriptor targetType);

      	@Nullable
      	<T> T convert(@Nullable Object source, Class<T> targetType);

      	@Nullable
      	Object convert(@Nullable Object source, @Nullable TypeDescriptor sourceType, TypeDescriptor targetType);

      }
## WebConversionService
- 스프링 부트가 제공하는 클래스인 WebConversionService는 Formatter와 Converter를 자동으로 등록해준다. 따라서 위에 Converter와 Formatter를 등록하기 위해 만들었던 WebMvcConfigurer를 작성할 필요가 없다. (즉, Formatter와 Converter를 스프링 빈으로 등록하면 자동적으로 ConversionService에 등록되게 된다)
- ``` java
  @Component
  public class EventFormatter implements Formatter<Event> {

      @Override
      public Event parse(String text, Locale locale) throws ParseException {
          return new Event(Integer.parseInt(text));
      }

      @Override
      public String print(Event object, Locale locale) {
          return object.getId().toString();
      }
  }
    //////////////////////////////////////////////////////////////
    public class EventConverter  {

        @Component
        public static class StringToEventConverter implements Converter<String, Event> {
            @Override
            public Event convert(String source){
                return new Event(Integer.parseInt(source));
            }
        }

        @Component
        public static class EventToStringConverter implements Converter<Event, String>{
            @Override
            public String convert(Event source){
                return source.getId().toString();
            }
        }
    }
# 참고
- https://engkimbs.tistory.com/738
- https://jddng.tistory.com/290
- https://develop-writing.tistory.com/105