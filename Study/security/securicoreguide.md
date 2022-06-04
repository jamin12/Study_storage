# 소프트 웨어 보안 취약 이유
- 보안 요구사항이 정의되지 않았거나
- 설계에 논리적인 오류가 있거나
- 보안에 취약한 코딩 규칙을 적용하였거나
- 소프트웨어 배치가 적절하지 않았거나
- 발견된 취약점에 대해 적절한 관리 또는 패치를 하지 않은 경우

---
##  __<span style="color:#ff9933">입력데이터 검증 및 표현</span>__
---
# SQL삽입
## __<span style="color:#9999ff">개요</span>__
- **데이터베이스와 연동된 웹 응용프로그램에서 입력된 데이터에 대한 유효성 검증을 하지 않을 경우, 공격자가 입력 폼 및 URL입력란에 SQL문을 삽입하여 DB로부터 정보를 열람하거나 조작할 수 있는 보안 약점을 말한다.**
- **개발자가 의도하지 않은 쿼리가 생성되어 정보유출에 악용될 수 있다**
## __<span style="color:#9999ff">보안대책</span>__
- **preparedStatment객체 등을 이용하여 DB에 컴파일 된 쿼리문(상수)을 전달하는 방법을 사용한다.**
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
- **파라미터(parameter)를 받는 preparedStatment객체를 상수 스트링으로 생성하고 파라미터 부분을 setString, setParameter등의 메소드로 설정하여, 외부의 입력이 쿼리문의 구조를 바꾸는 것을 방지해야 한다.**
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
- **검증되지 않은 외부 입력값을 통해 파일 및 서버 등 시스템 자원에 대한 접근 혹은 식별을 허용할 경우, 입력값 조작을 통해 시스템이 보호하는 자원에 임의로 접근할 수 있는 보안약점이다.**
## __<span style="color:#9999ff">보안대책</span>__
- **외부의 입력을 자원(파일,소켓의 포트 등)의 식별자로 사용하는 경우 적절한 검증을 거치도록 하거나, 사전에 정의된 적합한 리스트에서 선택되도록 한다. 특히 외부의 입력이 파일명인 경우에는 경로순회 공격의 위험이 있는 문자(" / \ ... 등)을 제거할 수 있는 필터를 이용한다.**
## __<span style="color:#9999ff">코드예제</span>__
- **../../../rootFile.txt 와같은 값이 들어오지 못하게 해야한다.**
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
- **웹 페이지에 악의적인 스크립트를 포함시켜 사용자 측에서 실행**되게 유도할 수 있다. 예를 들어 검증되지 않은 **외부 입력이 동적 웹페이지 생성에 사용될 경우, 전송된 동적 웹페이지를 열람하는 접속자의 권한으로 부적절한 스크립트가 수행되어 정보유출 등의 공격을 유발할 수 있다.**
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
- 서버 측에서 실행될 수 있는 스크립트 파일(asp,jsp 파일 등)이 업로드가능하고, 이 파일을 공격자가 **웹을 통해 직접 실행시킬 수 있는 경우, 시스템 내부명령어를 실행하거나 외부와 연결하여 시스템을 제어할 수 있는 보안 약점이다.**
## __<span style="color:#9999ff">보안대책</span>__
- **화이트리스트 방식으로 허용된 확장자만 업로드를 허용한다**. 업로드 되는 파일을 저장할 떄에는 파일명과 확장자를 외부 사용자가 추측할 수 없는 문자열로 변경하여 저장하며, 저장경로는 'web document root'밖에 위치시켜서 공격자의 웹을 통한 직접 접근을 차단한다. 또한 파일 실행 여부를 설정할 수 있는 경우 **실행 속성을 제거**한다.
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
- **응용프로그램이 외부 입력값에 대한 신뢰를 전제로 보호메커니즘을 사용하는 경우 공격자가 입력값을 조작할 수 있다면 보호 메커니즘을 우회할 수 있게 된다.**
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

---
## __<span style="color:#ff9933">보안기능</span>__
---
# 적절한 인증 없는 중요기능 허용
## __<span style="color:#9999ff">개요</span>__
- **적절한 인증과정 없이 중요정보(계좌이체 정보, 개인정보 등)를 열람(또는 변경)할 때 발생하는 보안약점이다.**
## __<span style="color:#9999ff">보안 대책</span>__
- 클라이언트의 보안검사를 우회하여 서버에 접근하지 못하도록 설계하고 중요한 정보가 있는 페이지는 재인증을 적용한다.
- 안전하다고 검증된 라이브러리나 프레임워크등을 사용하는 것이 중요하다
## __<span style="color:#9999ff">코드 예제<span>__
- 회원정보 수정 시 수정을 요청한 사용자와 로그인한 사용자의 일치 여부를 확인하지 않고 처리하고 있다.
- 안전하지 않은 코드
- ``` java
    @RequestMapping(value = "/modify.do", method = RequestMethod.POST)
    public ModelAndView memberModifyProcess(@ModelAttribute("MemberModel")
    MemberModel memberModel, BindingResult result, HttpServletRequest request,
    HttpSession session) {
    ModelAndView mav = new ModelAndView();
    //1. 로그인한 사용자를 불러온다.
    String userId = (String) session.getAttribute("userId")
    String passwd = request.getParameter("oldUserPw");
    ...
    //2. 실제 수정하는 사용자와 일치 여부를 확인하지 않고, 회원정보를 수정하여 안전하지 않다.
    if (service.modifyMember(memberModel)) {
    mav.setViewName("redirect:/board/list.do");
    session.setAttribute("userName", memberModel.getUserName());
    return mav;
    } else {
    mav.addObject("errCode", 2);
    mav.setViewName("/board/member_modify");
    return mav;
    }
    }
- 안전한 코드
- ``` java
    @RequestMapping(value = "/modify.do", method = RequestMethod.POST)
    public ModelAndView memberModifyProcess(@ModelAttribute("MemberModel")
    MemberModel memberModel, BindingResult result, HttpServletRequest request,
    HttpSession session) {
    ModelAndView mav = new ModelAndView();
    //1. 로그인한 사용자를 불러온다.
    String userId = (String) session.getAttribute("userId");
    String passwd = request.getParameter("oldUserPw");
    //2. 회원정보를 실제 수정하는 사용자와 로그인 사용자와 동일한지 확인한다.
    String requestUser = memberModel.getUserId();
    if (userId != null && requestUser != null && !userId.equals(requestUser)) {
    mav.addObject("errCode", 1);
    mav.addObject("member", memberModel);
    mav.setViewName("/board/member_modify");
    return mav;
    }
    ... 
    //3. 동일한 경우에만 회원정보를 수정해야 안전하다.
    if (service.modifyMember(memberModel)) {
    ...
# 부적절한 인가
## __<span style="color:#9999ff">개요</span>__
- 프로그램이 모든 가능한 실행결로에 대해서 접근제어를 검사하지 않거나 불완전하게 검사하는 경우, 공경자는 접근 가능한 실행경로를 통해 정보를 유출할 수 있다.
## __<span style="color:#9999ff">보안대책</span>__
- 응용프로그램이 제공하는 정보와 기능을 역할에 따라 배분함으로써 공격자에게 노출되는 공격노출면을 최소화하고 사용자의 권한에 따른 ACL을 관리한다. 프레임워크(JAAS Authentication Framewor나 OWASP ESAPI access Control)을 사용해 취약점을 피할 수 있다.
## __<span style="color:#9999ff">코드예제</span>__
- 사용자의 권한 확인을 위한 별도의 통제가 적용되지 않고 있다.
- ``` java
    private BoardDao boardDao;
    String action = request.getParameter("action");
    String contentId = request.getParameter("contentId");
    //요청을 하는 사용자의 delete 작원 권한 확인 없이 수행하고 있어 안전하지 않다.
    if (action != null && action.equals("delete")) {
        boardDao.delete(contentId);
    }
- 사용자가 삭제작업을 수행할 권한이 있는지 확인한 뒤 권한이 있는 경우에만 수행하도록 해야한다.
- ``` java
    private BoardDao boardDao;
    String action = request.getParameter("action");
    String contentId = request.getParameter("contentId");
    // 세션에 저장된 사용자 정보를 얻어온다.
    User user= (User)session.getAttribute("user");
    // 사용자정보에서 해당 사용자가 delete작업의 권한이 있는지 확인한 뒤 삭제 작업을 수행한다.
    if (action != null && action.equals("delete") &&
        checkAccessControlList(user,action)) {
        boardDao.delete(contenId);
        }
    }
# 취약한 암호화 알고리즘 사용
## __<span style="color:#9999ff">개요</span>__
- **SW 개발자들은 환경설정 파일에 저장된 패스워드를 보호하기 위하여 간단한 인코딩 함수를 이용하여 패스워드를 감추는 방법을 사용하기도 한다. 그렇지만 base64와 같은 지나치게 간단한 인코딩 함수로는 패스워드를 제대로 보호할 수 없다**
- **정보보호 측면에서 취약하거나 위험한 암호화 알고리즘을 사용해서는 안 된다.** 표준화되지 않은 암호화 알고리즘을 사용하는 것은 공격자가 알고리즘을 분석하여 무력화 시킬 수 있는 가능성을 높일 수도 있다.
## __<span style="color:#9999ff">보안대책</span>__
- 자신만의 암호화 알고리즘을 개발하는 것은 위험하며, 학계 및 업계에서 이미 검증된 표준화된 알고리즘을 사용한다. 기존에 취약하다고 알려진 DES, RC5등의 암호알고리즘을 대신하여 3DES,AES,SEED 드으이 안전한 암호 알고지름으로 대체하여 사용하낟. 또한, 업무관련 내용, 개인정보 등의 대한 암호 알고리즘 적용시, IT보안인증 사무국이 안정성을 확인한 검증필 암효모듈을 사용해야 한다.
- ![](https://t1.daumcdn.net/cfile/tistory/252FB6495819737532)
- ![](https://blog.kakaocdn.net/dn/RuHmU/btqFPrxGLVW/yFbz61okUXgS6XFkYH7ukk/img.png)
## __<span style="color:#9999ff">코드예제</span>__
- 안전하지 않은 코드
- ``` java
    import java.security.*;
    import javax.crypto.Cipher;
    import javax.crypto.NoSuchPaddingException;
    public class CryptoUtils {
    public byte[] encrypt(byte[] msg, Key k) {
    byte[] rslt = null;
    try {
    // 키 길이가 짧아 취약함 암호와 알고리즘인 DES를 사용하여 안전하지 않다.
    Cipher c = Cipher.getInstance("DES");
    c.init(Cipher.ENCRYPT_MODE, k);
    rslt = c.update(msg);
    }
- 안전한 코드
- ``` java
    import java.security.*;
    import javax.crypto.Cipher;
    import javax.crypto.NoSuchPaddingException;
    public class CryptoUtils {
    public byte[] encrypt(byte[] msg, Key k) {
    byte[] rslt = null;
    try {
    // 키 길이가 길어 강력한 알고리즘인 AES를 사용하여 안전하다.
    Cipher c = Cipher.getInstance("AES/CBC/PKCS5Padding");
    c.init(Cipher.ENCRYPT_MODE, k);
    rslt = c.update(msg);
    }
# 중요정보 평문저장
## __<span style="color:#9999ff">개요</span>__
- 많은 응용프로그램은 메모리나 디스크에서 중요한 데이터(개인정보, 인증정보, 금융정보)를 처리한다. 이러한 중요 데이터가 제대로 보호되지 않을 경우, 보안이나 데이터의 무결성을 잃을 수 있다. 특히 프로그램이 개인정보, 인증정보 등의 사용자 중요정보 및 시스템 중요정보를 처리하는 과정에서 이를 평문으로 저장할 경우 공격자에게 민감한 정보가 노출될 수 있다.

## __<span style="color:#9999ff">보안대책</span>__
- 개인정보(주민등록번호, 여권번호 등), 금융정보(카드번호, 계좌번호 등), 패스워드 등 중요정보를 저장할 때는 반드시 암호화하여 저장해야 하며, 중요정보를 읽거나 쓸 경우에 권한인증 등을 통해 적합한 사용자가 중요정보에 접근하도록 해야 한다.

## __<span style="color:#9999ff">코드예제</span>__
- 중요정보 평문저장
- 안전하지 않은 코드
- ``` java
    String id = request.getParameter("id");
    // 외부값에 의해 패스워드 정보를 얻고 있다.
    String pwd = request.getParameter("pwd");
    ......
    String sql = " insert into customer(id, pwd, name, ssn, zipcode, addr)"
    + " values (?, ?, ?, ?, ?, ?)";
    PreparedStatement stmt = con.prepareStatement(sql);
    stmt.setString(1, id);
    stmt.setString(2, pwd);
    ......
    // 입력받은 패스워드가 평문으로 DB에 저장되어 안전하지 않다.
    stmt.executeUpdate(); 
- 안전한 코드
  - __<span style="color:#14a492">해시를 이용한 암호화</span>__
  - ``` java
      String id = request.getParameter("id");
      // 외부값에 의해 패스워드 정보를 얻고 있다.
      String pwd = request.getParameter("pwd");
      // 패스워드를 솔트값을 포함하여 SHA-256 해쉬로 변경하여 안전하게 저장한다.
      MessageDigest md = MessageDigest.getInstance("SHA-256");
      md.reset();
      md.update(salt);
      byte[] hashInBytes = md.digest(pwd.getBytes());
      StringBuilder sb = new StringBuilder();
      for (byte b : hashInBytes) {
      sb.append(String.format("%02x", b));
      }
      pwd = sb.toString();
      ......
      String sql = " insert into customer(id, pwd, name, ssn, zipcode, addr)"
      + " values (?, ?, ?, ?, ?, ?)";
      PreparedStatement stmt = con.prepareStatement(sql);
      stmt.setString(1, id);
      stmt.setString(2, pwd);
      ......
      stmt.executeUpdate();

# 중요정보 평문전송
## __<span style="color:#9999ff">개요</span>__
- 사용자 또는 시스템의 중요정보가 포한된 데이터를 평문으로 송.수신 또는 저장할 때 인가되지 않은 사용자에게 민감한 정보가 노출될 수 있는 보안약점이다.
## __<span style="color:#9999ff">보안대책</span>__
- 개인정보, 금융정보, 패스워드 등 중요정보를 통신채널로 전송하거나 저장할 때는 반드시 암호화 과정을 거쳐야한다.필요한 경우 SSL 또는HTTPS 등과 같은 암호채널을 사용해야 하며, HTTPS와 같은 보안 채녈을 사용하여 브라우저쿠키에 중요 데이터를 저장하는 경우, 쿠키객체에 보안속성을 반드시 설정하여 중요정보의 노출을방지한다. 중요정보를 읽거나 쓸 경우에 권한인증 등으로 적합한 사용자가 중요정보에 접근하도록 해야 한다
  
## __<span style="color:#9999ff">코드 예제</span>__
- 안전하지 않은 코드
- ``` java
    try {
    Socket s = new Socket("taranis", 4444);
    PrintWriter o = new PrintWriter(s.getOutputStream(), true);
    //패스워드를 평문으로 전송하여 안전하지 않다.
    String password = getPassword();
    o.write(password);
    } catch (FileNotFoundException e) {
    ……
- 안전한 코드
- AES등의 안전한 암호알고리즘으로 암호화.
- ``` java
    // 패스워드를 암호화 하여 전송
    try {
    Socket s = new Socket("taranis", 4444);
    PrintStream o = new PrintStream(s.getOutputStream(), true);
    // 패스워드를 강력한 AES암호화 알고리즘으로 전송하여 사용한다.
    Cipher c = Cipher.getInstance("AES/CBC/PKCS5Padding");
    String password = getPassword();
    byte[] encPassword = c.update(password.getBytes());
    o.write(encPassword, 0, encPassword.length);
    } catch (FileNotFoundException e) {
    ……
# 하드코드된 비밀번호
## __<span style="color:#9999ff">개요</span>__
- 프로그램 코드 내부에 하드코드된 패스워드를 포함하고 , 이를 이용하여 내부 인증에 사용하거나 외부 컴포넌트와 통신을 하는 경우, 관지라 정보가 노출될 수 있어 위험하다. 또한 코드 내부에 하드코드 된 패스워드가 인증실패를 야기하는 경우 시스템 관리자가 그 실패의 원인을 파악하기 쉽지 않은 단점이 있다.
## __<span style="color:#9999ff">보안대책</span>__
- 패스워드는 암호화하여 별도의 파일에 저장하여 사용하고 SW설치 시 사용하는 디폴트 패스워드, 키 등을 사용하는 대신 "최초-로그인"모드를 두어 사용자가 직접 패스워드나 키를 입력하도록 설계한다.

## __<span style="color:#9999ff">코드예제</span>__
- 데이터베이스 연결을 위한 패스워드를 소스코드 내부에 상수 형태로 하드코딩 하는경우 접속 정보가 노출될 수 있어 위험하다.
- 안전하지 않은 코드
- ``` java
    public class MemberDAO {
    private static final String DRIVER = "oracle.jdbc.driver.OracleDriver";
    private static final String URL = "jdbc:oracle:thin:@192.168.0.3:1521:ORCL";
    private static final String USER = "SCOTT"; // DB ID;
    //DB 패스워드가 소스코드에 평문으로 저장되어 있다.
    private static final String PASS = "SCOTT"; // DB PW;
    ……
    public Connection getConn() {
    Connection con = null;
    try {
    Class.forName(DRIVER);
    con = DriverManager.getConnection(URL, USER, PASS);
    ……
- 안전한 코드
- ``` java
    public class MemberDAO {
    private static final String DRIVER = "oracle.jdbc.driver.OracleDriver";
    private static final String URL = "jdbc:oracle:thin:@192.168.0.3:1521:ORCL";
    private static final String USER = "SCOTT"; // DB ID
    ……
    public Connection getConn() {
    Connection con = null;
    try {
    Class.forName(DRIVER);
    //암호화된 패스워드를 프로퍼티에서 읽어들어 복화해서 사용해야한다.
    String PASS = props.getProperty("EncryptedPswd");
    byte[] decryptedPswd = cipher.doFinal(PASS.getBytes());
    PASS = new String(decryptedPswd);
    con = DriverManager.getConnection(URL, USER, PASS);
    ……
# 충분하지 않은 키 길이 사용
## __<span style="color:#9999ff">개요</span>__
- 길이가 짧은 키를 사용하는 것은 암호화 알고리즘을 취약하게 만들 수 있다. 키는 암호화 및 복호화에 사용되는데, 검증된 암호화 알고리즘을 사용하더라도 키 길이가 충분히 길지 않으면 짧은 시간 안에 키를 찾아낼 수 있고 이를 이용해 공격자가 암호화된 데이터나 패스워드를 복호화 할 수 있게 된다.
## __<span style="color:#9999ff">보안대책</span>__
- RSA 알고리즘은 적어도 2,048 비트 이상의 길이를 가진 키와 함께 사용해야 하고, 대칭암호화 알고리즘(Symmetric Encryption Algorithm)의 경우에는 적어도 128비트 이상의 키를 사용한다.
## __<span style="color:#9999ff">코드예제</span>__
- 보안성이 강한 RSA알고리즘을 사용에도 불구하고 키 사이즈를 잘게 설정해 프로그램의 보안 약점을 야기한 경우이다.
- 안전하지 않은 코드
- ``` java
    public static final String ALGORITHM = "RSA";
    public static final String PRIVATE_KEY_FILE = "C:/keys/private.key";
    public static final String PUBLIC_KEY_FILE = "C:/keys/public.key";
    public static void generateKey() {
    try {
    final KeyPairGenerator keyGen = KeyPairGenerator.getInstance(ALGORITHM);
    //RSA 키 길이를 1024 비트로 짧게 설정하는 경우 안전하지 않다.
    keyGen.initialize(1024);
    final KeyPair key = keyGen.generateKeyPair();
    File privateKeyFile = new File(PRIVATE_KEY_FILE);
    File publicKeyFile = new File(PUBLIC_KEY_FILE);
- 안전한 코드
- ``` java
    public static final String ALGORITHM = "RSA";
    public static final String PRIVATE_KEY_FILE = "C:/keys/private.key";
    public static final String PUBLIC_KEY_FILE = "C:/keys/public.key";
    public static void generateKey() {
    try {
    final KeyPairGenerator keyGen = KeyPairGenerator.getInstance(ALGORITHM);
    keyGen.initialize(2048);
    final KeyPair key = keyGen.generateKeyPair();
    File privateKeyFile = new File(PRIVATE_KEY_FILE);
    File publicKeyFile = new File(PUBLIC_KEY_FILE);
# 적절하지 않은 난수값 사용
## __<span style="color:#9999ff">개요</span>__
- 예측 가능한 난수를 사용하는 것은 시스템에 보안약점을 유발한다. 예측 불가능한 숫자가 필요한 상황에서 예측 가능한 난수를 사용한다면, 공격자는 SW에서 생성되는 다음 숫자를 예상하여 시스템을 공격하는 것이 가능하다.
## __<span style="color:#9999ff">보안대책</span>__
- 컴퓨터의 난수발생기는 난수 값을 결정하는 시드(Seed)값이 고정될 경우, 매번 동일한 난수값이 발생 한다. 이를 최대한 피하기 위해 Java에서는 Random()과 Math.random() 사용 시 java.util.Random 클래스에서 기본값으로 현재시간을 기반으로 조합하여 매번 변경 되는 시드(Seed)값을 사용하며, C 에서는 rand()함수 사용 시 매번 변경되는 기본 시드(Seed)값이 없으므로, srand()를 통해 매번 변경 되는 현재시간 기반 등으로 시드(Seed)값을 설정 하여야 한다. 그러나 세션 ID, 암호화키 등 보안결정을 위한 값을 생성하고 보안결정을 수행하는 경우에는, Java 에서 Random()과 Math.random()을 사용 하지 말아야 하며, 예측이 거의 불가능하게 암호학적으로 보호된 java.security.SecureRandom 클래스를 사용하는 것이 안전하다
## __<span style="color:#9999ff">코드예제</span>__
- java.util.Random 클래스의 random() 메소드 사용시, 고정된 seed를 설정하면 동일한 난수 값이 생성되어 안전하지 않다. 매번 변경되는 seed를 설정하더라도 보안결정을 위한 난수 이용시에는 안전하지 않다.
- 안전하지 않은 코드
- ``` java
    import java.util.Random;
    ...
    public Static int getRandomValue(int maxValue) {
    //고정된 시드값을 사용하여 동일한 난수값이 생성되어 안전하지 않다.
    Random random = new Random(100);
    return random.nextInt(maxValue);
    }
    public Static String getAuthKey() {
    //매번 변경되는 시드값을 사용하여 다른 난수값이 생성되나 보안결정을 위한 난수로는 안전하지 않다.
    Random random = new Random();
    String authKey = Integer.toString(random.nextInt());
- 안전한 코드
- ``` java
    import java.util.Random;
    import java.security.SecureRandom;
    ...
    public Static int getRandomValue(int maxValue) {
    //setSeed를 통해 매번 변경되는 시드값을 설정 하거나, 기본값인 현재 시간 기반으로 매번 변경되는
    시드값을 사용하도록 한다.
    Random random = new Random();
    return random.nextInt(maxValue);
    }
    public Static String getAuthKey() {
    //보안결정을 위한 난수로는 예측이 거의 불가능하게 암호학적으로 보호된 SecureRandom을 사용한다.
    try{
    SecureRandom secureRandom = SecureRandom.getInstance("SHA1PRNG");
    MessageDigest digest = MessageDigest.getInstance("SHA-256");
    secureRandom.setSeed(secureRandom.generateSeed(128));
    String authKey = new String(digest.digest((secureRandom.nextLong() +
    "").getBytes()));
    ...
    } catch (NoSuchAlgorithmException e) {
# 하드코드된 암호화 키
## __<span style="color:#9999ff">개요</span>__
- 프로그램 코드 내부에 하드코드된 패스워드 또는 암호화키를 포함하여 내부 인증에 사용하거나 암호화를 수행하면 중요정보(관리자 정보, 암호화된 정보 등)가 유출될 수 있는 보안약점이다.
## __<span style="color:#9999ff">보안대책</span>__
- 패스워드는 암호화 하여 별도의 파일에 저장하여 사용한다. 또한 중요정보를 암호화하면, 상수가 아닌 암호화 키를 사용하도록 하며 소스코드 내부에 상수형태의 암호화 키를 저장해서 사용하지 않도록 한다. 
## __<span style="color:#9999ff">코드예제</span>__
- 안전하지 않은 코드
- ``` c#
    // 암호화 키를 소스코드 내부에 사용하는 것은 안전하지 않다.
    byte[] key = new byte[] { 0x43, 0x87, 0x23, 0x72 };
    byte[] iv = new byte[] { 0x43, 0x87, 0x23, 0x72 };
    FileStream fStream = File.Open(fileName, FileMode.OpenOrCreate);
    CryptoStream cStream = new CryptoStream(fStream,
    new TripleDESCryptoServiceProvider().CreateEncryptor(key, iv),
    CryptoStreamMode.Write);
- 암호화 과정에 사용하는 암호화 키는 외부 공간에 안전한 방식으로 암호화하여 보관해야 하며, 암호화된 암호화키는 복호화하여사용한다.
- 안전한 코드
- ``` c#
    // 암호화 키는 외부 파일에서 암호화 된 형태로 저장하고, 사용시 복호화 한다.
    byte[] key = GetKey(./password.ini);
    byte[] iv = GetIV(./password.ini);
    FileStream fStream = File.Open(fileName, FileMode.OpenOrCreate);
    CryptoStream cStream = new CryptoStream(fStream,
    new TripleDESCryptoServiceProvider().CreateEncryptor(Decrypt(key),
    Decrypt(iv)),
    CryptoStreamMode.Write);

# 취약한 비밀번호 허용
## __<span style="color:#9999ff">개요</span>__
- 사용자에게 강한 비밀번호 조합규칙을 요구하지 않으면, 사용자 계정이 취약하게 된다. 안전한 비밀번호를 생성하기 위해서는 「패스워드 선택 및 이용 안내서」의 안전한 패스워드 설정규칙을 적용해야 한다.
## __<span style="color:#9999ff">보안대책</span>__
- 비밀번호 생성 시 강한 조건 검증을 수행한다. 비밀번호(패스워드)는 숫자와 영문자, 특수문자 등을 혼합하여 정해진 자릿수를 사용하여 생성되도록 하고, 주기적으로 변경하도록 해야 한다.
## __<span style="color:#9999ff">코드예제</span>__
- 안전하지 않은 코드
- ``` java
    String id = request.getParameter("id");
    String pass = request.getParameter("pass");
    UserVo userVO = new UserVo(id, pass);
    ……
    // 비밀번호의 자릿수, 특수문자 포함 여부 등 복잡도를 체크하지 않고 등록
    String result = registerDAO.register(userVO);
- 안전한 코드
- ``` java
    String id = request.getParameter("id");
    String pass = request.getParameter("pass");
    // 비밀번호에 자릿수, 특수문자 포함 여부 등의 복잡도를 체크하고 등록하게 한다.
    Pattern pattern = Pattern.compile("((?=.*[a-zA-Z])(?=.*[0-9@#$%]). {9, })");
    Matcher matcher = pattern.matcher(pass);

# 솔트 없이 일방향 해쉬함수 사용
## __<span style="color:#9999ff">개요</span>__
- **패스워드를 저장 시 일방향 해쉬함수의 성질을 이용하여 패스워드의 해쉬값을 저장한다. 만약 패스워드를 솔트(salt)없이 해쉬하여 저장한다면 공격자는 레인보우 테이블과 같이 해쉬값을 미리 계산하여 패스워드를 찾을 수 있게 된다.**
## __<span style="color:#9999ff">보안대책</span>__
- **패스워드를 저장 시 패스워드와 솔트를 해쉬함수의 입력으로 하여 얻은 해쉬값을 저장한다.**
## __<span style="color:#9999ff">코드 예제</span>__
- 안전하지 않은 코드
- ``` java
    public String getPasswordHash(String password) throws Exception {
    MessageDigest md = MessageDigest.getInstance("SHA-256");
    // 해쉬에 솔트를 적용하지 않아 안전하지 않다.
    md.update(password.getBytes());
    byte byteData[] = md.digest();
    StringBuffer hexString = new StringBuffer();
    for (int i=0; i<byteData.length i++) {
    String hex=Integer.toHexString(0xff & byteData[i]);
        if (hex.length() == 1) {
            hexString.append('0');
        }
        hexString.append(hex);
    }
    return hexString.toString();
    }
- 안전한 코드
- ``` java
    public String getPasswordHash(String password, byte[] salt) throws Exception {
    MessageDigest md = MessageDigest.getInstance("SHA-256");
    md.update(password.getBytes());
    // 해쉬 사용 시에는 원문을 찾을 수 없도록 솔트를 사용하여야 한다.
    md.update(salt);
    byte byteData[] = md.digest();
    StringBuffer hexString = new StringBuffer();
    for (int i=0; i<byteData.length i++) {
        String hex=Integer.toHexString(0xff & byteData[i]);
        if (hex.length() == 1) {
            hexString.append('0');
        }
        hexString.append(hex);
    }
    return hexString.toString()
    }
# 주석문 안에 포함된 시스템 주요정보
## __<span style="color:#9999ff">개요</span>__
- 패스워드를 주석문에 넣어두면 시스템 보안이 훼손될 수 있다. 소프트웨어 개발자가 편의를 위해서 주석문에 패스워드를 적어둔 경우, 소프트웨어가 완성된 후에는 그것을 제거하는 것이 매우 어렵게 된다. 또한, 공격자가 소스코드에 접근할 수 있다면, 아주 쉽게 시스템에 침입할 수 있다.
## __<span style="color:#9999ff">보안대책</span>__
- 주석에는 ID 패스워드 등 보안과 관련된 내용을 기입하지 않는다.
## __<span style="color:#9999ff">코드 예제</span>__
- 안전하지 않은 코드
- ``` java
    //주석문을 통해 DB연결 ID, 패스워드의 중요한 정보를 노출시켜 안전하지 않다.
    // DB연결 root / a1q2w3r3f2!@
    con = DriverManager.getConnection(URL, USER, PASS);
- 안전한 코드
- ``` java
    // ID, 패스워드등의 중요 정보는 주석에 포함해서는 안된다.
    con = DriverManager.getConnection(URL, USER, PASS)
---
## __<span style="color:#ff9933">에러처리</span>__
---

# 오류 메시지 정보 노출
## __<span style="color:#9999ff">개요</span>__
- 응용프로그램이 실행환경, 사용자 등 관련 데이터 또는 시스템의 내부데이터 등 민감한 정보를 포함하는 오류 메시지를 생성하여 외부에 제공하는 경우, 공격자의 악성 행위를 도울 수 있다. 예외 발생 시 예외 이름이나 스택 트레이스를 출력하는 경우, 프로그램 내부구조를 쉽게 파악할 수 있기 때문이다.
## __<span style="color:#9999ff">보안대책</span>__
- 오류 메시지는 정해진 사용자에게 유용한 최소한의 정보만 포함하도록 한다. 소스코드에서 예외 상황은 내부적으로 처리하고 사용자에게 시스템 내부 정보 등 민감한 정보를 포함하는 오류를 출력하지 않도록 미리 정의된 메시지를 제공하도록 설정한다.
## __<span style="color:#9999ff">코드 예제</span>__
- 안전하지 않은 코드
- ``` java
    try {
    rd = new BufferedReader(new FileReader(new File(filename)));
    } catch(IOException e) {
    // 에러 메시지로 스택 정보가 노출됨
    e.printStackTrace();
    }
    catch(IOException e) {
    // 오류발생시 화면에 출력된 시스템 정보로 다른 공격의 빌미를 제공한다.
    System.err.print(e.getMessage());
    }
- 예외 이름이나 오류추적 정보를 출력하지 않도록 한다.
- ``` java
    try {
    rd = new BufferedReader(new FileReader(new File(filename)));
    } catch(IOException e) {
    // 에러 코드와 정보를 별도로 정의하고 최소 정보만 로깅
    logger.error("ERROR-01: 파일 열기 에러");
    }
- 방화벽 중요