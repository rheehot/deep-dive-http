# HTTP 완벽가이드 정리
## 3장 HTTP 메시지

* 메시지가 어떻게 흘러가는가
* HTTP 메시지의 세 부분(시작줄, 헤더, 개체 본문)
* 요청과 응답 메시지의 차이
* 요청 메시지가 지원하는 여러 기능(메서드)들
* 응답 메시지가 반환하는 여러 상태 코드들
* 여러 HTTP 헤더들은 무슨 일을 하는가

### 3.1 메시지의 흐름

HTTP 메시지는 HTTP 애플리케이션 간에 주고받은 데이터의 블록들이다.
이 데이터의 블록들은 메시지의 내용과 의미를 설명하는 **텍스트 메타 정보**로 시작하고
그 다음에 선택적으로 데이터가 온다.

메시지는 ```클라이언트, 서버, 프락시``` 사이를 흐른다.
```인바운드, 아웃바운드, 업스트림, 다운스트림```은 메시지의 방향을 의미한다.

#### 3.1.1 메시지는 원 서버 방향을 인바운드로 하여 송신된다

HTTP는 인바운드와 아웃바운드 라는 용어를 트랜잭션 방향을 표현하기 위해 사용한다.

* 인바운드 : 메시지가 원 서버로 향하는 것
* 아웃바운드 : 모든 처리가 끝난 뒤, 메시지가 사용자 에이전트로 돌아오는 것

#### 3.1.2 다운 스트림으로 흐르는 메시지

요청 메시지, 응답 메시지에 관계없이 모든 메시지는 **다운스트림** 으로 흐른다.

### 3.2 메시지의 각 부분

HTTP 메시지는 단순한, 데이터의 구조화된 블록이다.

메시지는 ```시작줄, 헤더 블록, 본문``` 이렇게 세 부분으로 이루어진다.

* 시작줄 : 어떤 메시지인지 서술
* 헤더 : 속성
* 본문 : 데이터를 담고있다 (아예 없을 수도 있다)

시작줄과 헤더는 줄 단위로 분리된 아스키 문자열이다.
각 줄은 캐리지 리턴과 개행문자로 구성된 두 글자의 줄바꿈 문자열로 끝난다.

본문은 단순히 선택적인 데이터 덩어리이다.
시작줄, 헤더와 달리 본문은 텍스트나 이진 데이터를 포함할 수도 있고 비어있을 수도 있다.

#### 3.2.1 메시지 문법

모든 HTTP 메시지는 요청 메시지나 응답 메시지로 분류된다.

요청 메시지의 형식은 다음과 같다.
 ```
<메서드><요청URL><버전>
<헤더>

<엔터티 본문>
 ```

응답 메시지의 형식은 다음과 같다(시작줄만 문법이 다르다)
 ```
<버전><상태코드><사유 구절>
<헤더>

<엔터티 본문>
 ``` 

* 메서드
  클라이언트 측에서 서버가 리소스에 대해 수행해주길 바라는 동작이다.
  ```GET, HEAD, POST```와 같이 한 단어로 되어있다.

* 요청 URL
  요청 대상 리소스를 지칭하는 완전한 URL 혹은 URL의 경로 구성요소다.
  완전한 URL이 아니더라도, 리소스를 가리키는 절대 경로이기만 하면 서버는 URL의
  생략된 호스트/포트가 자신을 가리키는 것으로 간주한다.

* 버전
  이 메시지에서 사용 중인 HTTP의 버전이다.
  ```
  HTTP / <메이저> , <마이너>
  ```
  메이저와 마이너는 모두 정수다.

* 상태코드
  요청 중에 무엇이 일어났는지 설명하는 세 자리 숫자다.
  각 코드의 첫 번째 자릿수는 상태의 일반적인 분류 ```성공, 에러```등을 나타낸다.

* 사유 구절(reason-phrase)
  숫자로 된 상태 코드의 의미를 사람이 해석하기 쉽게 설명해주는 짧은 문구이다.

* 헤더들
  ```이름, 콜론, 선택적인 공백, 값, CRLF```가 순서대로 나타나는 0개 이상의 헤더들.
  헤더의 목록은 빈 줄(CRLF)로 끝나 헤더 목록의 끝과 엔터티 본문의 시작을 표시한다.

