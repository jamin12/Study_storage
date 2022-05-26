# SQL삽입
## __<span style="color:#9999ff">개요</span>__
- 데이터베이스와 연동된 웹 응용프로그램에서 입력된 데이터에 대한 유효성 검증을 하지 않을 경우, 공격자가 입력 폼 및 URL입력란에 SQL문을 삽입하여 DB로부터 정보를 열람하거나 조작할 수 있는 보안 약점을 말한다.
- 개발자가 의도하지 않은 쿼리가 생성되어 정보유출에 악용될 수 있다
## __<span style="color:#9999ff">보안대책</span>__
- preparedStatment객체 등을 이용하여 DB에 컴파일 된 쿼리문(상수)을 전달하는 방법을 사용한다.
- preparedStatment를 사용하는 경우에는 DB쿼리에 사용되는 외부 입력값에 대하여 특수문자 및 쿼리 예약어를 필터링하고 스트러츠(struts), 스프링(Spring)등과 같은 프레임워크를 사용하는 경우에는 외부 입력값 검증모듈 및 보안모듈을 상황에 맞추어 적절하게 사용한다.
> preparedStatment: 컴파일된 쿼리 객체로 MYSQL, Oracle 등등에서 지원하며 JAVA의 JDBC Perl의 DBI 를 이용하여 사용가능
## __<span style="color:#9999ff">코드 예제</span>__
- 안전하지 않은 코드
- ``` java
    //외부로부터 입력받은 값을 검증 없이 사용할 경우 안전하지 않다.
    String gubun = request.getParameter("gubun");
    ......
    String sql = "SELECT * FROM board WHERE b_gubun = '" + gubun + "'";
    Connection con = db.getConnection();
    Statement stmt = con.createStatement();
    //외부로부터 입력받은 값이 검증 또는 처리 없이 쿼리로 수행되어 안전하지 않다.
    ResultSet rs = stmt.executeQuery(sql); 
- 파라미터(parameter)를 받는 preparedStatment객체를 상수 스트링으로 생성하고 파라미터 부분을 setString, setParameter등의 메소드로 설정하여, 외부의 입력이 쿼리문의 구조를 바꾸는 것을 방지해야 한다.
- ``` java
    String gubun = request.getParameter("gubun");
    ......
    //1. 사용자에 의해 외부로부터 입력받은 값은 안전하지 않을 수 있으므로, PreparedStatement
    사용을 위해 ?문자로 바인딩 변수를 사용한다.
    String sql = "SELECT * FROM board WHERE b_gubun = ?";
    Connection con = db.getConnection();
    //2. PreparedStatement 사용한다.
    PreparedStatement pstmt = con.prepareStatement(sql);
    //3. PreparedStatement 객체를 상수 스트링으로 생성하고, 파라미터 부분을 setString 등의
    메소드로 설정하여 안전하다.
    pstmt.setString(1, gubun);
    ResultSet rs = pstmt.executeQuery();

