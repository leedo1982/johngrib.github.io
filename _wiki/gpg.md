---
layout  : wiki
title   : GnuPG 사용법
summary : GnuPG, the GNU Privacy Guard
date    : 2018-09-10 14:24:06 +0900
updated : 2018-09-13 08:57:14 +0900
tags    : bash, 암호화, GNU
toc     : true
public  : true
parent  : command-line
latex   : false
---
* TOC
{:toc}

# GPG

GnuPG : GNU Privacy Guard. GPG 라고도 한다.

# 꼭 읽으세요

* 이 웹 페이지의 글은 개인의 의견을 개진한 것입니다.
    * 만일 이 글을 읽는 여러분께서 암호나 비밀 통신과 관련된 문제에 관한 조언이 필요할 경우, **암호학 전문가** 또는 **변호사**를 찾아 상담받기를 권장합니다.
    * 저는 암호학 전문가나 변호사가 아닙니다.
* 이 글은 자신을 위해 gpg 도구의 사용 방법을 메모해 둔 것입니다.
* 귀하가 이 글을 읽고 시도한 모든 일에 대해 저는 책임을 지지 않습니다.

---

* 암호화 알고리즘은 만능이 아닙니다. 언젠가는 깨질 수 있습니다.
    * 컴퓨터 연산 능력의 발전에 의해 한때 강력했던 암호가 brute force 앞에 무의미해질 수 있습니다.
    * 암호가 우연히 깨지는 경우도 있습니다.
    * 당분간만 비밀로 유지할 필요가 있는 메시지만 암호화하세요.
    * 자신의 비밀 키를 공공 장소나 인터넷 카페, 도서관 같은 곳에서 복사하거나 저장하지 마세요.
        * 가족이나 친구와 함께 사용하는 컴퓨터에 비밀 키를 보관하지 마세요.
    * 키를 주기적으로 교체하세요.
    * 전화번호나 생일, 집 주소 같은 자신의 신상 정보를 암호 생성이나 관리에 사용하지 마세요.
        * 추측하기 쉬운 문자열을 비밀 키의 패스워드로 사용하지 마세요.
        * 주민등록 번호, 계좌번호, 전화번호, 집 주소 동 호수, 우편번호, 연속/반복된 숫자, 사전에 있는 단어를 사용하지 마세요.
* **사람의 건강이나 생명, 재산, 정치적 문제 등이 걸린 중요한 메시지는 컴퓨터에 보관하지 마세요.**
    * 누군가 당신의 컴퓨터에 침투해 암호화된 메시지를 가져가 복호화를 시도할 수 있습니다.
    * 낡은 컴퓨터를 판매하거나 양도하거나 버릴 때에는 반드시 키와 암호화된 메시지를 삭제하고 하드디스크를 포맷하세요.
    * **중요한 비밀 키는 스마트카드나 USB 등에 옮긴 다음, 나만 열어볼 수 있는 물리적 금고에 넣어두세요.**
* 다른 사람의 비밀 키를 우연히 획득했다면, 가능한 한 빨리 비밀 키의 주인에게 제보하세요.
    * 타인의 비밀 키를 복제하지 마세요.
    * 타인의 비밀 키를 사용해 무언가를 암호화하거나 복호화하지 마세요.
    * 타인의 비밀 키나 공개 키를 분석하는 시도를 하지 마세요.
* 비밀 키를 타인에게 복사해주지 마세요.
    * 가족이나 연인에게도 복사해주지 마세요.
* export한 비밀 키를 모니터로 볼 때, 주위를 살펴 cctv나 비밀 카메라 또는 창문이 없는지 확인하세요.


# install

## Ubuntu

```sh
$ sudo apt-get install gnupg
```

## Mac

```sh
$ brew install gnupg
```

# 버전 확인

* `--version`


# 새로운 키 생성