* 엔터티 본문
  엔터티 본문은 임의의 데이터 블록을 포함한다. 헤더나 본문이 없더라도 HTTP 헤더의 집합은
  항상 빈 줄로 끝나야 한다.

#### 3.2.2 시작줄

모든 HTTP 메시지는 시작줄로 시작한다. 요청 메시지의 시작줄은 **무엇을 해야하는지** 말해준다.
응답 메시지의 시작줄은 **무슨 일이 일어났는지** 말해준다.

* 요청줄
  서버에서 어떤 동작이 일어나야 하는지 설명해주는 **메서드**와 그 동작에 대한 대상을 지칭하는 **요청 URL**이 들어있다.
  또한, 클라이언트의 HTTP 버전도 포함한다. 필드는 모두 공백으로 구분된다.

  ```
  GET /test/hi0there.txt HTTP/1.1
  ```

* 응답줄
  **상태 정보**와 결과 **데이터**를 클라이언트에게 돌려준다. 응답 메시지에서 쓰인 **HTTP의 버전, 상태코드, 사유구절**이 들어있다. 필드는 모두 공백으로 구분된다.
  ```
  HTTP/1.0 200 OK
  ```

* 메서드
  서버에게 무엇을 해야하는지 말해준다.
  ```
  GET
  HEAD
  POST
  PUT
  TRACE
  OPTIONS
  DELETE
  ```
  모든 서버가 위의 메서드를 모두 구현한 것은 아니다. 또한 다른 서버는 특정 메서드를 추가로 구현했을 수도 있다.
  추가 메서드는 HTTP 명세를 확장하여 **확장 메서드**라고 불린다.

* 상태 코드
  클라이언트에게 무엇이 일어났는지 말해준다. 상태코드들은 세 자리 숫자로 된 코드값을 기준으로 묶인다.
  
  전체 범위|정의된 범위|정보
  :---|:---|:---
  100-199|100-101|정보
  200-299|200-206|성공
  300-399|300-305|리다이렉션
  400-499|400-415|클라이언트 에러
  500-599|500-505|서버 에러

  아래는 만날 수 있는 가장 흔한 상태 코드 중 몇 가지이다.

  상태 코드|사유 구절|의미
  :---|:---|:---
  200|OK|성공! 요청한 모든 데이터는 응답 본문에 있다
  401|Unauthorized|사용자 이름과 비밀번호를 입력해야 한다
  404|Not Found|서버는 요청한 URL에 해당하는 리소스를 찾지 못했다

* 사유구절
  상태 코드에 대한 글로 된 설명을 제공한다.

* 버전 번호
  자신이 따르는 프로토콜의 버전을 상대방에게 전달한다.
  응답 프로토콜 버전이 HTTP/1.1 이라는 것은 응답을 보낸 애플리케이션이 HTTP/1.1까지 이해할 수 있음을 의미한다.

#### 3.2.3 헤더

시작줄 다음에는 0개, 1개 혹은 여러 개의 HTTP 헤더가 온다.

* 헤더 분류
  * 일반 헤더 : 요청과 응답 양쪽에 모두 나타날 수 있음
  * 요청 헤더 : 요청에 대한 부가 정보를 제공
  * 응답 헤더 : 응답에 대한 부가 정보를 제공
  * Entity 헤더 : 본문 크기와 콘텐츠, 혹은 리소스 그 자체를 서술
  * 확장 헤더 : 명세에 정의되지 않은 새로운 헤더

각 헤더는 간단한 문법을 가진다. 이름, 쉼표, 공백(없어도 됨), 필드 값, CRLF가 순서대로 온다.

헤더의 예|설명
:---|:---
Date: Mon, 16 Nov 2020 10:10:10 GMT|서버가 응답을 만들어 낸 시각
Content-length: 15040|15040 바이트의 데이터를 포함한 엔터티 본문
Content-type: image/gif|엔터티 본문은 GIF 이미지이다
Accept: image/gif, image/jpeg, text/html|클라이언트는 GIF, JPEG, HTML을 받아들일 수 있다.