# 경로 조작 및 자원 삽입
## __<span style="color:#9999ff">개요</span>__
- 검증되지 않은 외부 입력값을 통해 파일 및 서버 등 시스템 자원에 대한 접근 혹은 식별을 허용할 경우, 입력값 조작을 통해 시스템이 보호하는 자원에 임의로 접근할 수 있는 보안약점이다.
## __<span style="color:#9999ff">보안대책</span>__
- 외부의 입력을 자원(파일,소켓의 포트 등)의 식별자로 사용하는 경우 적절한 검증을 거치도록 하거나, 사전에 정의된 적합한 리스트에서 선택되도록 한다. 특히 외부의 입력이 파일명인 경우에는 경로순회 공격의 위험이 있는 문자(" / \ ... 등)을 제거할 수 있는 필터를 이용한다.
## __<span style="color:#9999ff">코드예제</span>__
- ../../../rootFile.txt 와같은 값이 들어오지 못하게 해야한다.
- 안전하지 않은 코드
- ``` java
    //외부로부터 입력받은 값을 검증 없이 사용할 경우 안전하지 않다.
    String fileName = request.getParameter("P");
    BufferedInputStream bis = null;
    BufferedOutputStream bos = null;
    FileInputStream fis = null;
    try {response.setHeader("Content-Disposition", "attachment;filename="+fileName+";");}
    ...
    //외부로부터 입력받은 값이 검증 또는 처리 없이 파일처리에 수행되었다.
    fis = new FileInputStream("C:/datas/" + fileName);
    bis = new BufferedInputStream(fis);
    bos = new BufferedOutputStream(response.getOutputStream()); 
- 안전한 코드
- ``` java
    String fileName = request.getParameter("P");
    BufferedInputStream bis = null;
    BufferedOutputStream bos = null;
    FileInputStream fis = null;
    try {
    response.setHeader("Content-Disposition", "attachment;filename="+fileName+";");
    ...
    // 외부 입력받은 값을 경로순회 문자열(./￦)을 제거하고 사용해야한다.
    filename = filename.replaceAll("￦￦.", "").replaceAll("/", "").replaceAll("￦￦￦￦", "");
    fis = new FileInputStream("C:/datas/" + fileName);
    bis = new BufferedInputStream(fis);
    bos = new BufferedOutputStream(response.getOutputStream());
    int read;
    while((read = bis.read(buffer, 0, 1024)) != -1) {
    bos.write(buffer,0,read);
    }
    }

# 크로스사이트 스크립트
## __<span style="color:#9999ff">개요</span>__
- 웹 페이지에 악의적인 스크립트를 포함시켜 사용자 측에서 실행되게 유도할 수 있다. 예를 들어 검증되지 않은 외부 입력이 동적 웹페이지 생성에 사용될 경우, 전송된 동적 웹페이지를 열람하는 접속자의 권한으로 부적절한 스크립트가 수행되어 정보유출 등의 공격을 유발할 수 있다.
## __<span style="color:#9999ff">보안대책</span>__
- 외부 입력값 또는 출력값에 스크립트가 삽입되지 못하도록 문자열 치환 함수를 사용하여& < > " ' /( ) 등을 &amp; &lt; &gt; &quot; &#x27; &#x2F; &#x28; &#x29;로 치환하거나 크로스사이트 스크립트 방지 라이브러리를 활용한다.
## __<span style="color:#9999ff">코드예제</span>__
- 안전하지 않은 코드
- ``` java
    <% String keyword = request.getParameter("keyword"); %>
    //외부 입력값에 대하여 검증 없이 화면에 출력될 경우 공격스크립트가 포함된 URL을 생성 할 수 있어
    안전하지 않다.(Reflected XSS)
    검색어 : <%=keyword%>
    //게시판 등의 입력form으로 외부값이 DB에 저장되고, 이를 검증 없이 화면에 출력될 경우
    공격스크립트가 실행되어 안전하지 않다.(Stored XSS)
    검색결과 : $ {m.content}
    <script type="text/javascript">
    //외부 입력값에 대하여 검증 없이 브라우저에서 실행되는 경우 서버를 거치지 않는 공격스크립트가
    포함된 URL을 생성 할 수 있어 안전하지 않다. (DOM 기반 XSS)
    document.write("keyword:" + <%=keyword%>);
    </script>
- 안전한 코드
- ``` java
    <% String keyword = request.getParameter("keyword"); %>
    // 방법1. 입력값에 대하여 스크립트 공격가능성이 있는 문자열을 치환한다.
    keyword = keyword.replaceAll("&", "&amp;");
    keyword = keyword.replaceAll("<", "&lt;");
    keyword = keyword.replaceAll(">", "&gt;");
    keyword = keyword.replaceAll("\"", "&quot;");
    keyword = keyword.replaceAll("'", "&#x27;");
    keyword = keyword.replaceAll("/", "&#x2F;");
    keyword = keyword.replaceAll("(", "&#x28;"); 
    keyword = keyword.replaceAll(")", "&#x29;");
    검색어 : <%=keyword%>
    //방법2. JSP에서 출력값에 JSTL c:out 을 사용하여 처리한다.
    <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
    <%@ taglib uri="http://java.sun.com/jsp/jstl/functions" prefix="fn" %>
    검색결과 : <c:out value="$ {m.content}"/>
    <script type="text/javascript">
    //방법3. 잘 만들어진 외부 라이브러리를 활용(NAVER Lucy-XSS-Filter, OWASP ESAPI,
    OWASP Java-Encoder-Project)
    document.write("keyword:"+
    <%=Encoder.encodeForJS(Encoder.encodeForHTML(keyword))%>);
    </script>

