# String, StringBuilder, StringBuffer의 차이
## __<span style="color:#9999ff">String  vs  StringBuffer/StringBuilder</span>__
- String 과 Stringbuilder/Strinbuffer 클래스의 가장 큰 차이점은 String은 **불변**의 속성을 갖는다는 점이다.
- ![](https://t1.daumcdn.net/cfile/tistory/99948B355E2F13350F)
- 위와 같이 String은 불변성을 가지기 때문에 변하지 않는 문자열을 자주 읽어들이는 경우 String을 사용해주면 좋은 성능을 기대할 수 있습니다. 그러나 문자열 추가, 수정, 삭제 드으이 연산이 빈번하게 발생하는 알고리즘에 String클래스를 사용하면 힙(Heap)메모리에 많은 임시 가비지(garbage)가 생성되어 힙(Heap)메모리가 부족으로 어플리케이션 성능에 치명적인 영향을 끼치게 됩니다.
</br>
</br>
</br>
- 이를 해결하기위해 Java에서는 가변(mutable)성을 가지는 Stringbuilder/StringBuffer클래스를 도입했습니다. 이 클래스들은 가변성을 가지기 때문에 .append() .delete()등의 API를 이용하여 동일 객체내에서 문자열을 변경하는 것이 가능합니다. 따라서 문자열의 추가,수정,삭제가 빈번하게 발생할 경우라면 String클래스가 아닌 Stringbuilder/Stringbuffer을 사용해야합니다.
- ![](https://t1.daumcdn.net/cfile/tistory/9923A9505E2F133608)
## __<span style="color:#9999ff">StringBuffer  vs  StringBuilder</span>__
- 가장 큰 차이점은 동기화의 유무로써 StringBuffer는 동기화 키워드를 지원하여 멀티쓰레드 환경에서 안전하다는점(thread-saft)입니다. 참고로 String도 불변성을 가지기 때문에 마찬가지로 멀티스레드 환경에서 안전성을 가지고 있습니다.
- 반대로 Stringbuilder는 동기화를 지원하지 않기 때문에 멀티쓰레드 환경에서 사용하는 것은 적합하지 않지만 동기화를 고려하지 않는 만큼 단일쓰레드에서의 성능은 Stringbuffer보다 뛰어납니다.
## __<span style="color:#9999ff">정리</span>__

![](https://t1.daumcdn.net/cfile/tistory/99BE23375E2F133722)

# 참고
- https://ifuwanna.tistory.com/221
#javabasic #java