---
layout: post
title:  "semantic analysis"
date:   2019-11-27
image:
comments: true
---
Semantic Analysis
================================
소스 코드(문자열) -> 어휘분석 -> token stream -> 구문분석 -> parser tree ,AST, symbol tables -> 의미분석 -> 중간코드 생성 -> Intermediate code (3-addr code, stack machine code)   

scope vs life time

scope -> 프로그램 자체에서의 범위
life time -> Runtime 중 에서의 생명주기

C 기준 extern 붙이면 다른 실행파일에서도 확인가능 = Scope 확장

class 내에서 private 붙이면 다른 클래스에서 보이지 않음 = Scope 축소

박스 안에서는 밖이 보이지만 밖에선 안이 안보임

Kind - type 보다는 상위개념으로 쓰임

lexical 마다 하나의 symbol table(값이 아닌 타입만 가지고 있음) = 하나의 블록마다 생성

nested 된 symbol table 을 거슬러올라가며 찾다가 없으면 오류  

블럭이 끝나면서 symbol table이 불필요해질때 스택에서 pop한다. - runtime 때 필요한 것만 스택에서 저장해서 사용함.

hash 가 변함 - runtime 동안 그당시에 사용되는 변수 등이 저장됨 -식별자가 같은 변수들 등이 존재 하므로 