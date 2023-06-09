# 캐시

웹 캐시는 자주 쓰이는 문서의 사본을 자동으로 저장하는 HTTP의 장치이다.

- 불필요한 데이터 전송을 줄여 네트워크 요금을 줄인다.
- 네트워크 병목을 줄여준다.
- 서버에 요청을 줄여 서버의 부하를 줄여주고, 더 빨리 응답할 수 있다.
- 거리로 인한 지연이 줄어든다.

> **불필요한 데이터 전송**  
> 여러 클라이언트가 원 서버에 동일한 콘텐츠를 요청할 때, 서버는 각각 한 번씩 동일하게 전송해야한다.  
> 똑같은 바이트가 네트워크를 통해 반복해서 이동하는 것이다. 이는 대역폭을 잡아먹고, 전송을 느리게 하며 서버에 부하를 준다.  
> 서버가 캐시를 이용하면, 첫번째 응답이 캐시에 저장된다. 뒤이은 동일한 요청에 대해 캐시된 사본을 사용할 수 있다.

> **대역폭 병목**  
> 많은 네트워크가 원격 서버보다 로컬 클라이언트에 더 넓은 대역폭을 제공한다.  
> 만약 클라이언트가 빠른 LAN(근거리 통신망)에서 캐시된 사본을 가져오면 성능을 대폭 개선할 수 있다.

> **갑작스러운 요청 쇄도**  
> 캐싱은 갑작스러운 요청에 대처하기 위해 특히 중요하다. 캐싱되지 않을 경우 네트워크와 원 서버에 장애를 야기한다.

> **거리로 인한 지연**  
> 대역폭 이외에도 클라이언트와 서버 간의 거리가 문제될 수도 있다.

<br />

## 적중과 부적중

위와 같이 캐싱은 유용하다. 하지만 캐시가 모든 문서의 사본을 저장하진 않는다. ㅐ시에 요청이 도착했을 때, 저장된 사본이 있다면 이를 `적중`, 없다면 `부적중`이라고 한다. `부적중`인 경우 캐시는 요청을 원 서버에 전달한다.

### 1. 재검사

캐시는 저장된 사본이 원 서버에 있는 원본과 동일한지 `HTTP 재검사`를 한다. HTTP는 서버로부터 전체 객체를 가져오지 않고도 여전히 최신의 상태인지 검사하는 요청 방법을 정의했다.

<img width="600" alt="image" src="https://github.com/flataex/http-the-definitive-guide-study/assets/67260437/392fca60-7751-41c3-ac22-5fb32d1b4df8">

- 대부분의 캐시는 클라이언트로부터 요청이 들어왔고, 사본이 꽤 오래된 경우 재검사를 진행한다.
- 캐시는 서버에 `If-Modified-Since 헤더`를 포함하는 재검사 요청을 보낸다.
- 콘텐츠가 변경되지 않았다면 서버는 캐시에 `304(Not Modified)`를 보낸다. 캐시는 사본이 최신 상태라고 판단하고, 클라이언트에 사본을 반환한다. 이를 `재검사 적중` 또는 `느린 적중`(사본이 있음에도 서버와 한 번 통신하기 때문에)이라고 한다.
- 콘텐츠가 변경된 경우 서버는 `200`으로 보내고, 삭제된 경우 `404`로 보낸다. `404`를 받은 경우 캐시는 사본을 삭제한다.

### 2. 적중률

캐시가 요청을 처리하는 비율을 **적중률(문서 적중률)**이라고 한다.  
적중률이 40% 정도일 때 적절하다고 보기도 하지만 이 적정률이 모든 것을 말해주진 않는다. 접근이 빈번하진 않지만 큰 객체에 대해 적중한 경우 트래픽에 더 많은 기여를 하기 때문이다. 때문에 **바이트 적중률**을 따지는 경우도 있다.  
바이트 적중률은 캐시를 통해 전달된 바이트의 비율을 표현한다.  
문서 적중률은 트랜잭션 개선(지연 시간 개선)을 판단할 때 사용되고 바이트 적중률은 대역폭 개선을 판단할 때 사용된다.

### 3. 적중과 부적중의 구별

HTTP는 클라이언트에게 응답이 서버에서 온 것인지, 캐시에서 온 것인지에 대한 정보를 주지 않는다. 대신 클라이언트는 프락시에서 온 via 헤더를 보거나 응답 메시지의 Date 헤더값을 보고 판단할 수 있다.

<br />

## 캐시 토폴로지

캐시는 한 명에게만 할당될 수도 있고, 공용으로 할당될 수도 있다.

웹브라우저는 개인 전용 캐시를 내장하고 있다. 자주 접근하는 문서를 개인의 컴퓨터 디스크나 메모리에 저장한다.  
공용 캐시는 6장에서 다룬 프락시 캐시로, 불필요한 트래픽을 줄일 수 있다.

### 1. 프락시 캐시 계층들

작은 캐시에서 캐시 부적중이 발생했을 때 부모 캐시가 그 트래픽을 처리하도록 하는 계층을 만드는 방식이 합리적인 경우가 많다.

