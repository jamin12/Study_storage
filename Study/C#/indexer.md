# indexer
## __<span style="color:#9999ff">정의</span>__
- 클래스 객체의 데이타를 배열 형태로 인덱스를 써서 겟세스할 수 있게 해준다. 마치 배열처럼 []를 사용하여 클래스내의 특정 필드 데이타를 엑세스 하는 것이다.
- ``` c#
    MyClass cls = new MyClass();
    cls[0] = "First";
- this[]를 써서 클래스 속성처럼 get과 set을 정의한다.
- 클래스 내부의 어떤 데이타를 리턴하는지 클래스 디자인시 필요에 따라 정하게 될 것이고, 리턴 데이타 타입도 여러가지로 지정할 수 있다.
- 입력 파라미터인 인덱스도 여러 테이타 타입을 쓸 수 있는데, 주로 int나 string 타입을 사용하여 인덱스값을 주는 것이 일반적이다.
- ``` c#
    class MyClass
    {
        private const int MAX = 10;
        private string name;

        // 내부의 정수 배열 데이타
        private int[] data = new int[MAX];

        // 인덱서 정의. int 파라미터 사용
        public int this[int index] 
        {
            get
            {            
                if (index < 0 || index >= MAX)
                {
                    throw new IndexOutOfRangeException();
                }
                else
                {
                    // 정수배열로부터 값 리턴
                    return data[index];
                }
            }
            set
            {
                if (!(index < 0 || index >= MAX))
                {
                    // 정수배열에 값 저장
                    data[index] = value;
                }
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            MyClass cls = new MyClass();

            // 인덱서 set 사용
            cls[1] = 1024;

            // 인덱서 get 사용
            int i = cls[1];
        }
    }
# 참고
- http://www.csharpstudy.com/CSharp/CSharp-indexer.aspx