* `--gen-key`
* `--full-generate-key` : MacOS에서는 이 옵션을 쓰지 않으면 몇 가지 질문이 생략되고 기본 값으로 설정된다.

```sh
$ gpg --gen-key
```

그러면 몇 가지 문답을 거쳐 키를 생성하게 된다.

* 어떤 암호화 알고리즘을 사용할 것인지?
* 키의 유효기간은 어떻게 할 것인지?
    * 주의: 키 유효 기한은 **1년 이하**를 권장.
    * 불편하더라도 몇 달 주기로 유효 기한을 연장하거나 새로 생성한 키로 교체하는 것이 좋다.
    * 무기한인 키를 키 서버에 업로드했는데 키를 분실하면 잘못된 키가 영원히 유지될 수도 있다.
* 사용자의 Real name은?
* 사용자의 email 주소는?

문답을 완료하고 랜덤 바이트가 생성되면, 다음과 같이 공개 키와 비밀키가 생성되었음을 알려준다.

```
public and secret key created and signed.

pub   rsa4096 2018-09-10 [SC]
      4AB3AA77
uid                      testuser <testuser@___.com>
sub   rsa2048 2018-09-10 [E]
```

## 키의 구성

* 하나의 키는 관례적으로 두 개의 서브 키로 구성된다.
    * 키(primary key) : 서명할 때 쓴다.
    * 서브 키(sub key) : 암호화/복호화할 때 쓴다.

따라서 `--gen-key`로 키를 생성하면 다음과 같이 4개의 키가 생성되는 셈이다.

* 공개 primary key
* 공개 sub key
* 비밀 primary key
* 비밀 sub key


# 키 목록 보기

* `--list-keys`, `-k` : 공개 키 목록을 본다. `--list-public-keys` 옵션과 똑같다.
* `--list-secret-keys`, `-K` : 비밀 키 목록을 본다.

```sh
$ gpg -k
----------------------------------
pub   rsa4096 2018-09-10 [SC] [expires: 2019-03-11]
      78BC79BB
uid           [ultimate] kim <kim@gpgtest.com>
sub   rsa4096 2018-09-10 [E]

pub   rsa2048 2018-09-11 [SC] [revoked: 2018-09-11]
      90F92F0
uid           [ revoked] lee <lee@gpgtest.com>

pub   rsa2048 2018-03-14 [SC]
      C475C477
uid           [ unknown] park <park@gpgtest.com>
sub   rsa2048 2018-03-14 [E]
```

* `pub`: 공개 키.
* `uid`: user id.
* `sub`: sub key.
* `[SC]`: 해당 키가 Signing 과 Certificate 용도로 사용된다는 의미.
* `[E]`: 해당 키가 Encryption 용도로 사용된다는 의미.
* `[expires: yyyy-mm-dd]`: 유효 기한.
* TRUST VALUES
    * ultimate, full, marginal, never, undefined, expired, unknown 순으로 신뢰도가 낮아진다.
    * `[ultimate]`: 완전히 신뢰할 수 있는 키.
    * `[revoked]`: 해지된 키.
    * `[unknown]`: 신뢰할 수 없는, 모르는 사람의 키.

한편, `--list-secret-keys`는 출력 결과가 **약간** 다르다.

```sh
$ gpg --list-secret-keys
----------------------------------
sec   rsa4096 2018-09-10 [SC] [expires: 2019-03-11]
      78BC79BB
uid           [ultimate] kim <kim@gpgtest.com>
ssb   rsa4096 2018-09-10 [E]

sec   rsa2048 2018-09-11 [SC] [revoked: 2018-09-11]
      90F92F0
uid           [ revoked] lee <lee@gpgtest.com>

sec   rsa2048 2018-03-14 [SC]
      C475C477
uid           [ unknown] park <park@gpgtest.com>
ssb   rsa2048 2018-03-14 [E]
```

* `sec`: 비밀 키.
* `ssb`: secret sub key.