* 헤더를 여러 줄로 나누기
  ```
  HTTP/1.0 200 OK
  Content-Type: image/gif
  Content-length: 8572
  Server: Test Server
    Version 1.0
  ```
  Server 헤더가 여러 줄로 값이 쪼개져 있다. 완전한 값은 ```Test Server Version 1.0```이다.
  여러 줄로 쪼개질 헤더의 앞에는 최소 하나의 스페이스 혹은 탭 문자가 와야 한다.

#### 3.2.4 엔터티 본문

HTTP 메시지는 이미지, 비디오, HTML 문서, 소프트웨어 애플리케이션, 신용카드 트랜젝션, 전자우편 등
여러 종류의 디지털 데이터를 실어 나를 수 있다.

<hr>

### 3.3 메서드

#### 3.3.1 안전한 메서드(Safe Method)

GET과 HEAD 메서드는 안전하다고 할 수 있다. 이는 GET이나 HEAD 메서드를 사용하는 HTTP 요청의 결과로
서버에 어떤 작용도 없음을 의미한다.

안전한 메서드가 서버에 작용을 유발하지 않는다는 보장은 없다(사실 그건 웹 개발자에게 달렸다)
안전한 메서드의 목적은, 서버에 어떤 영향을 줄 수 있는 **안전하지 않은 메서드가 사용될 때
사용자들에게 그 사실을 알려주는** HTTP 애플리케이션을 만들 수 있도록 하는 것이다.

#### 3.3.2 GET

가장 흔히 쓰이며, 주로 서버에게 리소스를 달라고 요청하기 위해 쓰인다.

#### 3.3.3 HEAD

GET처럼 행동하지만, 서버는 응답으로 헤더만을 돌려준다. 클라이언트가 리소스를 가져올 필요 없이
헤더만을 조사할 수 있도록 해준다.

* 리소스를 가져오지 않고도 그에 대한 무엇인가(타입이라거나)를 알아낼 수 있다
* 응답의 상태 코드를 통해, 개체가 존재하는지 확인할 수 있다.
* 헤더를 확인하여 리소스가 변경되었는지 검사할 수 있다.

#### 3.3.4 PUT

PUT 메서드는 서버에 문서를 쓴다. PUT 메서드의 의미는, 서버가 요청의 본문을 가지고
요청 URL의 이름대로 새 문서를 만들거나, 이미 URL이 존재한다면 본문을 사용해서 교체하는 것이다.

#### 3.3.5 POST

POST 메서드는 서버에 **입력 데이터를 전송**하기 위해 설계되었다. 실제로 HTML 폼을 지원하기 위해
흔히 사용된다. 폼에 채워진 데이터는 서버로 전송되며, 서버는 이를 모아 필요로 하는곳(데이터를 처리할 서버 게이트웨이 프로그램)에 보낸다.

#### 3.3.6 TRACE

TRACE 메서드는 주로 진단을 위해 사용된다. 예를 들면 요청이 의도한 요청/응답 연쇄를 거쳐가는지 검사할 수 있다.
또한 프락시나 다른 애플리케이션들이 요청에 어떤 영향을 미치는지 확인해보고자 할 때도 좋은 도구다.

TRACE 요청은 어떠한 엔터티 본문도 보낼 수 없다. TRACE 응답의 엔터티 본문에는 서버가 받은 요청이 그대로 들어있다.

#### 3.3.7 OPTIONS

웹 서버에게 여러 가지 종류의 지원 범위에 대해 물어본다. 서버에게 특정 리소스에 대해 어떤 메서드가
지원되는지 물어볼 수 있다

#### 3.3.8 DELETE

서베에게 요청 URL로 지정한 리소스를 삭제할 것을 요청한다. 그러나 삭제가 수행되는 것을 보장하지 못한다.
HTTP 명세는 서버가 클라이언트에게 알리지 않고 요청을 무시하는 것을 허용하기 때문이다.

#### 3.3.9 확장 메서드

HTTP는 필요에 따라 확장해도 문제가 없도록 설계되어 있으므로, 새로 기능을 추가해도
과거에 구현된 소프트웨어들의 오동작을 유발하지 않는다. 확장 메서드(그리고 대부분의 HTTP)를 다룰 때는
**"엄격하게 보내고 관대하게 받아들여라"** 라는 오랜 규칙에 따라는 것이 가장 좋다.