<img width="594" alt="image" src="https://github.com/flataex/http-the-definitive-guide-study/assets/67260437/45a4bb00-26d6-488b-9ced-eaf9d45f545b">

다만 캐시 계층이 깊어 프락시 연쇄가 길어질 경우 중간 계층에는 성능 저하가 있을 수 있다.

### 2. 캐시망, 콘텐츠 라우팅, 피어링

- 캐시망  
   몇몇 네트워크 아키텍처는 캐시 계층 대신 캐시망을 사용한다.  
   캐시망의 프락시 캐시는 어떤 부모 캐시를 사용할지 아니면 서버에 요청할 것인지 동적으로 결정한다.
- 콘텐츠 라우팅  
  캐시망 안에서 콘텐츠 라우팅을 위해 설계된 캐시는 다음의 일을 수행한다.
  - URL에 기반해 부모 캐시와 원 서버 중 하나를 선택한다.
  - URL에 기반해 특정 부모 캐시를 선택한다.
  - 부모 캐시에 접근하기 전에 로컬에서 사본을 찾아본다.
  - 다른 캐시들이 그들의 캐시된 콘텐츠에 부분적으로 접근할 수 있도 허용하되, 그들의 캐시를 통한 인터넷 트랜짓은 허용하지 않는다.
- 피어링  
   서로 다른 조직들이 그들의 캐시를 연결하여 서로를 찾아볼 수 있도록 해준다. 선택적인 피어링을 지원하는 캐시는 형제 캐시라고 불린다. HTTP는 형제 캐시를 지원하지 않기 때문에, 사람들은 인터넷 캐시 프로토콜(ICP)이나 하이퍼텍스트 캐시 프로토콜(HTCP)같은 프로토콜을 이용해 HTTP를 확장했다.

<br />

## 캐시 처리 단계

1. 요청받기  
   캐시는 네트워크 트랜잭션을 감지해 요청 데이터를 읽는다.
2. 파싱  
   요청 메시지에서 URL, 헤더를 처리하기 쉽게 파싱한다.
3. 검색  
   URL을 기반으로 로컬 디스크나 메모리에 해당 사본이 있는지 찾아본다. 로컬에 없다면 원 서버나 부모 프락시에 요청하거나 실패를 반환한다.  
   캐시된 객체는 서버 응답 헤더와 본문 모두 갖고 있다.
4. 신선도 검사  
   HTTP는 최신의 상태인 문서로 판단하는 일정 기간을 설정한다. 이 기간이 지나면 서버에 재검사 요청을 해야한다.
5. 응답 생성  
   캐시된 응답 헤더를 토대로 응답 헤더를 생성한다. 이때 클라이언트에 맞게 헤더를 조정한다. 여기에 사본의 상태를 알려주는 `Cache-Control, Age, Expires` 헤더를 포함시키며, `Via` 헤더를 제공하기도 한다.  
   **다만 Date 헤더는 수정하지 않는다.** Date 헤더는 원 서버에서 최초로 생겨난 일시를 표현하는 것이다.
6. 응답 전송
7. 로깅  
   각 트랜잭션 완료 후 적중과 부적중 횟수를 갱신하고 요청 종류, URL 등을 추가한다.

<br />

## 사본을 신선하게 유지하기

### 1. 문서 만료

HTTP는 원 서버가 Cache-Control과 Expires 헤더를 이용해 각 문서의 유효기간을 붙일 수 있게 해준다.

|                       |                                                                                             |           |
| --------------------- | ------------------------------------------------------------------------------------------- | --------- |
| Cache-Conrol: max-age | 문서의 최대 나이를 설정한다. ex) max—age=484200                                             | HTTP/1.1  |
| Expires               | 절대 유효기간을 명시한다. 절대 시간은 컴퓨터의 시계가 올바르게 맞추어져 있을 것을 요구한다. | HTTP/1.0+ |

### 2. 서버 재검사

재검사 결과 콘텐츠가 변경되었다면 캐시는 새로운 콘텐츠의 사본을 저장하고 클라이언트에 반환한다. 콘텐츠가 변경되지 않았다면 만료일이 갱신된 헤더만 가져와 캐시의 헤더를 갱신한다.

### 3. 조건부 메서드와의 재검사

HTTP는 조건부 메서드를 이용해 효율적으로 재검사할 수 있도록 5가지 헤더를 제공한다.  
그 중 가장 유용한 헤더는 `If-Modified-Since`와 `If-None-Match`이다.  
캐시는 콘텐츠가 변경되었을 때만 응답 본문을 보내도록 요청한다.

#### **If-Modified-Since: 날짜 재검사**

만약 문서가 주어진 날짜 이후로 수정되었는지 확인한다.  
서버는 응답 헤더로 `Last-Modified` 헤더를 보내 가장 최근에 변경된 일시를 알려준다.  
콘텐츠가 변경되었다면 캐시는 If-Modified-Since를 Last-Modified 값으로 갱신한다.