# 공개 키 export

* 나의 **공개 키**로 암호화한 메시지는 나의 **비밀 키**로 복호화할 수 있다.
* 따라서 나의 공개 키를 다른 사람에게 주면, 다른 사람은 내 공개 키를 이용해 내게 보낼 메시지를 암호화할 수 있다.

> 주의: 절대 **비밀 키**가 다른 사람에게 넘어가지 않도록 조심하자.  
인터넷에 공개하거나 다른 사람에게 복사해줘도 되는 것은 **공개 키** 뿐이다.

testuser가 자신의 **공개 키**를 친구에게 주고, 서로 비밀 메시지를 주고받으려 한다고 하자.

다음과 같이 `--armor`, `--export 사용자아이디`를 입력하면 해당 사용자의 공개 키를 얻을 수 있다.

```sh
$ gpg --armor --export testuser
```

`--armor` 옵션은 `-a`로 축약할 수 있다.

```sh
$ gpg -a --export testuser
```

* `--armor` 옵션을 넣어주면 출력 결과가 ASCII 문자로 변환되어 나온다(ASCII armored output).
    * `--armor` 옵션을 넣어주지 않으면 binary OpenPGP format으로 출력된다.
    * binary format은 눈으로 봐도 알 수 없는 문자열이 잔뜩이니 `--armor` 옵션을 사용하자.

다음과 같이 공개 키를 눈으로 확인할 수 있다.

```sh
$ gpg -a --export testuser
-----BEGIN PGP PUBLIC KEY BLOCK-----

mQENBFuWILQBCACXfJbKayPzNksIq06PpHb/jy/5C1pXFJY/jvQY3r8ccgt018k5
RFdyv6qh4zAM8RlBWQMTNNmrFX7P938hiAZ/R+cJh+iwHH3Z8z4MPYoemhvluuI/
ny1ouXGmYFkHTozh2Ev9aoS2zJ1U0VMGpMKyfXOocIFxEGYiP99uKaszVD9KgeZw
HhpM/rsX4hTDAPPvrVAGD4Nu9ntbbyII6UDpKoAf4bwqRUJ1qi8cIBSJQyC4LS5R
lhWe+iGGxNyv0y7v/dPW6NUxU/B8O9qSPhXNA17AdAncwZoFlBdXUHKskkhSAjkm
Iw3hQZgvO3DsuJWFHDUkCFuBEb99Udnf/3aFABEBAAG0KHRlc3R1c2VyIDxqb2hu
Z3JpYjgyK3Rlc3R1c2VyQGdtYWlsLmNvbT6JAVQEEwEIAD4WIQSM3cREfw683P7w
ndHUAoZI0Pf+cwUCW5YgtAIbAwUJA8JnAAULCQgHAgYVCgkICwIEFgIDAQIeAQIX
gAAKCRDUAoZI0Pf+cxh2B/9pR8HzVLc+CXLPlRzsji3euxKyFWO7m1qzUceC7tg2
hXgaufiBLDph19X6QKqZFRzcLLWlSyp9cAdfXwnMvgeTtqDGUb/6w4MbVgGO3V8G
d2inubF86PkRAxXuroaQguUj2/d75Xu3g4b3/HmfwytFWj0N5Yz86gn0xFUHP2BD
5hU4AK84tfzn9hrUmbAyYNsdR5mC5k00Z4sArI70gC6e4N4IdOewzADm+ysO3NVk
(생략)
ufIMNQazrGeSnOo4L7mGKpYIGoyUVPcY7i9Slzo6YYxnuQENBFuWILQBCAC8zneN
SaiChy6i0WeHW//jWv1EmM5qxQnXAEncOh7tJYJyCCn9XHxkyzqXYd49y7X+ZM/J
VtpyxApWEaEKP6ZF2T/nYCO1zbm3qXO/5kEDBmuIctLv+3CMfWM4xxZBk8l2F/5y
dxWDBPBAe2WM6bpKJPY1V4GSSHZ2pqm35nlZWIsVFkX7hR9HjAh/wTJHfDV9a+fy
KmPuV1f/XVMZ3WEV4nufB09/d1p8enfyM4h5SgRtdD/5Q8m+louMowmEfVBC5Zh9
0RcQa1w4vJ2uEEg7sLhLnEKEQJfc5ZdT/3jP46IOCHRNNGOWGC11h1h+xyknTWaF
XIGnMQNtpsW1ZZ5VABEBAAGJATwEGAEIACYWIQSM3cREfw683P7wndHUAoZI0Pf+
cwUCW5YgtAIbDAUJA8JnAAAKCRDUAoZI0Pf+c41iB/9FSFwkoXRrxrB15Le7xdFR
AK53RgbYG3tlWLwv2ZbEbMvNB25KoEXJzfIY+dtY1RF3BLKNNuakl3+tB1S6ErO7
n5ExA+1yIsP6gKnvltvYvEzWYcC3gdpSDwnDXzipZ6nNaUH6jO7vlU6Ibz6xaLRZ
G47kEJUICkIPKmn8pla0phWhzh+C1NwGr7y6tHwfwc7tmJlWXqQv4fImj7xasRCs
f//mhn4NZRemZPbjhxbj8QMrKOMHrg4mjLpEqmQhzMnOt5lHGuMaUrg0F5AKxLos
9Z+MxrjADsqOvaT5rgZIsDzRWTt8fK7x/M6YjreIaIh4kPSeKCqS9vp+Ix7lrLb+
=3hoW
-----END PGP PUBLIC KEY BLOCK-----
```

