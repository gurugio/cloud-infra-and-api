
# 그림으로 배우는 클라우드 인프라와 API의 구조

## 2023-06-10

HTTP 소개
* 클라우드 환경에서도 HTTP 프로토콜과 동일하게 URL 사용함
* HTTP 프로토콜은 stateless임. 상태/연결 정보를 관리하지 않음
* 쿠키나 킵얼라이브 기술을 사용해야함
* 쿠키: 서버가 보낸 정보를 저장
* 웹 API 요청때마다 TCP 연결을 생성하고 끊게됨 - 트래픽 발생
* 킵얼라이브: TCP연결을 계속 유지할 수 있음

HTTP 요청
* 요청행 request line, 요청 헤더 request header, 메세지 바디 message body 세부분으로 구성됨
* 요청행: 메소드, request URI, HTTP 버전 정보
  * 메소드: URI에 대한 제어 행위이자 액션
  * 수신지 URI: 제어할 리소스
  * URI를 FQDN와 경로로 표현 / URI를 절대 경로로 표현
* 요청 헤더: 쿠키나 킵얼라이브와 같은 통신 관련 제어 정보나 메타 데이터
* 메세지 바디: 송수신 데이터

HTTP 응답
* status line: 상태 코드 정보
* response header: 서버가 클라이언트에 서버의 정보를 전달
* message-body: 요청받은 데이터

HTTP 메소드
* POST: 리소스 생성 - CURD에서 Create
* GET: 리소스 조회 - CRUD에서 Read
* PUT: 리존 리소스 수정 - CRUD에서 Update
* DELETE: 리소스 삭제 - CRUD에서 Delete
* HEAD: HTTP헤더, 메타 정보 - Read

HTTP 상태 코드
* 200 OK GET성공
* 201 Create: POST성공
* 202 Accepted: 요청은 접수되었으나 리소스 처리는 완료되지않음
* 204 No Contents: 요청은 성공했으나 제공한 컨텐츠가 없음
* 500 Internal Server Error: 클라우드 측의 내부 오류
* 503 Service Unavailable: 일시적으로 클라우드 측에 과부하나 다운

200번대 통신 성공
400번대 클라이언트 어류
500번대 서버측 오류
 
HTTP 요청을 보낼 때 Accept 헤더에 Application/json, test/plain 타입으로 요청하면 응답 포맷 지정

cURL program
* -X HTTP method
* -H header
* -i: print HTTP header
* -u user
* -cacert: SSL certificate
* -d: body
```
curl -i -X POST http://compute/v2/{tenant-id}/servers/{server-id}action \
-H "Content-Type: application/json" \
-H "Accept: application:json" \
-H "X-Auth-Token: {****}" \
-d '{"reboot": {"Type": "SOFT"}}'
```

ROS: Resource Oriented Architecture
* REST API 사상을 기반으로 리소스 중심적인 API를 사용하는 아키텍처
* REST: Representational State Transfer
* REST 기반 서비스의 지침
  * 상태를 갖지 않는다
  * URI는 디렉토리 구조처럼 계층적으로
  * HTTP 메소드를 명시적으로 사용한다
  * 응답할때는 XML이나 JSON
* API를 행위 중심이 아닌 자원 중심으로 설계할 때 반드시 필요한 고려사항
* 분산 환경 제어를 위한 세가지 필수 개념
  * 비동기: 먼저 보낸 요청이 뒤에 보낸 요청보다 먼저 서버에 도착하지 않을 수 있음
  * 멱등성: API를 여러번 호출해도 리소스에 변경이 발생하지 않는 한 같은 응답을 받게됨
  * 재시도: 네트워크 상태가 좋지 않아 오류가 발생해도 다시 시도하면 옳바른 값을 받을 수 있음



## 2023-05-31

* 웹API = 인증처리 + 제어할 대상 + 제어 행위
* 인증처리: 클라우드 서비스가 제공하는 독자적인 인증 기능 => Actor
* 제어할 대상: 리소스, URI로 표현됨 => Resource
* 제어행위: HTTP 메소드 => Action

* 리소스
  * 서버, 스토리지, 네트워크, 인스턴스, 이미지, 키 페어 등이 리소스
  * 리소스를 구분하기 위해 UUID나 이름을 사용함

* 액션
  * API 제어 행위
  * CRUD: Create, Read, Update, Delete

* 리소스
  * URI는 네트워크 부분과 경로 부분으로 구성됨
  * API를 공개하여 서비스를 제공하는 접점 혹은 API 호출을 받아서 처리하는 접점을 엔드포인트라고함
  * 엔드포인트의 경로를 가리키는게 URI
  * 도메인: 네트워크상에서 자원의 위치를 표현하는 것, IP가 아니라 문자 형태로 표현

## 2023-05-26

* 이메퍼럴 디스크: Ephemeral 수명이 짧은 이라는 뜻, 인스턴스가 종료되면 데이터도 사라짐
* 인스턴스를 실행할 때 템플릿 이미지를 사용해서 기동할 수 있음 -> 인스턴스가 종료되면 데이터도 사라짐 -> 블럭 장치로 부팅
* 오브젝트 스토리지
  * 파일 단위로 데이터를 저장하는 데이터 스토어
  * HTTP/s 프로토콜을 사용하는 파일 서버?
  * 인스턴스뿐 아니라 외부 네트워크에서도 접근되는 데이터
  * 이미 저장된 파일의 덮어쓰기가 안됨 -> 파일을 삭제한 후 다시 저장해야함
  * 가용성이 높고 (멀티 AZ) 처리율이 우수 (속도 빠름?)
  * 동영상같은 정적 컨텐츠에 주로 사용
    * aws 오브젝트 스토리지는 버킷을 리젼별로 만듬 -> 다른 리전의 인스턴스에서 생성한 오브젝트도 다른 인스턴스에서 접근 가능
  * 실제로 디렉토리가 존재하는게 아니라 이름만 /을 써준것임
  * 각 오브젝트에 key-value의 메타 데이터를 부여할 수 있음
  * 버저닝 - 오브젝트에 버전 번호를 붙여 관리하는 기능
    * 같은 이름의 파일을 저장하면 기존 파일을 덮어 쓰지않고 새로운 버전 번호로 파일을 저장
    * 기존 파일을 해당 버전 번호로 복구할 수 있음
  * 정적 웹 호스팅 - 오브젝트 스토리지를 웹 서버로 사용, 버킷을 누구나 읽을 수 있게 설정하고 정적 HTML파일, 이미지, 동영상 등 저장 - URL로 웹페이지 접근
  * 블록 스토리지의 백업에도 사용 - 볼륨을 잘라서 오브젝트로 저장/복원 - 다른 리전이나 AZ에서도 받아서 복원가능