<hr>

### 3.4 상태 코드

#### 3.4.1 100-199 : 정보성 상태 코드

정보성 상태 코드는 HTTP/1.1 에서 도입되었다.

상태 코드|사유 구절|의미
:---|:---|:---
100|Continue|요청의 시작 부분을 일부가 받아들여졌으며, 클라이언트는 나머지를 계속 이어서 보내야 함을 의미
101|Switching Protocols|클라이언트가 Upgrade 헤더에 나열한 것 중 하나로 서버가 프로토콜을 바꾸었음을 의미

100 Continue 상태 코드는 클라이언트가 서버에 엔터티 본문을 전송하기 전에 그 엔터티 본문을
서버가 받아들일 것인지 확인하려고 할 때, 그 확인 작업을 최적화하기 위한 의도로 도입되었다.

*클라이언트와 100 Continue*

클라이언트는 엔터티를 서버에 보낼 떄, 값을 100-continu로 하는 Expect 요청 헤더를 보낼 필요가 있다.
클라이언트는 100-continu를 서버가 다루거나 사용할 수 없는 큰 엔터티를 서버에게 보내지 않으려는 목적으로만 사용해야한다.

또한, 마냥 100-continue 응답을 기다리기만 해서는 안되고, 약간의 타임아웃 후 클라이언트는 그냥 서버로 엔터티를 보내야한다.

*서버와 100 Continue*

서버가 100-continue값이 담긴 Expect 헤더의 요청을 받았다면, 100 Continue 응답 혹은 에러코드로 답해야한다.
서버는 절대로 100-continue 응답을 받을 것을 의도하지 않은 클라이언트에게 100 Continue 상태 코드를 보내서는 안된다.

*프락시와 100 Continue*

100-continue 응답을 의도한 요청을 받은 프락시는 몇 가지 해야 할 일이 있다.
다음 홉(next-hop) 서버가 HTTP/1.1을 따르거나 혹은 어떤 버전을 따르는지 모른다면,
Expect 헤더를 포함시켜서 요청을 다음으로 전달해야 한다. 만약 다음 홉의 서버가 1.1보다
이전 버전의 HTTP를 따른다는 것을 알고 있다면, 프락시는 417 Expectation Failed 에러로 응답해야 한다.

#### 3.4.2 200-299 : 성공 상태 코드

상태 코드|사유 구절|의미
:---|:---|:---
200|OK|요청은 정상이고, 엔터티 본문은 요청된 리소스를 포함하고 있다.
201|Created|서버 개체를 생성하라는 요청(예: PUT)을 위한 것. 응답은, 생성된 리소스에 대한 최대한 구체적인 참조가 담긴 Location 헤더와 함께, 그 리소스를 참조할 수 있는 여러 URL을 엔터티 본문에 포함해야 한다.
202|Accepted|요청은 받아들여졌으나 서버는 아직 그에 대한 어떤 동작도 수행하지 않았다. 서버는 엔터티 본문에 요청에 대한 상태와 가급적이면 요청의 처리가 언제 완료 될 것인지에 대한 추정도 포함해야 한다.
203|Non-Authoritative Information|엔터티 헤더에 들어있는 정보가 원래 서버가 아닌 리소스의 사본에서 왔다. 이 응답 코드는 필수적으로 사용되어야 하는 것은 아니다. 엔티티 헤더가 원래 서버에서 온 것이었다면 응답이 200 상태였을 애플리케이션을 위한 선택사항이다.
204|No Content|응답 메시지는 헤더와 상태줄을 포함하지만 엔터티 본문은 포함하지 않는다. 주로 웹 브라우저를 새 문서로 이동시키지 않고 갱신하고자 할 때(폼을 리프레시) 사용한다.
205|Reset Content|브라우저에게 현재 페이지에 있는 HTML 폼에 채워진 모든 값을 비우라고 말한다.
206|Partial Content|부분 혹은 범위 요청 성공. 206 응답은 Content-Rage와 Date 헤더를 반드시 포함해야 하며, Etag와 Content-Location중 하나의 헤더도 반드시 포함해야한다.

#### 3.4.3 300-399 : 리다이렉션 상태 코드

