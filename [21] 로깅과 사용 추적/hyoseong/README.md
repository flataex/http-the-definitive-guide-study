# 로깅과 사용 추적

## 21.1 로그란무엇인가?

- 디버깅이나 웹 사이트 접근 통계(서버나 대역폭 조정 등을 위해)를 내려고 로깅을 합니다.
- 일반적으로 로깅하는 필드
  - HTTP 메서드
  - 클라이언트와 서버의 HTTP 버전
  - 요청받은 리소스의 URL
  - 응답의 HTTP 상태 코드
  - 요청과 응답 메시지의 크기(모든 엔터티 본문을 포함)
  - 트랜잭션이 일어난 시간
  - Referer와 User-Agent 헤더 값

## 21.2 로그 포맷

### 21.2.1 일반 로그 포맷(Common Log Format)

- 필드
  - remotehost
  - username
  - auth-username
  - timestamp
  - request-line
  - response-code
  - response-size

### 21.2.2 혼합 로그 포맷(Combined Log Format)

- Referer, User-Agent 두 개의 필드가 추가되고 일반 로그 포맷이랑 동일

### 21.2.3 넷스케이프확장 로그포맷

- 필드
  - proxy-response-code
  - proxy-response-size
  - client-request-size
  - proxy-request-size
  - client-request-hdr-size
  - proxy-response-hdr-size
  - proxy-request-hdr-size
  - server-response-hdr-size
  - proxy-timestamp

### 21.2.4 넷스케이프 확장 2 로그 포맷

- 넷스케이프 확장2 로그 포맷에서 추가된 필드
  - route
  - client-finish-status-code
  - proxy-finish-status-code
  - cache-result-code

### 21.2.5 스퀴드(Sq니d) 프락시 로그 포맷

- 필드
  - timestamp
  - time-elapsed
  - host-ip
  - result-code/status
  - size
  - method
  - url
  - rfc931-identc
  - hierarchy/from
  - content-type

## 21.3 적중 계량하기

- 로깅을 한다고 해도 캐시 때문에 제대로 로깅이 안되는 일들이 발생합니다.
- 적중 계량 규약에서는 캐시가 정기적으로 캐시 접근 통계를 원 서버에 보고하도록 합니다.

### 21.3.1 개요

- 콘텐츠의 캐시를 파기하 는 대신 적중 계량 규약을 통해 통계를 냅니다.

### 21.3.2 Meter 헤더

- 적중 계량 확장은 Mete라는 새로운 헤더를 추가되는데 사용량이나 보고 에 관한 지시자가 기술합니다.

## 21.4 개인 정보 보호에 대해

- 로깅을 통해 사용자에 관한 많은 정보 를 모을 수 있기에 로깅하는 웹 서버와 프락시는 최종 사용자의 개인 정보를 보호하는데 신경을 많이 써야 합니다.
