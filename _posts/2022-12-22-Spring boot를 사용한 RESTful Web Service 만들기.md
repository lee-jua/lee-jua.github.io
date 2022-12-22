---
title: Spring boot를 사용한 RESTful Web Service 만들기
author: 이주아
date: 2022-12-22 22:03:00 +0900
categories: [개발 실습]
tags: [Spring boot,REST API,JPA,인프런]
---


'**동적인 웹 페이지를 만들기 위한 JAVA 웹 프로그래밍 기술**'이라는 포스팅에서 자바 웹 프로그래밍과 스프링에 대해서 정리한 적이 있습니다. 그 다음 이어지는 내용으로 스프링 부트로 만드는 RESTful Web Service에 대해서 정리하려고 합니다. 인프런의 다음의 강의를 수강했습니다. 

![Desktop View](/assets/img/20221204/1.png){: width="400"}  

2년정도 전에 학원에 다닐때 배웠던 내용이 대부분인데 오랜만에 다시 배우려니까 꽤 어렵더라고요. 총 6시간 43분의 짧은 강의지만 체감으로는 꽤나 길었습니다. 이번 포스팅에서는 RESTful Web Service를 구성하는 RESTful API에 대해서 알아보고, 이후로는 스프링부트로 이를 구현해보려고 합니다.


----


## RESTful API란

RESTful API에 대해서 설명하기 전에 API란 무엇인지에 대해서 먼저 얘기해 보도록 하겠습니다. 다음은 위키백과에 정의된 API와 인터페이스에 대한 내용입니다.

#### API(Application Programming Interface) 
> 응용 프로그램에서 사용할 수 있도록, 운영 체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스를 뜻한다

#### 인터페이스
> 2개 이상의 장치나 소프트웨어 사이에서 정보나 신호를 주고받을때 그 사이를 연결하는 연결장치나 경계면, 또는 연결하는 경계에서 상호 접속하기 위한 하드웨어, 소프트웨어, 조건, 규약 등을 말한다.

인터페이스의 의미가 너무 넓어서 잘 와닿지 않을 수도 있습니다. 예를 들어 에어컨을 켜기 위해 손가락으로 에어컨 본체의 전원 버튼을 눌렀을 때, 손가락과 에어컨은 신호를 주고받았다고 할 수 있습니다. 이 때 에어컨의 전원버튼이 사람의 손가락과 에어컨을 연결해주는 인터페이스가 됩니다.  

인터페이스는 하드웨어 인터페이스, 소프트웨어 인터페이스, 사용자 인터페이스로 구분합니다. 그 중 소프트웨어, 그리고 그 중에서도 응용 프로그램 두 개를 연결하는 인터페이스를 API라고 합니다.

또한 웹 API는 웹 프로그래밍에서 클라이언트와 웹 리소스 간의 통신 방 여기에서 클라이언트는 정보에 엑세스하려는 사용자이며, 리소스는 애플리케이션이 클라이언트에게 제공하는 정보를 의미합니다. RESTful API에서의 API는 웹 API를 의미합니다.

REST는 Representational State Transfer의 약자로, REST 아키텍쳐 스타일을 따르는 API를 RESTful API라고 합니다. 이것은 URL(Uniform Resource Locator)을 사용하여 리소스를 식별합니다. 또한 HTTP 메서드를 사용하여 리소스에 수행해야 하는 작업을 서버에 알려줍니다. RESTful API를 통해 두 컴퓨터 시스템이 인터넷을 통해 정보를 안전하게 교환할 수 있습니다. 


<br>

## RESTful API의 주요 규칙

RESTful API의 주요 규칙은 다음의 두가지가 있습니다.  

- 고유 리소스 식별자인 **URL**(Uniform Resource Locator)로 자원을 구분한다.
- 자원에 대한 행위는 **HTTP Method**로 표현한다.
  

``` GET /user/1 ```  
``` GET /user/99/post/1 ```