클라이언트가 관심있어 하는 리소스에 대해 다른 위치를 사용하라고 말해주거나 그 리소스의 내용 대신 다른 대안
응답을 제공한다. 클라이언트에게 리소스가 옮겨졌으며 어디서 찾을 수 있는지 **리다이렉션 상태 코드와 Location 헤더**를 보낼 수 있다.

리다이렉션 상태 코드 중 몇몇은 리소스에 대한 애플리케이션의 로컬 복사본이 원래 서버와 비교했을 때
유효한지 확인하기 위해 사용된다.

상태 코드|사유 구절|의미
:---|:---|:---
300|Multiple Choices|클라이언트가 동시에 여러 리소스를 가리키는 URL을 요청한 경우, 그 리소스의 목록과 함께 반환한다.
301|Moved Permanently|요청한 URL이 옮겨졌을 때 사용한다. 응답은 Location 헤더에 현재 리소스가 존재하고 있는 URL을 포함해야 한다.
302|Found|301 과 같다. 그러나 클라이언트는 Location 헤더로 주어진 URL을 리소스를 임시로 가리키기 위한 목적으로 사용해야 한다.
303|See Other|클라이언트에게 리소스를 다른 URL에서 가져와야 한다고 말해주고자 할 때 쓰인다. 주 목적은 POST 요청에 대한 응답으로 클라이언트에게 리소스의 위치를 알려주는 것이다.
304|Not Modified|요청한 리소스가 최근에 수정된 일이 없다면, 이 코드는 리소스가 수정되지 않았음을 의미한게 된다. 이 상태 코드를 동반한 응답은 엔터티 본문을 가져가서는 안된다.
305|Use Proxy|리소스가 반드시 프락시를 통해서 접근되어야 함을 나타내기 위해 사용한다. 프락시의 위치는 Location 헤더를 통해 주어진다. 해당 요청에 대해서만 이 프락시를 사용해야 한다고 해석한다.
306|(사용되지 않음)|현재 사용되지 않음
307|Temporary Redirect|301 상태 코드와 비슷하다. 그러나 클라이언트는 Location 헤더로 주어진 URL을 리소스를 임시로 가리키기 위한 목적으로 사용해야 한다.

302, 303, 307 상태 코드 사이에 중복되는 부분이 있다. 이는 주로 **HTTP/1.0과 HTTP/1.1** 애플리케이션이
상태 코드를 다루는 방식의 차이점에 기인한다.

* 302 -> HTTP/1.0 클라이언트가 POST 요청을 보내고 302 리다이렉트 상태 코드가 담긴 응답을 받으면,
  Location 헤더에 들어있는 리다이렉트 URL을 GET 요청으로 따라간다. (POST->GET)
* 303 -> HTTP/1.1 에서는 302대 신 303을 사용
* 307 -> HTTP/1.1 에서는 일시적인 리다이렉트를 위해 307 상태 코드를 사용하라고 한다

#### 3.4.4 400-499 : 클라이언트 에러 상태 코드

상태 코드|사유 구절|의미
:---|:---|:---
400|Bad Request|클라이언트가 잘못된 요청을 보냈다.
401|Unauthorized|리소스를 얻기 전 클라이언트에게 인증하라고 요구
402|Payment Required|현재는 쓰이지 않지만, 미래를 위해 준비해둠
403|Forbidden|요청이 서버에 의해 거부되었다. 보통 서버가 거절의 의미를 숨기려고 사용
404|Not Found|서버가 요청한 URL을 찾을 수 없음을 알려주기 위해 사용
405|Method Not Allowed|요청 URL에 대해, 지원하지 않는 메서드로 요청받았을 때 사용
406|Not Acceptable|주어진 URL에 대한 리소스 중 클라이언트가 받아들일 수 있는 것이 없는 경우 사용
407|Proxy Authentication Requried|401과 같으나, 리소스에 대해 인증을 요구하는 프락시 서버를 위해 사용
408|Request Timeout|요청 완수하기에 시간이 너무 많이 걸리는 경우, 연결을 끊을 수 있다.
409|Conflict|서버는 요청이 충돌을 일으킬 염려가 있다고 생각될 때 이 요청을 보낸다.
410|Gone|404와 비슷하나 서버가 한때 그 리소스를 갖고 있었다는 차이점이 있다.
411|Length Required|서버가 요청 메시지에 Content-Length 헤더가 있을 것을 요구할 때 사용
412|Precondition Failed|조건부 요청을 받았는데 그중 하나가 실패했을 때 사용한다.
413|Request Entity Too Large|서버가 처리할 수 있는 한계를 넘은 크기의 요청을 클리아언트가 보냈을 때 사용
414|Request URI Too Long|서버가 처리할 수 있는 한계를 넘은 URL이 포함된 요청을 했을 때 사용
415|Unsupported Media Type|서버가 이해하거나 지원하지 못하는 내용 유형의 엔터티를 보냈을 때 사용
416|Requested Range Not Satisfiable|리소스의 특정 범위를 요청했는데, 그 범위가 잘못되었을 때 사용
417|Expectation Failed|요청의 Expect 요청 헤더에 서버가 만족시킬 수 없는 기대가 담겨있는 경우 사용

