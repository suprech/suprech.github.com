---
layout: post
title:  "python 4"
date:   2018-02-26
categories: python_web_programming
tags: python flask web sqlalchemy
comments: true
---
## jwt를 이용한 인증 구현

[jwt 공식홈페이지](https://jwt.io/)

[pyjwt 도큐먼트](https://pyjwt.readthedocs.io/en/latest/usage.html#encoding-decoding-tokens-with-hs256)


jwt는 HTTP request의 header에 넣어서 보낸다!

HTTP의 구조를 보고싶으면, 크롬 개발자도구 -> 상단 메뉴의 Network에서 확인가능.

![chrome_network_img]({{https://suprech.github.io/}}/images/chrome_network.png)


## CORS란 무엇인가?

[참고 url](https://developer.mozilla.org/ko/docs/Web/HTTP/Access_control_CORS)


Cross-Origin Resource Sharing 표준은 웹 브라우저가 사용하는 정보를 읽을 수 있도록 허가된 출처 집합를 서버에게 알려주도록 허용하는 HTTP 헤더를 추가함으로써 동작합니다. 추가적으로, 사용자 데이터 상에서 부수 효과를 일으킬 수 있는 HTTP 요청 메서드에 대해(특히, GET 이외의 HTTP 메서드들 혹은 어떤 MIME 타입을 사용하는 POST 사용에 대해), 스펙은 브라우저가 요청을 "preflight"(사전 전달)하도록 강제하는데, 이는 HTTP OPTION 요청 메서드를 이용해 서버로부터 지원 중인 메서드들을 내려 받은 뒤, 서버에서 "approval"(승인) 시에 실제 HTTP 요청 메서드를 이용해 실제 요청을 전송하는 것을 말합니다. 서버들은 또한 클라이언트에게 (Cookie와 HTTP Authentication 데이터를 포함하는) "credentials"가 요청과 함께 전송되어야 하는지를 알려줄 수도 있습니다.

계속되는 섹션에서는 사용 중인 HTTP 헤더의 상세 내용뿐만 아니라, 시나리오에 대해서도 논하고 있습니다.