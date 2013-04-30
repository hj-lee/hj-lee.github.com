---
layout: post
title: "Emacs에서 한글 맞춤법 검사기 사용하기 업데이트"
description: ""
category: hunspell-ko
tags: [emacs, hunspell, 맞춤법 검사기]
date: 2013-04-30
published: true
---
{% include JB/setup %}

[char-position.patch]: /assets/codes/hunspell-patch/char-position.patch
[input-conv-putdic.patch]: /assets/codes/hunspell-patch/input-conv-putdic.patch
[old-blog]: {% post_url 2009-04-29-emacs-korean-spellchecker %}

[my-hunspell]: https://github.com/hj-lee/hunspell.git

[hunspell-init.el]: https://github.com/hj-lee/hjlee-emacs-init/raw/master/hjlee-init-hunspell-ko.el

[spellchecker-ko]: http://code.google.com/p/spellcheck-ko/ "한글 맞춤법 검사 프로젝트 홈"

### 개선된 점

이전에 hunspell 기반으로 한글 맞춤법 검사를 가능하게 해주는 프로젝트가 있다는 것을 알고 emacs ispell.el 에서 이것을 사용할 수 있게 하느라 애썼던 적이 있습니다.
([관련 포스트][old-blog])

당시에는 hunspell 과 emacs 간의 통신에 문제가 있어서 enchant 를 대신 사용했었습니다. 그런데, 최근에 다시 사용하면서 내용에 따라 간혹 동작을 멈추는 일들이 발생하게 되었습니다.

이 참에 좀 더 근본적으로 문제를 해결하고자 맘 먹고, enchant 에 의존하는 대신 hunspell 을 수정해 버렸습니다. 더불어 이전에 동작하지 않았던 개인 사전 같은 기능도 동작하도록 작업을 하였습니다. `.emacs` 에 넣을 내용도 조금 수정하였고요.

### 작업

우선 hunspell 용 한국어 사전은 깔려 있어야 합니다. 요즘, 웬만한 리눅스 배포판에는 hunspell-ko 가 패키징 되어 있을 것입니다. 아니면 [맞춤법 검사 프로젝트 페이지][spellchecker-ko]에서 받을 수 있습니다.

Hunspell 은 두 가지가 변경되었는데, 하나는 원래의 문제였던 문자 대신 바이트를 세던 문제를 해결한 것이고, 또 하나는 개인 사전이나 세션 단어 기능이 동작하도록 수정한 것입니다.

Hunspell 1.3.2 기반 수정된 소스는 [github][my-hunspell]에서 받으실 수 있습니다.(두 개의 patch 로도 만들어 놓았습니다. [char-position.patch][], [input-conv-putdic.patch][])

{% highlight bash %}
git clone https://github.com/hj-lee/hunspell.git
cd hunspell
./configure --prefix=<프리픽스>
make
make install
{% endhighlight %}

이미 깔린 hunspell 이 있다면, 그것 대신 새로 설치한 hunspell 이 실행되도록 해주어야 합니다. (`PATH`를 변경 한다든가, 깔려 있는 hunspell 을 지운다든가...)

이제 `.emacs` 파일에 아래 코드를 추가하면, 작업 완료. ([hunspell-init.el][])

{% highlight cl %}
(setq ispell-program-name "hunspell")
(setq ispell-really-hunspell t)

;; 사전 목록에 한국어("korean") 추가
(if (>= emacs-major-version 23)
    (setq ispell-local-dictionary-alist
          '("korean"
            "[가-힣]"
            "[^가-힣]"
            "[0-9a-zA-Z]" nil
            ("-d" "ko_KR")
            nil utf-8))
    (setq  ispell-local-dictionary-alist
           '("korean"
             "[가-힝]"
             "[^가-힝]"
             "[0-9a-zA-Z)]" nil
             ("-d" "ko_KR")
             nil utf-8)))

;; 한국어를 기본 사전으로 지정
(setq ispell-dictionary "korean")
{% endhighlight %}

이전과 달라진 부분은 `OTHERCHARS` 부분을 숫자와 영문자로 바꾼 것입니다.
이렇게 하면 "3을", "James를" 과 같은 표현이 나왔을 때에도 "를", "을" 등을 각각의 단어로 인식하지 않고 조사로 인식하게 됩니다. (불행하게도 flyspell 모드에서는 잘 동작하지 않습니다.)

### 개인 사전(Private Dictionary)에 대하여

개인 사전은 홈 디렉터리에 `.hunspell_ko_KR` 과 같이 언어 별로 생기는데, 여기 있는 단어들에도 '단어/변형 규칙' 과 같은 형태로 단어 변형 규칙을 명시해 줄 수 있습니다.
동사나 형용사는 규칙이 매우 다양해서 규칙을 적어 주기가 어렵고, 명사의 경우 현재의 hunspell-ko 기준으로 규칙 3(단위 명사인 경우 규칙 8)에 해당합니다. 예를 들어 `김만중/3` 이라는 내용을 사전에 추가하면 "김만중이", "김만중은"과 같이 조사가 붙은 단어들을 더 추가할 필요가 없어집니다. ("홍길동"이 이미 사전에 있는 바람에 그냥 생각나는 사람을 예로 들게 되었네요.)

### 변경된 부분들에 대해서

hunspell-ko 는 한글 자모 코드로 사전과 규칙 데이터가 정의 되어 있습니다. 
그래서 아주 초기에는 한글 사전을 사용하는 경우, 입력을 이 자모 코드로 변환하는 코드가 필요했었습니다. 그래서 당시에는 hunspell 실행 파일도 패치 된 버전을 사용해야 했었습니다. 나중에 입력/출력 변환 테이블을 사전 데이터에 사용할 수 있게 되면서 굳이 패치 된 hunspell 을 사용할 필요가 없게 되었습니다.

제가 작업을 시작할 때 이 사실을 몰라서, 류창우님이 관리하던 hunspell 소스를 fork 해와서 작업을 시작해 버렸었습니다. 나중에 알고 나서 1.3.2 기준으로 작업을 하긴 했지만, 그 때문에 히스토리에는 흔적이 고스란히 남아있습니다.

#### 문자 위치 [file][char-position.patch]

ispell 호환 모드, 혹은 기본 출력 모드 에서 바이트 위치 대신 문자 위치를 표시하도록 변경한 부분입니다.

이것은 hunspell 라이브러리를 직접 사용하는 프로그램들과는 무관하고,
pipe를 사용해서 통신하는 다른 프로그램들과는 문제가 될 수도 있습니다.
다만, 제가 아는 한에서는 emacs 가 이렇게 동작하는 대표 프로그램이고, 이 패치는 emacs 가 hunspell 과 잘 동작하게 해줍니다.

#### 입력 변환 [file][input-conv-putdic.patch]

이 패치는 `.aff` 파일에 `ICONV` 항목이 있는 경우 개인 사전이나 세션 중의 단어를 추가할 때 무조건 `ICONV` 변환을 거치도록 합니다. 

`ICONV` 는 사전 파일에서 입력 전환 테이블 같은 것입니다. hunspell-ko 에서는 한글 자모 코드로 변환시켜줍니다.

국어의 경우 문제가 해결 되는데, `ICONV`를 사용하는 다른 언어들은, 문제가 될 지도 모르겠네요.

<!--  LocalWords:  를 을
 -->