친구에게 보내려면 다음과 같이 파일로 저장한 다음 보내도록 하자.

```sh
$ gpg --armor --export testuser > testuser.asc
```

* 친구는 보내고 싶은 메시지나 파일을 testuser의 공개 키로 암호화하여 보내줄 것이다.
* 친구가 보내준 암호화된 파일을, testuser는 자신의 비밀 키로 복호할 수 있다.

testuser의 공개 키로 암호화한 것은 testuser의 비밀 키로만 복호화할 수 있으므로
testuser가 비밀 키가 유출되지 않도록 잘 관리한다면 충분히 안전하게 메시지를 주고 받을 수 있게 된 것이다.

## 키 서버로 전송하기

주의 : 키 서버로 자신의 공개 키를 올리기 전에 다음을 읽고 충분히 고민할 것.

* 키 서버에 공개 키를 한 번 올리면 절대 지울 수 없습니다. (게다가 키 서버들끼리 정보를 공유.)
* 비밀 키를 분실하거나 유출해도 지울 수 없습니다.
    * Revocation Certificate 만을 키 서버에 추가로 올려, 해당 공개키를 사용하지 말 것을 알릴 수 있을 뿐입니다.
* 공개 키를 올릴 때 자신의 Real Name과 email이 전 세계에 알려지므로, 신상정보를 스스로 퍼뜨리는 행위일 수 있습니다.
    * 자신의 아이디나 이메일에 들어간 단어가 다른 사람을 자극하거나 공격하지는 않는지 숙고하세요.
    * 자신의 이름과 이메일을 사용해 올바르지 못하거나 불법적인 행동을 하지 마세요.

공개 키는 친구에게 보내는 것 뿐 아니라 다음과 같이 키 서버로 전송해 세상에 퍼뜨릴 수도 있다.

