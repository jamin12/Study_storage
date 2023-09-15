# 자바 박싱 언박싱
1. ### __<span style="color:#ff9933">Wrapper class</span>__
   - 자바는 int, long 같은 변수의 기본 자료형을 제공한다. 하지만 이런 기본 자료형은 컬렉션이나 제네릭에서 제한적으로 사용하능하다.
   - 자바는 각 기본 자료형에 대응되는 래퍼 클래스를 제공한다.
   - |기본 자료형|래퍼 클래스|
       |---|---|
       |boolean|Boolean|
       |short|Short|
       |int|Integer|
       |long|Long|
       |float|Float|
       |double|Double|
       |char|Character|
       |byte|Byte|
1. ### __<span style="color:#ff9933">박싱 언박싱</span>__
- 박싱은 기본 자료형의 데이터를 대응되는 래퍼 클래스로 만드는 동작을 의미한다.
- ``` java
        public class Boxing {
        public static void main(String[] args) {
            
            int i = 10;
            Integer wI = new Integer(a); // int Boxing 동작
            
            double d = 3.14;
            Double wD = new Double(d); // double Boxing 동작
            
            boolean b = true;
            Boolean wB = new Boolean(b); // boolean Boxing 동작
        }
    }
- 언박싱은 동작은 래퍼 클래스에서 기본 자료형으로의 변환을 의미한다.
- ``` java
        public class Boxing {
        public static void main(String[] args) {
            
            Integer wI = new Integer(10);
            int i = wI.intValue();     // Integer Unboxing 동작
            
            Double wD = new Double(3.14);
            double d = wD.doubleValue(); // Double Unboxing 동작
            
            Boolean wB = new Boolean(true);
            boolean b = wB.booleanValue(); // Boolean Unboxing 동작
        }
    }

# 참고
- https://soft.plusblog.co.kr/56
#javabasic #java