# Mockito
## Mock
- 단위 테스트를 위한 자바 Mocking프레임워크 중 하나이다.
- mock()/ @Mock : Mock만들기
  - answer/MockSetting를 통해 동작하는 방법을 지정할 수 있다.
  - when() / given()는 mock이 어떻게 행동할지 지정한다.
## Spy
- spy() / @spy: partial mocking, 실제 메소드가 호출되지만 verify와 stub가 가능하다. 
  - @injectionMock: @Spy또는 @Mock 어노테이션에 달린 spies/mocks fields를 자동으로 주입한다.
  - verify(): 주어진 argument로 메소드가 호출되었는지 확인하다. 가변적인 argument에 대한 매칭을 사용할 수 있다.
## Test Double
- 테스팅에서 실제 객체를 대신하여 사용되는 모든 방법을 일컫는다.
- 실제 객체를 사용하기 어렵고 모호할 때, 이를 대신해줄 객체를 만들어 테스트 수행을 돕는다.
1. ### Dummy
   - Dummy 객체는 전달되기만 하고 실제 사용되지는 않는다.
   - 단지 인스턴스화된 객체가 필요할 경우
2. ### Fake
   - 실제로 동작하는 구현체가 있으나, 운영시에는 사용할 수 없는 간단한 현태이다.
   - 보통 List나 Map을 이용해서 DB같은 외부 의존 환경을 대체한다.**복잡한 로직 OR 외부 서비스의 동작을 비교적 단순화하여 구현한 객체**
3. ### Stub
   - Dummy 객체가 마치 실제로 동작하는 것처럼보이게 만들어놓은 객체
   - 모의 객체 생성 및 모의 객체의 동작을 지정하는 것
   - 미리 준비된 answers로 응답하므로 테스트에 사용하기 위해 미리 프록그램된 것 이외의 것에 대해서는 응답하지 않는다.
4. ### Spy
   - Stub의 역할을 가지면서 호출된 내용에 대해 테스트에서 확인하기 위한 약간의 정보를 기록하는 객체이다.
   - 호출 여부를 감시해서 기록했다가 나중에 요청이 들어오면 해당 기록 정보를 전달해준다.
   - 기록정보: 특정객체가 사용됐는가? 그 객체의 예상된 메소드가 정상적으로 호출됐는가?
5. ### Mock
   - Mock 객체는 호출에 대한 기대를 명세하고 해당 내용에 따라 동작하도록 프로그래밍 된 객체이다.

## Stubbing
- 특정 매개변수를 받았을 때 특정 값을 반환하거나 예외를 던지도록 설정할 수 있다.
- ``` java
  @Test
  @DisplayName("아이디 중복으로 인한 유저 등록 실패")
  public void FailToUserCreateIfDuplicateLoginId() throws Exception {
      // findUserByLoginId 메서드가 호출될때 UserDTOAllField를 반환
      when(userServiceImpl.findUserByLoginId("loginid123")).thenReturn(UserDTOAllField);

      assertThrows(DuplicateKeyException.class,
          () -> {
              userServiceImpl.addUser(UserDTOAllField);
          }
      );
  }
- thenReturn() 메서드나 thenThrow() 메서드를 이어 붙이는 구조를 사용하여 동일한 메서드가 여러 번 호출될 때 각각 다르게 행동하도록 할 수 있다.
- ``` java
  @Test
  public void stubbingChaining() {
      // thenReturn() 메서드를 이어 붙여서 29와 31을 반환하게 했다.
      when(mock.length()).thenReturn(29).thenReturn(31);
      
      assertEquals(29, mock.length());
      assertEquals(31, mock.length());
  }
- Mock 객체 메서드의 파라미터 값을 하드 코딩하고 싶지 않다면 Mockito의 Argument Matchers를 이용하면 된다.
- ``` java
  @Test
  public void stubbingGenericArgument() {
      // anyInt() 메서드로 mock 객체의 get() 메서드 파라미터에 타입만 일치한다면
      // 어떠한 값이 와도 beststar를 반환하도록 설정했다.
      when(mock.get(anyInt())).thenReturn("beststar");
      
      assertEquals("beststar", mock.get(29));
      assertEquals("beststar", mock.get(31));
  }

# 참고 
- https://beststar-1.tistory.com/30
- https://cobbybb.tistory.com/16
- https://velog.io/@hanblueblue/spring-boot-%EC%9C%A0%EB%8B%9B-%ED%85%8C%EC%8A%A4%ED%8A%B8-3.-Mockito
#java