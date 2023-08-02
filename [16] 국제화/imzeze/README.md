# 국제화

## 국제적인 컨텐츠를 다루기 위해 필요한 HTTP 지원

서버는 클라이언트에게 각 문서의 문자와 언어를 알려줘서 클라이언트가 엔터티 본문을 올바르게 처리할 수 있도록 해야한다.
이를 위해 `Content-Type charset` 매개변수와 `Content-Language` 헤더가 사용된다.

클라이언트는 사용자가 어떤 언어를 이해할 수 있고 어떤 알파벳 코딩 알고리즘이 서버에 설치되어 있는지 말해줄 필요가 있다.
이를 위해 `Accept-Charset`과 `Accept-Language` 헤더가 사용된다.

## 문자집합과 HTTP

### Charset은 글자를 비트로 변환하는 인코딩이다

charset 매개변수는 수신자에게 엔터티 본문을 디코딩하기 위해 사용되어야하는 기법을 말해준다.

> Content-Type: text/html; charset=iso-8859-6

### 문자집합과 인코딩은 어떻게 동작하는가

<img width="607" alt="image" src="https://github.com/flataex/http-the-definitive-guide-study/assets/67260437/b644c769-e9f1-418f-a6c2-b9ff03538994">

1. 데이터가 charset 매개변수에 명시된 방법으로 디코딩된다.
2. 디코딩된 문자를 문자집합에서 식별해 해당하는 문자를 반환한다.

잘못된 charset은 잘못된 글자를 낳는다

### 표준화된 MIME charset 값

특정 문자 인코딩과 특정 코딩된 문자집합의 결합을 MIME charset이라고 한다. HTTP는 이 태그를 Content-Type과 Accept-Charset에서 사용한다.

### Content-Type charset 헤더와 META 태그

만약 헤더에 문자집합이 명시되지 않았다면 수신자는 받은 데이터로부터 문자집합을 추론한다. 이를 META 태그에서 찾을 수 있다.

> 〈META HTTP-EQUIV="Content-Type" CONTENT="text/html; charset=iso-2022-jp"〉

만약 HTML 파일이 아니거나 위와 같은 패턴이 없으면 소프트웨어는 텍스트를 스캐닝해 인코딩을 추측한다. 추측하지 못한 경우 iso-8859-1인 것으로 가정한다.