#### **If-None-Match: 엔터티 태그 재검사**

엔터티 태그 재검사 방법은 아래의 경우에 유용하다.

- 일정 시간마다 최신화되긴 하지만 내용은 같은 경우
- 철자나 주석 같은 사소한 변경만 일어난 경우
- 서버에서 정확한 변경일시를 확인하지 못하는 경우
- 매우 짧은 간격으로 변경되는 경우
  > [예시]  
  > If-None-Match: Hv2.6"  
  > If-None-Match: "v2.4","v2.5","v2.6"  
  > If-None-Match: "foobar","A34FAC0095,‘,"Profiles in Courage"

### 4. 약한 검사기와 강한 검사기

**strong validator**는 바이트가 동일한지 아닌지 확인하는 방식으로 콘텐츠가 바뀔 때마다 값이 바뀐다.  
하지만 서버는 때때로 모든 캐시 사본을 무효화하지 않고 일부만 변경하고자 할 때가 있다. 이를 위해 HTTP는 **weak validator**를 지원한다. 서버는 `W/`로 weak validator를 구분한다.

> [예시]  
> ETag: "W/v2.6" 헤더가 있는 경우, 이는 ETag: "v2.6"과 동일한 것으로 간주된다.

서로 다른 객체라더라도 강한 엔터티 값이나 약한 엔터티 값은 재활용되면 안된다.

### 5. 언제 엔터티 태그를 사용하고 언제 Last-Modified 일시를 사용하는가

HTTP/1.1 클라이언트는 서버가 엔터티 태그를 보낸 경우 엔터티 태그 검사를 해야하며, 서버가 Last-Modifited 값만 보낸 경우 If-Modified-Since 검사를 할 수 있다. 서버에서 두가지 모두 보낸 경우엔 HTTP/1.0과 HTTP/1.1 캐시 모두 적절히 응답할 수 있도록 두 가지의 재검사 정책을 모두 사용해야 한다.

<br/>

## 캐시제어

HTTP는 얼마나 오랫동안 캐시를 유지할 것인지 서버에서 정할 수 있는 방법을 제공한다.

|                                         |                                                                                                                                                                            |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cache-Control: no-store                 | 캐시가 응답 사본을 만드는 것을 금지한다. 캐시는 클라이언트에 객체를 보내고 삭제한다.                                                                                       |
| Cache-Control: no-cache                 | 로컬 캐시 저장소에 사본을 저장할 수 있지만 서버에 재검사받지 않고 클라이언트에 제공할 수 없다.                                                                             |
| Cache-Control: max—age=3600             | 0으로 설정하여 캐시하지 않도록 설정할 수 있다.                                                                                                                             |
| Cache-Control: s-maxage=3600            | 공용 캐시에 사용된다.                                                                                                                                                      |
| Expires: Fri, 05 Jul 2002, 05:00:00 GMT | 캐시를 막기위해 Expires: 0과 같이 사용하지 않도록 주의한다.                                                                                                                |
| Cache-Control: must-revalidate          | 캐시 만료후 최초 조회시 원 서버에 검증해야 할 때 설정한다. 원 서버에 접근이 불가한 경우 캐시된 사본을 클라이언트에 제공하지 않고, `504(Gateway Timeout error)`를 반환한다. |

### 1. 휴리스틱 만료

응답이 `Cache-Control: max-age` 헤더나 `Expires` 헤더 모두 포함하지 않다면, 캐시는 경험적인 방법으로(heuristic) 최대 나이를 계산한다.

- LM 인자 알고리즘  
   문서의 최근 변경 일시가 얼마나 자주 바뀌는지에 대해 추정한다.  
   일반적으로, 휴리스틱 유지기간에 상한을 설정하여 지나치게 커지는 것을 막는다.
- 아무런 단서가 없는 경우  
   보통 한시간 내지 하루 정도로 기본 신선도 유지기간을 설정한다.

### 2. 클라이언트 신선도 제약

클라이언트는 `Cache-Control` 요청 헤더를 사용하여 만료 제약을 엄격하게 하거나 느슨하게 할 수 있다.

|                                          |                                                                       |
| ---------------------------------------- | --------------------------------------------------------------------- |
| Cache-Control: max-stale`(=<s>)`         | 캐싱 규칙을 느슨하게 하는 헤더. (`<s>만큼`) 만료된 사본도 받아드린다. |
| Cache-Control: min-fresh`=<s>`           | 캐싱 규칙을 느슨하게 하는 헤더. `<s>`동안 최신 사본만 받아드린다.     |
| Cache-Control: max-age`=<s>`             | `<s>` 초보다 오랫동안 캐시된 문서를 받을 수 없다.                     |
| Cache-Control: no-cache Pragma: no-cache | 사본을 재검사하기 전에는 받을 수 없다.                                |
| Cache-Control: no-store                  | 캐시에서 즉시 삭제되어야한다.                                         |
| Cache-Control: only-if-cached            | 캐시에 들어있는 사본만 받는다.                                        |