#### 3.4.5 500-599 : 서버 에러 상태 코드

상태 코드|사유 구절|의미
:---|:---|:---
500|Internal Server Error|서버가 요청을 처리할 수 없게 만드는 에러를 만남
501|Not Implemented|클라이언트가 서버의 능력을 넘은 요청을 했을 때 사용(예: 서버가 지원하지 않는 메서드 사용)
502|Bad Gateway|프락시나 게이트웨이처럼 행동하는 서버가 그 요청 응답 연쇄에 있는 다음 링크로부터 가짜 응답에 맞닥뜨렸을 때 사용(예: 자신의 부모 게이트웨이에 접속하는 것이 불가능할 때)
503|Service Unavailable|현재는 서버가 요청을 처리해 줄 수 없지만 나중에는 가능함을 의미하고자 할ㄷ 때 사용(Retry-After 헤더 사용)
504|Gateway Timeout|408과 비슷하지만, 다른 서버에게 요청을 보내고 응답을 기다리다 타임아웃이 발생한 게이트웨이나 프락시에서 온 응답이라는 차이점이 존재
505|HTTP Version Not Supported|서버가 지원할 수 없거나 지원하지 않으려 하는 버전의 프로토콜 요청을 받았을 경우 사용

<hr>

### 3.5 헤더

헤더에는 특정 종류의 메시지에만 사용할 수 있는 헤더와, 일반 목적으로 사용할 수 있는 헤더, 그리고
응답과 요청 메시지 양쪽 모두에서 정보를 제공하는 헤더가 있다.

1. 일반 헤더(General Headers)
   **클라이언트와 서버 양쪽 모두가 사용한다.**
   ```
   Date: Tue, 17 Nov 2020 23:13:00 GMT
   ```
2. 요청 헤더(Request Headers)
   요청 메시지를 위한 헤더다. 서버에게 받고자 하는 데이터의 타입이 무엇인지와 같은 부가 정보를 제공한다.
   아래의 Accept 헤더는 서버에게 클라이언트가 자신의 요청에 대응하는 어떤 미디어 타입도 받아들일 것임을 의미한다.
   ```
   Accept: */*
   ```
3. 응답 헤더(Response Headers)
   응답 메시지를 위한 헤더다.
   아래의 Server 헤더는 클라이언트에게 그가 Tiki-Hut 서버 1.0 버전과 대화하고 있음을 말해준다.
   ```
   Server: Tiki-Hut/1.0
   ```
4. 엔터티 헤더(Entity Headers)
   엔터티 본문에 대한 헤더이다. 엔터티 본문에 들어있는 데이터의 타입이 무엇인지 말해준다.
   아래의 Content-Type 헤더는 데이터가 iso-lation-1 문자집합으로 된 HTML 문서임을 알려준다.
   ```
   Content-Type: text/html; charset=iso-latin-1
   ```
5. 확장 헤더(Extension Headers)
   애플리케이션 개발자들에 의해 만들어졌지만 아직 승인된 HTTP 명세에는 추가되지 않은 비표준 헤더다.

#### 3.5.1 일반 헤더

메시지에 대한 아주 기본적인 정보를 제공하는 일반 헤더가 있다.
메시지가 어떤 종류이든 상관없이 유용한 정보를 제공한다.

