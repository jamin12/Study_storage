# Stream Api의 고급 활용 및 사용시 주의할 점
## __<span style="color:#9999ff">FlatMap을 통한 중첩 제거</span>__
1. __<span style="color:#ff9933">[FaltMap 이란?]</span>__
   - 만약 우리가 처리해야 하는 데이터가 2중 배열 또는 2중 리스트로 되어 있고, 이를 1차원으로 처리해야 한다면 어떻게 해야 할까? 이러한 경우에 map을 이용해도 결과는 2중 Stream형태일 것이다. 이처럼 중첩 구조를 한 단계 제거하기 위한 중간 연산이 필요한데, 이것이 바로 flatMap이다. flatMap은 funtion함수형 인터페이스를 매개 변수로 받고 있다.
   - 예를 들어 다음과 같이 2중 리스트가 존재한다고 할 때, 이를 1중 리스트로 변환하기 위해서 flatMap을 이용할 수 있다.
   - ``` java
        // flatMap 함수 
        <R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper); 
        // [[a], [b]] 
        List<List<String>> list = Arrays.asList(Arrays.asList("a"), Arrays.asList("b"));
        // [a, b] 
        List<String> flatList = list.stream() .flatMap(Collection::stream) .collect(Collectors.toList());
2. __<span style="color:#ff9933">[FaltMap의 동작 방식]</span>__
   - 예를 들어 ["Hello","World"]를 갖는 String의 List가 존재한다고 할 때, 이를 1개의 알파벳씩을 갖도록 String으로 나누고 중복된 알파벳을 갖지 않는 List로 변환하는 작업을 한다고 하자. 이러한 경우 Map을 이용하면 해결이 불가능하고 flatMap을 이용해야만 하는데, 두 함수의 동작 방식에 대해 자세히 알고 넘어가도록 하자
   - Map을 적용할 경우
     - ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcWBsve%2FbtqUuMPcQhG%2FEKVs0ZmVB5IwhETi4nN3g1%2Fimg.jpg)
       - Hello가 split("")에 의해 ["H", "e", "l", "l", "o"] 로 분리되고, World가 split("")에 의해 ["W", "o", "r", "l", "d"]로 분리된다.
       - map에 의해 Stream의 소스가 ["H", "e", "l", "l", "o"] 와 ["W", "o", "r", "l", "d"] 로 변환된다.
       - distinct()에 의해 중복된 소스가 제거된다. (해당 사항 없음)
       - 2개의  ["H", "e", "l", "l", "o"] 와 ["W", "o", "r", "l", "d"]가 collect(toList())에 의해 수집된다.
     - 위와 같은 처리과정을 통해 2개의 String[]을 요소라 갖는 리스트List<String[]>가 생성된다. 하지만 우리가 원했던 List\<String>이 아니며 이러한 경우에는 map으로 해결이 불가능하다. 위의 과정에서 필요한 것은 map에 의해 변환된 Stream의 소스인 String[]을 String으로 falt하게 나열하는 것이며 이러한 필요에 의해 flatMap이 등장하게 되었다.
   - flatMap을 적용할 경우
     - ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FmmDug%2FbtqUwzaL8Zu%2FAd2HK6e1S2B6pfXMNM8WeK%2Fimg.jpg)
       - Hello가 split("")에 의해 ["H", "e", "l", "l", "o"] 로 분리되고, World가 split("")에 의해 ["W", "o", "r", "l", "d"]로 분리된다.
       - Arrays.stream(T[] array)를 사용해 ["H", "e", "l", "l", "o"] 와 ["W", "o", "r", "l", "d"]를 각각 Stream<String>으로 만든다.
       - flatMap()을 사용해 여러 개의 Stream<String>을 1개의 Stream<String>으로 평평하게 합치고, Stream의 소스는 ["H", "e", "l", "l", "o", W", "o", "r", "l", "d"] 가 된다.
       - distinct()에 의해 중복된 소스(l, o)가 제거된다.
       - 중복이 제거된 ["H", "e", "l", "o", "W", "r", "d"]가 collect(toList())에 의해 수집된다.
     - 3번 과정에서 map을 사용하면 평평하게 펼치지 못하므로 Stream<Stream\<String>>이 생성되었다. 하지만flatMap을 이요함으로써 알파벳 String을 요소로 갖는 리스트 List\<String>을 생성할 수 있었다.