[pgp.key-server.io](https://pgp.key-server.io/ )를 선택해 전송해 보았다.

```sh
$ gpg --keyserver pgp.key-server.io --send-key 키아이디
gpg: sending key 21831..... to hkp://pgp.key-server.io
```

이제 지구상의 누군가가 나에게 암호화된 메시지를 보내고 싶다면
키 서버에서 내 아이디나 이메일을 검색해 공개 키를 얻어, 메시지를 암호화해 나한테 보내줄 수 있다.


## 해지 인증서(Revocation Certificate) 만들기

* 키를 분실했거나 의도치 않게 유출되었다면, 해지 인증서를 만들어 키 서버에 올리는 것이 좋다.
* 키를 처음 생성했을 때 해지 인증서를 함께 만들어 비밀 키와 함께 오프라인 저장장치에 보관하는 것을 권장.

해지 인증서는 `--gen-revoke` 옵션으로 만들 수 있다.

```sh
$ gpg --output revoke.asc --gen-revoke 키아이디
```

해지 인증서를 `--import` 한 다음, 키 서버로 전송하면 된다.

이후 키 서버로 들어가 키를 검색해 보면 `revoke`되었다는 표시가 뜨는 것을 확인할 수 있다.


# 공개 키 import

한편, testuser의 친구는 testuser의 공개 키를 가져와 자신의 컴퓨터에 등록하기로 하였다.

공개 키 파일을 준비한 다음, 다음과 같이 `--import` 옵션을 사용하면 된다.

```sh
$ gpg --import testuser.asc
gpg: key D0F7FE73: public key "testuser <testuser@___.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
```

`--list-public-keys`로 등록된 키 목록을 보면 testuser의 공개 키가 등록되어 있음을 알 수 있다.

```sh
$ gpg --list-public-keys
~/.gnupg/pubring.gpg
--------------------------------
pub   2048R/D0F7FE73 2018-09-10 [expires: 2020-09-09]
uid                  testuser <testuser@___.com>
sub   2048R/8B52E542 2018-09-10 [expires: 2020-09-09]
```

# 암호화

이제 친구가 testuser에게 암호화된 메시지를 보낸다고 하자.

암호화할 때에는 `--encrypt` 옵션을 주면 된다.

```sh
$ echo 'hello testuser!' | gpg --encrypt --armor --recipient testuser
```

또는 다음과 같이 `--encrypt`와 `--armor`를 축약한 `-ea` 옵션을 사용해도 된다.

```sh
$ echo 'hello testuser!' | gpg -ea --recipient testuser

-----BEGIN PGP MESSAGE-----
Version: GnuPG v1

hQEMA5DmoKGLUuVCAQgAl4BaAa6WziQpnyPO9uiGCrkCJTMFgJMPJ7AcX6IXa5kS
e4ux32gieoBIkeg30LgTpwprPoBCS3l4h8nWE5X3GpPUC+gkde2STyP1FSbCon/C
4/I/Zo6p2/3UDAtXyzKZAELG/IXgyX+sut8mfinmdFRoT6xB77GcOITDtrX0DAoJ
(생략)
AhAhb8ADIDA21XLcZ0z7vVFEuhvPheWgWN0RouatRnI7t7zaiCm3t5OQtDmoMhLF
ssafE4at7QwoCHS6tUw+hI3nvGYyFMzJdTmMnAxFbNJLAbdAekveojWeDahnwTDm
l/9XIBCX5wt7K4/gNGEqqC9jDNqgmWDdtFUM/uPjPsHZajZG3uOIbfsxTQT9X7l9
B65GoPB9OoB/A7tk
=/qH6
-----END PGP MESSAGE-----
```

파일로 저장하고 싶다면 다음과 같이 하면 된다.

```sh
$ echo 'hello testuser!' | gpg -ea --recipient testuser > to_testuser.txt
```

또는 `--output 파일명` 옵션을 사용해도 된다. `-o`로도 쓸 수 있다.

```sh
$ echo 'hello testuser!' | gpg -o to_testuser.txt -ea --recipient testuser
```

이제 친구는 이 파일을 testuser에게 이메일이나 메신저로 전송하면 된다.

testuser가 갖고 있는 비밀 키가 아니면 복호화가 불가능한(적어도 RSA 2048이 깨지기 전까지는) 안전한 메시지이므로 안심하고 보낼 수 있다.


# 복호화

testuser는 친구가 보낸 파일을 받아 복호화하려 한다.

복호화는 아주 쉽다. `--decrypt` 옵션을 주면 된다.

```sh
$ gpg --decrypt testmsg.txt 
gpg: encrypted with 2048-bit RSA key, ID 90E6A0A18B52E542, created 2018-09-10
      "testuser <testuser@___.com>"
hello testuser!
```

친구가 보낸 메시지는 바로 `hello testuser!` 였다.

결과를 파일로 저장하고 싶다면 `--output 파일명` 옵션을 사용한다. `-o`로도 쓸 수 있다.

```sh
$ gpg -o decrypted.txt --decrypt testmsg.txt
$ cat decrypted.txt

hello testuser!
```

# 비밀 키 import

비밀 키를 등록하는 방법은 간단하다 `--import` 옵션을 사용하면 된다.

```sh
$ gpg --import 비밀키파일
```

한편 비밀 키의 비밀번호는 다음과 같이 `--edit-key` 옵션으로 변경할 수 있다.

그러면 `gpg>`라는 프롬프트가 나타나는데 `passwd`를 입력하면 기존 비밀번호/신규 비밀번호를 물어본다.

이후 `save`를 입력하면 변경 절차가 완료된다.

```sh
$ gpg --edit-key 비밀키아이디
gpg> passwd
gpg> save
```

# 유효 기한 관리

* 키 유효 기한은 1년 이하를 권장.
    * 불편하더라도 몇 달 주기로 유효 기한을 연장하거나 새로 생성한 키로 교체하는 것이 좋다.
* 만료가 없는 키를 생성했다 하더라도 다음과 같이 만료일을 수정할 수 있다.

```sh
$ gpg --edit-key 키아이디
```

이후 아래와 같이 `expire`를 입력하고, 기간을 설정해주고(`6m`: 6개월) 저장한 다음 종료하면 된다.


```sh
$ gpg --edit-key 키아이디

gpg> expire

Changing expiration time for the primary key.
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 6m
Key expires at 월  3/11 12:22:33 2019 KST
Is this correct? (y/N) y

sec  rsa4096/....
     created: 2018-09-10  expires: 2019-03-11  usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/....
     created: 2018-09-10  expires: never       usage: E   
[ultimate] (1). testuser <testuser@___.com>

gpg> save
```

# Links

* [gnupg.org](https://www.gnupg.org/index.html )
* [Getting Started with Gnu Privacy Guard](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Step_by_Step_Guide/ch-gnupg.html )
* [RSA(cryptosystem) (wikipedia)](https://en.wikipedia.org/wiki/RSA_(cryptosystem) )
    * [RSA 암호(wikipedia)](https://ko.wikipedia.org/wiki/RSA_%EC%95%94%ED%98%B8 )
* [pgp.key-server.io](https://pgp.key-server.io/ )
* [The comp.security.pgp FAQ (www.pgp.net)](http://www.pgp.net/pgpnet/pgp-faq/pgp-faq.html )
    * [Security Questions (www.pgp.net)](http://www.pgp.net/pgpnet/pgp-faq/pgp-faq-security-questions.html )
* [GnuPG 리눅스에서 안전하게 통신하기](http://www.linuxlab.co.kr/docs/01-01-3.htm )

* [OpenPGP Best Practices (riseup.net)](https://riseup.net/en/security/message-security/openpgp/gpg-best-practices )
* [CREATING THE PERFECT GPG KEYPAIR (alexcabal.com)](https://alexcabal.com/creating-the-perfect-gpg-keypair/ )
* [RFC 4880 - OpenPGP Message Format 5.5](https://tools.ietf.org/html/rfc4880#section-5.5 )
* <https://wiki.debian.org/Subkeys >