# 위험한 형식 파일 업로드
## __<span style="color:#9999ff">개요</span>__
- 서버 측에서 실행될 수 있는 스크립트 파일(asp,jsp 파일 등)이 업로드가능하고, 이 파일을 공격자가 웹을 통해 직접 실행시킬 수 있는 경우, 시스템 내부명령어를 실행하거나 외부와 연결하여 시스템을 제어할 수 있는 보안 약점이다.
## __<span style="color:#9999ff">보안대책</span>__
- 화이트리스트 방식으로 허용된 확장자만 업로드를 허용한다. 업로드 되는 파일을 저장할 떄에는 파일명과 확장자를 외부 사용자가 추측할 수 없는 문자열로 변경하여 저장하며, 저장경로는 'web document root'밖에 위치시켜서 공격자의 웹을 통한 직접 접근을 차단한다. 또한 파일 실행 여부를 설정할 수 있는 경우 실행 속성을 제거한다.
## __<span style="color:#9999ff">코드예제</span>__
- 안전하지 않은 코드
- ``` java
    MultipartRequest multi
    = new MultipartRequest(request,savePath,sizeLimit,"euc-kr",new
    DefaultFileRenamePolicy());
    ......
    //업로드 되는 파일명을 검증없이 사용하고 있어 안전하지 않다.
    String fileName = multi.getFilesystemName("filename");
    ......
    sql = " INSERT INTO
    board(email,r_num,w_date,pwd,content,re_step,re_num,filename) "
    + " values ( ?, 0, sysdate(), ?, ?, ?, ?, ? ) ";
    preparedStatement pstmt = con.prepareStatement(sql);
    pstmt.setString(1, stemail);
    pstmt.setString(2, stpwd); 
    pstmt.setString(3, stcontent);
    pstmt.setString(4, stre_step);
    pstmt.setString(5, stre_num);
    pstmt.setString(6, fileName);
    pstmt.executeUpdate();
    Thumbnail.create(savePath+"/"+fileName, savePath+"/"+"s_"+fileName, 150);

- 안전한 코드
- ``` java
    MultipartRequest multi
    = new MultipartRequest(request,savePath,sizeLimit,"euc-kr",new
    DefaultFileRenamePolicy());
    ......
    String fileName = multi.getFilesystemName("filename");
    if (fileName != null) {
    //1.업로드 파일의 마지막 “.” 문자열의 기준으로 실제 확장자 여부를 확인하고, 대소문자 구별을
    해야한다.
    String fileExt =
    FileName.substring(fileName.lastIndexOf(".")+1).toLowerCase();
    //2.되도록 화이트 리스트 방식으로 허용되는 확장자로 업로드를 제한해야 안전하다.
    if (!"gif".equals(fileExt) && !"jpg".equals(fileExt) && !"png".equals(fileExt))
    {
    alertMessage("업로드 불가능한 파일입니다.");
    return;
    }
    }
    ......
    sql = " INSERT INTO
    board(email,r_num,w_date,pwd,content,re_step,re_num,filename) "
    + " values ( ?, 0, sysdate(), ?, ?, ?, ?, ? ) ";
    PreparedStatement pstmt = con.prepareStatement(sql);
    ......
    Thumbnail.create(savePath+"/"+fileName, savePath+"/"+"s_"+fileName, 150);

# 보안기능 결정에 사요되는 부적절한 입력값
## __<span style="color:#9999ff">개요</span>__
- 응용프로그램이 외부 입력값에 대한 신뢰를 전제로 보호메커니즘을 사용하는 경우 공격자가 입력값을 조작할 수 있다면 보호 메커니즘을 우회할 수 있게 된다.
- 개발자들이 흔히 쿠키, 환경변수 또는 히든필드와 같은 입력값이 조작될 수 없다고 가정 하지만
공격자는 다양한 방법으로 이러한 입력값들을 변경할 수 있고 조작된 내용은 탐지되지 않을 수
있다. 인증이나 인가와 같은 보안결정이 이런 입력값(쿠키, 환경변수, 히든필드 등)에 기반해 수행
되는 경우 공격자는 이런 입력값을 조작하여 응용프로그램의 보안을 우회할 수 있으므로 충분한
암호 화, 무결성 체크를 수행하고 이와 같은 메커니즘이 없는 경우엔 외부사용자에 의한 입력값을
신뢰해서는 안 된다.
## __<span style="color:#9999ff">보안 대책</span>__
- 상태정보나 민감한 데이터 특히 사용자 세션정보와 같은 중요한 정보는 서버에 저장하고 보안확인
절차도 서버에서 실행한다. 보안설계관점에서 신뢰할 수 없는 입력값이 응용프로그램 내부로
들어올 수 있는 지점과 보안결정에 사용되는 입력값을 식별하고 제공되는 입력값에 의존할 필요가
없는 구조 로 변경할 수 있는지 검토한다.
## __<span style="color:#9999ff">코드예제</span>__
- 안전하지 않은 코드
- ``` java
    <input type="hidden" name="price" value="1000"/>
    <br/>품목 : HDTV
    <br/>수량 : <input type="hidden" name="quantity" />개
    <br/><input type="submit" value="구입" />
    try {
    // 서버가 보유하고 있는 가격(단가) 정보를 사용자 화면에서 받아서 처리
    price = request.getParameter("price");
    quantity = request.getParameter("quantity");
    total = Integer.parseInt(quantity) * Float.parseFloat(price);
    } catch (Exception e) {
- 안전한 코드
- ``` java
    <input type="hidden" name="price" value="1000"/>
    <br/>품목 : HDTV
    <br/>수량 : <input type="hidden" name="quantity" />개
    <br/><input type="submit" value="구입" />
    ......
    try {
    item = request.getParameter(“item”);
    // 가격이 아니라 item 항목을 가져와서 서버가 보유하고 있는 가격정보를 이용하여 전체 가격을 계산
    price = productService.getPrice(item);
    quantity = request.getParameter("quantity");
    total = Integer.parseInt(quantity) * price;
    } catch (Exception e) {
    ......
    }
    ......


- 방화벽 중요