헤더|설명
:---|:---
Connection| 클라이언트와 서버가 요청/응답 연결에 대한 옵션을 정할 수 있게 해준다.
Date | 메시지가 언제 만들어졌는지에 대한 날짜와 시간을 제공
MIME_Version|발송자가 사용한 MIME의 버전을 알려준다.
Trailer chunked transfer|인코딩으로 인코딩된 메시지의 끝 부분에 위치한 헤더들의 목록을 나열
Transfer-Encoding|수신자에게 안전한 전송을 위해 메시지에 어떤 인코딩인 적용되었는지 알려준다.
Upgrade|발송자가 '업그레이드'하길 원하는 새 버전이나 프로토콜을 알려준다.
Via|이 메시지가 어떤 중개자(프락시, 게이트웨이)를 거쳐 왔는지 보여준다.


*일반 캐시 헤더*

HTTP/1.0은 HTTP 애플리케이션에게 매번 원 서버로부터 객체를 가져오는 대신
로컬 복사본으로 캐시할 수 있도록 해주는 최초의 헤더를 도입했다.

헤더|설명
:---|:---
Cache-Control|메시지와 함께 캐시 지시자를 전달하기 위해 사용한다.
Pragma|메시지와 함께 지시자를 전달하는 또 다른 방법. 캐시에 국한되지 않는다. (deprecated 예정?)

#### 3.5.2 요청 헤더

요청 메시지에서만 의미를 갖는 헤더다.

헤더|설명
:---|:---
Client-IP|클라이언트가 실행된 컴퓨터의 IP를 제공한다
From|클라이언트 사용자의 메일 주소를 제공한다
Host|요청으 ㅣ대상이 되는 서버의 호스트 명과 포트를 준다
Referer|현재의 요청 URI가 들어있었던 문서의 URL을 제공한다
UA-Color|클라이언트 기기 디스플레이의 색상 능력에 대한 정보를 제공한다
UA-CPU|클라이언트 CPU의 종류나 제조사를 알려준다.
UA-Disp|클라이언트의 디스플레이(화면) 능력에 대한 정보를 제공
UA-OS|클라이언트 기기에서 동작 중인 운영체제의 이름과 버전을 알려준다
UA-Pixels|클라이언트 기기 디스플레이에 대한 픽셀 정보를 제공
User-Agent|요청을 보낸 애플리케이션의 이름을 서버에 알려준다

*Accept 관련 헤더*

Accept 헤더들을 이용해 서버에게 자신의 선호와 능력을 알려줄 수 있다.
클라이언트는 그들이 원하는 것을 얻을 수 있으며, 서버는 클라이언트가 사용할 수도 없는 것을 전송하는 데
시간과 대역폭을 낭비하지 않을 수 있다.

헤더|설명
:---|:---
Accept|서버에게 서버가 보내도 되는 미디어 종류를 말해준다
Accept-Charset|서버에게 서버가 보내도 되는 문자집합을 말해준다
Accept-Encoding|서버에게 서버가 보내도 되는 인코딩을 말해준다
Accept-Language|서버에게 서버가 보내도 되는 언어를 말해준다
TE|서버에게 서버가 보내도 되는 확장 전송 코딩을 말해준다

*조건부 요청 헤더*

클라이언트는 서버에게 요청에 응답하기 전에 먼저 조건이 참인지 확인하게 하는 제약을 포함시킬 수 있다.

헤더|설명
:---|:---
Except|클라이언트가 요청에 필요한 서버의 행동을 열거할 수 있게 해준다.
If-Match|문서의 엔터티 태그가 주어진 엔터티 태그와 일치하는 경우에만 문서를 가져온다
If-Modified-Since|주어진 날짜 이후에 리소스가 변경되지 않았다면 요청을 제한한다.
If-None-Match|문서의 엔터티 태그가 주어진 엔터티 태그와 일치하지 않는 경우에만 문서를 가져온다
If-Range|문서의 특정 범위에 대한 요청을 할 수 있다.
If-Unmodified-Since|주어진 날짜 이후에 리소스가 변경되었다면 요청을 제한한다.
Range|서버가 범위 요청을 지원한다면, 리소스에 대한 특정 범위를 요청한다

