# class
## __<span style="color:#9999ff">필드</span>__
- 클래스 안에서 선언된 변수를 필드라고 부른다
- 필드는 일반적으로 클래스 부품 역할을 하며 대부분 private 한정자가 붙는다.
- 필드는 클래스가 가지는 변수 또는 상수가 위치
  - 상수: const를 붙인다.
### __<span style="color:#ff9933">일기 전용 필드(read only)</span>__
- ``` c#
    public readonly decimal Price;
- 대문자로 시작가능
- 클래스 내에 선언된 상수와 비슷하나, 생성자로 초기화가 가능하다
- public 한정자 지정가능
  - 필드는 private 지정 권장
### __<span style="color:#ff9933">상수와 읽기 전용 필드</span>__
- 상수
  - 컴파일 시 데이터가 저장된다.
  - 선언과 동시에 초기화
  - 숫자, boolean, 문자열
  - 항상 static으로 호출됨
- 읽기 전용 필드
  - 런타임 상수, 런타임시 데이터가 저장된다.
  - 선언과 동시 초기화 또는 생성자에 의한 초기화
  - 모든 데이터 형식이 가능
  - static 선언 가능, 일반적으로 인스턴스
## __<span style="color:#9999ff">메서드</span>__
- 클래스 안에서 선언된 함수를 메서드라고 부른다
### __<span style="color:#ff9933">참조로 인수 전달</span>__
- 메서드의 매개 변수 목록에 사용되는 ref 키워드는 인수가 값이 아니라 참조로 전달 됨을 나타낸다.
- ``` c#
    void Method(ref int refArgument)
    {
        refArgument = refArgument + 44;
    }

    int number = 1;
    Method(ref number);
    Console.WriteLine(number);
    // Output: 45
- ref 매개 변수를 사용하려면 메서드 정의와 호출 매서드가 모두 ref키워드를 명시적으로 사용해야한다.
- ``` c#
    // out 정의
    static bool GetData(int a, int b, out int c, out int d)
    {
        c = a + b;
        d = a - b;
        return true;
    }
    // out 사용. 초기화 불필요.
    int c, d;
    bool bret = GetData(10, 20, out c, out d);
- out을 사용하는 파라미터는 메서드 내에서 그 값을 반드시 지정하여 전달하게 되어있다.(반드시 값을 넣어야 한다.)
- out은 사전에 변수를 초기화할 필요는 없다.

### __<span style="color:#ff9933">Named 파라미터</span>__
- 메서드에 파라미터를 전달할 때 일반적으로 파라미터 위치에 따라 순차적으로 파라미터가 넘겨지는데, c# 4.0부터는 위치와 상관없이 파라미터명을 지정하여 파라미터를 전달할 수 있게 하였다.
- ``` c#
    Method1(name: "John", age: 10, score: 90);

### __<span style="color:#ff9933">optional 파라미터</span>__
- 어떤 메서드의 파라미터가 디폴트 값을 갖고 있다면, 메서드 호출 시 이러한 파라미터를 생략하는 것을 허용하였따. 이렇게 디폴트 값을 가진 파라미터를 Optional 파라미터라고 부른다.
- Optional 파라미터는 반드시 파라미터들 중 맨 마지막에 놓여져야 한다.
- 복수개의 optional 파라미터가 있는 경우 반드시 Optional이 아닌 파라미터들 뒤에 위치해야한다.

### __<span style="color:#ff9933">params</span>__
- 파라미터의 갯수를 미리 알 수 없는 경우들도 있는데 이 때 params를 사용한다. 이 params키워드는 가변적인 배열을 인수로 갖게 해주는데 파라미터들 중 반드시 하나만 존재해야하며, 맨 마지막에 위치해야한다.
- ``` c#
    //메서드
    int Calc(params int[] values)

    //사용
    int s = Calc(1,2,3,4);
    s = Calc(6,7,8,9,10,11);
## __<span style="color:#9999ff">속성(property)</span>__
- 클래스에서 필드의 값을 읽거나 쓸 수 있게 제공하는 멤버입니다. 데이터를 손쉽게 접근하고 안전성과 데이터의 유효성을 검사할 수 있습니다.
- ``` c#
    class Data
    {
        private int num = 127;

        public int ByteNumber
        {
            get
            {
                return num;
            }
            set
            {
                if (value >= 0 && value < 256)
                {
                    num = value;
                }
            }
        }
    }
- Date라는 클래스 안에 byteNumber는 속성으로 사용된다. num 필드는 속성 데이터를 백업할 저장소의 역할을 합니다.
- 해당 필드는 속성의 호출을 통해서 필드를 변경할 수 있도록 private를 사용합니다. 
- get 접근자를 사용하여 num필드의 값을 읽습니다. 또한, set접근자를 사용하여 num필드의 값을 설정합니다.
- set 접근자 사용시 속성 형식인 value라는 암시적 매개 변수를 사용하여 num 필드에 값을 할당 합니다.
- ``` c#
    using System;

    class Data
    {
        public int IntNumber { get; set; }
        public string Name { get; set; }
    }
- 속성에 특별한 논리나 조건이 필요하지 않을 경우, 자동 구현 속성을 이용하여 속성 선언을 간단하게 질행할 수 있습니다.
## __<span style="color:#9999ff">이벤트</span>__
- 클래스내에 특정한 일(event)이 일어났을음 외부의 이벤트 가입자 들에게 알려주는 기능을 한다. c#에서 이벤트는 event라는 키워드를 사용하여 표시하며, 클래스 내에서 일종의 필드처럼 정의된다.
- 이벤트에 가입하는 외부 가입자 측에서는 이벤트가 발생했을 때 어떤 명령들을 실행할 지를 지정해 주는데, 이를 이벤트 핸들러라 한다. 이벤트에 가입하기 위해서는 += 연산자를 사용하여 이벤트 핸들러를 이벤트에 추가한다. 반대로 이벤드 핸들러를 삭제하기 위해서는 -=사용한다. 하나의 이벤트에는 여러개의 이벤트핸드러들을 추가할 수 있으며, 이벤트가 발생되면 추가된 이벤트핸들러들을 모두 차례로 호출한다. 
- ``` c#
    // 클래스 내의 이벤트 정의
    class MyButton
    {
        public string Text;
        // 이벤트 정의
        public event EventHandler Click;

        public void MouseButtonDown()
        {
            if (this.Click != null)
            {
                // 이벤트핸들러들을 호출
                Click(this, EventArgs.Empty);
            }
        }
    }

    // 이벤트 사용
    public void Run()
    {
        MyButton btn = new MyButton();
        // Click 이벤트에 대한 이벤트핸들러로
        // btn_Click 이라는 메서드를 지정함
        btn.Click += new EventHandler(btn_Click);
        btn.Text = "Run";
        //....
    }

    void btn_Click(object sender, EventArgs e)
    {
    MessageBox.Show("Button 클릭");
    }
# 참고
- [필드](https://antstudy.tistory.com/66)
- [참조](https://docs.microsoft.com/ko-kr/dotnet/csharp/language-reference/keywords/ref)
- [속성](https://076923.github.io/posts/C-31/)
- [이벤트](http://www.csharpstudy.com/CSharp/CSharp-event.aspx)
- [메서드](http://www.csharpstudy.com/CSharp/CSharp-method.aspx)