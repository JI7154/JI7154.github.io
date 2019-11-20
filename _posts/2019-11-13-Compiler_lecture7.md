---
layout: post
title:  "IR_generation"
date:   2019-09-17
image:
comments: true
---
IR_generation
====================================
N-tuple(Quadruple) - 연산자,피연산자1,피연산자2,결과값
--------------------------
- 임시 변수 다루는 것이 단점  
  
3-주소코드  
a = [b]  
[a] = b :b의 주소를 가져와 a에 저장  

3-주소코드의 예시 

1. gcc의 gimple
gcc는 중간 코드가 3개
- Generic : 여러 언어를 받아 언어의 특성을 바꿈
- RTL : backend의 머신코드 생성
- GENERIC,RTL : 트리 형태
- Gimple : 3-주소 코드 

2. LLVM BIT 코드
- 언어와 machine에 독립적
- 컴파일러의 이름: Clang
- 현재 가장빠른 컴파일러
- @ :global 변수 %: local 변수
- alloca i32 : malloc 처럼 메모리 assign, 그 후 int 형태이므로 align 4
- 임시변수는 로컬이므로 %이고 보통 숫자를 붙임
- 임시변수는 거의 1회용(load,store 하고 버림) : static Single Assignment - 변수가 굉장히 많아지지만 최적화하기 좋아짐  

Stack machine code
-----------------------------
1. jvm Bytecode  

왼쪽 숫자는 주소값
putfield 이후에는 스택이 비워지므로 aload_1 (this) 를 다시 해줘야함  

new 했을때 생성된 객체는 heap에 생성, 따라서 stack에는 OID만 가지고 있음
astore_1 : 0 번에는 args 가 들어있음.  

2. MS사에서 사용하는 CIL(common Intermediate Language)
- VES : Binary로 바꿔줌 (Jit complier)  

3. 가상 기계 코드 : P-Code (pascal), U-Code
- p-기계 = 특수레지스터 4개 + 기억공간
- 구조를 알고 사용하는 것이 특징  


Tree 구조 코드
--------------------------------------
AST나 HIR과 비슷한 면도 있으나 보다 자세함 - 메모리 로드 등이 명시적으로 표현됨  

1. RTL
- 트리를 괄호 형태로 표시  


Lectur8
=================================================
Intermediate Representation Translation
----------------------------------------
HIR -> LIR

[[e]] : expression을 풀어야하나 간단하게 표기하기 위해 사용
