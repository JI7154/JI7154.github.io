---
layout: post
title:  "IR_generation_translation"
date:   2019-09-17
image:
comments: true
---
IR_generation
====================================
t1,t2,... : 컴파일러가 임시 생성한 변수
while 등 어셈블리랑 비슷하게 쪼개져서 진행
3-address code - 컴파일러가 사용하는 표현식 중 하나
[[e]] : e는 표현식 -> 표현식을 3-address code 형태로 풀어나감
e:가 변수나 상수일 때는 바로 벗겨도 됨
[] : 주소값 접근
Lend : 루프를 탈출할때, jump 와 반대

Storage Process
------------------------------
Registers
- 빠른 접근
- 일반 프로그래머에게는 보이지 않음

Memory
- 상대적으로 느린 접근,간접접근

컴파일러가 스토리지를 사용하는 방법
1.
Standard approach
- Global/static 변수 등은 -> 메모리에
- Locals
  - structs, array,  scalar 변수 -> 메모리에 매핑
  - '&' 연산자가 없 는 scalar 변수 'Virtual' register : 메모리로 다시 쫒거남

2.
All memory approach
- 모든 변수를 memory에 이 중 가능한 것은 register로 조정

Memory의 4대 영역

- Code space : 명령을 저장하는 공간(read-only면 성능이 좋음)
- static(or Global) - 프로그램과 life time을 함께 하는 변수들의 집합
- Stack - local 변수들 (블럭 life time)
- heap - System call(malloc,new) 에 의해 동적으로 allocate 되는 공간

변수 초기화 하면 메모리에 저장 되어 있음.

Run-Time stack

- 힙은 커지는 방향으로 자라고, 스택은 아래로 자라남 (메모리를 효율적으로 쓰기 위해 ) 그 사이에 shared library가 존재 

- local 변수들을 스택에 넣을때 임시변수도 생성됨

Run-Time Stack 변화도 컴파일러가 코드적으로 생성함


