---
layout  : wiki
title   : 메뉴의 ... (점 3개)
summary : 명령어가 수행되기 전에 추가적인 정보가 필요한 항목을 표현한다
date    : 2020-04-24 21:58:40 +0900
updated : 2020-04-26 14:20:02 +0900
tag     : 
toc     : true
public  : true
parent  : index
latex   : false
---
* TOC
{:toc}

## 메뉴의 ... 에 대하여

> 윈도우 사용자 인터페이스 설계 사양 및 지침서의 '생략부호'라는 절에 이에 대한 규칙이 있다.  
전체 내용을 파악하려면 이 지침서를 읽어야겠지만, 요약하면 다음과 같다.
명령어가 수행되기 전에 추가적인 정보를 필요로 할 경우, 생략부호를 사용한다.
정보(About)나 속성(Properties)처럼 대화상자가 명령어 자체인 경우도 있는데, 이들이 비록 대화상자를 표시하기는 하지만 이 대화상자는 결과이다.
반면에, 인쇄(Print)라는 명령어는 결과에 앞서서 추가적인 정보를 수집하는 대화 상자를 표시한다.
[^raymond-12]

## 확인해보면

윈도우 뿐만이 아니라 MacOS 에서도 같은 원칙이 통용되는 것 같다.

다음은 MacOS의 터미널이다.

![]( /post-img/three-dots-in-menu/terminal.png )

`셸` 탭을 살펴보면 `...`이 있는 메뉴가 6개 있음을 알 수 있다.

- `새로운 명령어...`
- `새로운 원격 연결...`
- `가져오기...`
- `설정 내보내기...`
- `텍스트를 다음으로 내보내기...`
- `프린트...`

모두 해당 메뉴를 선택하면 무언가를 곧바로 실행하지 않고, **무언가를 사용자에게 선택하게 한다**.

파일을 선택하거나, 옵션을 고르는 등의 활동을 하면 그 다음에 할 일을 수행하는 방식이다.



## 참고문헌

- 레이몬드 첸의 윈도우 개발 282 스토리 / 레이몬드 첸 저 / 손광수 역 / ITC / 초판 1쇄 2007년 09월 10일 / 원제 : The Old New Thing: Practical Development Throughout the Evolution of Windows

## 주석

[^raymond-12]: 윈도우 개발 282 스토리. 1장. 12쪽.