*요청 보안 헤더*

HTTP는 자체적으로 요청을 위한 간단한 인증요구/응답 체계를 갖고 있다.

헤더|요청
:---|:---
Authorization|클라이언트가 서버에게 제공하는 인증 그 자체에 대한 정보를 담고 있다
Cookie|클라이언트가 서버에게 토큰을 전달할 때 사용한다. 진짜 보안 헤더는 아니지만, 보안에 영향을 줄 수 있다는 것은 확실하다.
Cookie2|요청자가 지원하는 쿠키의 버전을 알려줄 때 사용한다.

*프락시 요청 헤더*

헤더|설명
:---|:---
Max-Forwards|요청이 원 서버로 향하는 과정에서 다른 프락시나 게이트웨이로 전달될 수 있는 최대 횟수. TRACE 메서드와 함께 사용된다
Proxy-Authorization|Authorization과 같으나 프락시에서 인증을 할 때 쓰인다
Proxy-Connection|Connection과 같으나 프락시에서 연결을 맺을 때 쓰인다

#### 3.5.3 응답 헤더

헤더|설명
:---|:---
Age|응답이 얼마나 오래되었는지
Public|서버가 특정 리소스에 대해 지원하는 요청 메서드의 목록
Retry-After|현재 리소스가 사용 불가능한 상태일 때, 언제 가능해지는지 날짜 혹은 시각
Server|서버 애플리케이션의 이름과 버전
Title|HTML 문서에서 주어진 것과 같은 제목
Warning|사유 구절에 있는 것보다 더 자세한 경고 메시지

*협상 헤더*

헤더|설명
:---|:---
Accept-Ragnes|서버가 자원에 대해 받아들일 수 있는 범위의 형태
Vary|서버가 확인해 보아야 하고 그렇기 때문에 응답에 영향을 줄 수 있는 헤더들의 목록

*응답 보안 헤더*

헤더|설명
:---|:---
Proxy-Authenticate|프락시에서 클라이언트로 보낸 인증요구의 목록
Set-Cookie|진짜 보안 헤더는 아니지만, 보안에 영향은 줄 수 있다. 서버가 클라이언트를 인증할 수 있도록 클라이언트 측에 토큰을 설정하기 위해 사용한다.
Set-Cookie2|Set-Cookie와 비슷하게 RFC 2965로 정의된 쿠키
WWW-Authenticate|서버에서 클라이언트로 보낸 인증요구 목록

#### 3.5.4 엔터티 헤더

요청과 응답 양쪽 모두 엔터티를 포함할 수 있기 때문에, 이 헤더들은 양 타입의 메시지에 모두 나타날 수 있다.

헤더|설명
:---|:---
Allow|이 엔터티에 대해 수행될 수 있는 요청 메서드들을 나열한다
Location|클라이언트에게 엔터티가 실제로 어디에 위치하고 있는지 말해준다. 수신자에게 리소스에 대한(아마도 새로운) 위치 URL을 알려줄 때 사용한다

*콘텐츠 헤더*

엔터티의 콘텐츠에 대한 구체적인 정보를 제공한다. 예를 들어 Content-Type 헤더를 보고 그 객체를 어떻게
보여줄 지 결정할 수 있다.

헤더|설명
:---|:---
Content-Base|본문에서 사용된 상대 URL을 계산하기 위한 기저 URL
Content-Encoding|본문에 적영된 어떤 인코딩
Content-Language|본문을 이해하는데 가장 적절한 자연어
Content-Length|본문의 길이나 크기
Content-Location|리소스가 실제로 어디에 위치하는지
Content-MD5|본문의 MD5 체크섬
Content-Range|전체 리소스에서 이 엔터티가 해당하는 범위를 바이트 단위로 표현
Content-Type|이 본문이 어떤 종류의 객체인지

*엔터티 캐싱 헤더*

엔터티 캐싱 헤더는 엔터티 캐싱에 대한 정보를 제공한다.

헤더|설명
:---|:---
ETag|엔터티에 대한 엔터티 태그
Expires|이 엔터티가 더 이상 유효하지 않아 원본을 다시 받아와야 하는 일시
Last-Modified|가장 최근 이 엔터티가 변경된 일시