위와 같이 서버에서 리소스를 불러오는 두 개의 API가 있습니다. 위의 API는 첫번째 user의 정보를 가지고 오는 것이고, 아래의 API는 99번째 user가 첫번째로 작성한 포스트의 정보를 가지고 오는 것입니다. '자원을 구분한다'의 의미는 이와 같이 각각 다른 자원을 다른 URL로 명시하여 구분짓는 것을 의미합니다.

HTTP 메서드의 종류는 다음과 같습니다.
- GET : 리소스를 조회한다.
- POST : 서버로 데이터를 전달한다. 주로 리소스를 새로 등록하는데에 사용.
- PUT : 리소스를 업데이트한다.
- DELETE : 리소스를 삭제한다.    

주로 사용하는 메서드 외의 다른 메서드는 다음을 참고해주세요. <sup>[2]</sup> 

<br>

## HTTP 상태코드

또한 RESTful API에서는 **HTTP 상태코드**를 통해 요청에 대한 응답의 상태, 즉 성공/실패 여부와 실패 이유 등을 표시합니다. 저는 간단한 웹 서비스를 개발할 때는 상태코드 설정의 중요성을 인지하지 못했는데, Cafe24의 패키징된 API를 사용할 때는 아주 유용하게 사용했던 경험이 있습니다. 

HTTP 상태코드의 첫번째 숫자에 따라 응답의 종류를 구분할 수 있습니다.
- 100번대 (Informational) : 조건부 응답
- 200번대 (Successful) : 성공
- 300번대 (Redirection) : 리다이렉션 완료
- 400번대 (Client Error) : 요청 오류
- 500번대 (Server Error) : 서버 오류

간단한 RESTful Web Service를 만들때 주로 사용하게될 상태코드는 다음과 같습니다.


**200 OK** : 요청이 성공적으로 수행되었습니다.

**201 Created** : 요청이 성공적이었으며 그 결과로 새로운 리소스가 생성되었습니다.

**302 Found** : 요청한 리소스의 URI가 일시적으로 변경되었음을 의미합니다. 

**400 Bad Request** : 잘못된 문법으로 인하여 서버가 요청을 이해할 수 없습니다.

**401 Unauthorized** : 인증되지 않은 사용자의 요청입니다.

**403 Forbidden** : 클라이언트가 콘텐츠에 접근할 권한을 가지고 있지 않음을 의미합니다. 401과 다른 점은 서버가 클라이언트가 누구인지 알고 있다는 것입니다. 

**404 Not Found** : 요청한 리소스를 찾을 수 없습니다. 또는 리소스가 존재하지 않습니다. 브라우저에서는 알려지지 않은 URL을 의미합니다. 

**500 Internal Server Error** : 서버가 처리방법을 모르는 오류가 발생했습니다.

**502 Bad Gateway** : 서버가 게이트웨이로부터 잘못된 응답을 수신했습니다.

**504 Gateway Timeout** : 서버가 게이트웨이 역할을 하고 있으며, 한 서버가 액세스하고 있는 다른 서버에서 적시에 응답을 받지 못했음을 의미합니다.

더 많은 HTTP 상태코드는 다음을 참고해주세요.<sup>[3]</sup> 

<br>

이번 포스팅에서 RESTful API의 개념을 살펴보고 이를 구현하기 위한 URL, HTTP Method, HTTP상태코드에 대해서 정리해봤습니다. 다음 포스팅에서는 스프링부트로 해당 내용을 구현해보도록 하겠습니다.

<br>
<br>

---


<span style="font-size:80%"><a name="footnote_1">1</a>. <https://aws.amazon.com/ko/what-is/restful-api/></span>   
<span style="font-size:80%"><a name="footnote_2">2</a>. <https://developer.mozilla.org/ko/docs/Web/HTTP/Methods/></span>  
<span style="font-size:80%"><a name="footnote_3">3</a>. <https://developer.mozilla.org/ko/docs/Web/HTTP/Status/></span>  