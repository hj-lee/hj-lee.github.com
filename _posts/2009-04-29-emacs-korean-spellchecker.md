---
layout: post
title: "Emacs에서 한글 맞춤법 검사기 사용하기"
description: ""
category: hunspell-ko
tags: [emacs, hunspell, 맞춤법 검사기]
---
{% include JB/setup %}

([업데이트된 포스트]({% post_url 2013-04-30-hunspell-ko %})가 있습니다.)

### 소개

작년 이맘때까지만 하더라도 소스가 공개된 쓸만한 한글 맞춤법 검사기가 없었습니다.
며칠 전에 [hunspell][]에서 사용 가능한 한글 단어 사전이 개발 되고 있다는 것을 알게 되었습니다.

* [프로젝트 홈][spellchecker-ko]
* [구글 그룹][구글 그룹]

OpenOffice와 Firefox, 그리고 기타 [hunspell][]이나 [enchant][]를 사용하는 소프트웨어들에서 잘 동작합니다.
Emacs에서도 ispell과 enchant를 이용하면 한글 맞춤법 검사를 할 수 있습니다.

### Emacs 설정

우선 hunspell 1.2.8 이상의 버전과 한글 사전 데이터(.aff와 .dic 파일), enchant 가 깔려 있어야 합니다.

(우분투 9.04의 경우 기본 hunspell 버전이 1.2.6인데 launchpad ubuntu-ko 저장소에 hunspell 1.2.8 버전과 hunspell-ko-kr이 패키징 되어 있습니다. 아래 저장소를 추가하면 쉽게 설치가 가능합니다.)

     deb http://ppa.launchpad.net/ubuntu-ko/ubuntu jaunty main
     deb-src http://ppa.launchpad.net/ubuntu-ko/ubuntu jaunty main

(enchant는 libenchant1c2a 패키지에 있습니다.)

이제 .emacs 파일에 아래의 내용을 추가하면 ispell.el로 한글 맞춤법 검사가 가능하게 됩니다. (`flyspell-mode`도 잘 동작합니다.)

    ;; enchant를 ispell 프로그램으로 사용
    (setq ispell-program-name "enchant")
 
    ;; 사전 목록에 한국어("korean") 추가
    (if (>= emacs-major-version 23)
        (setq ispell-local-dictionary-alist
    	  '(("korean"
    	     "[가-힣]"
    	     "[^가-힣]"
    	     "" nil
    	     ("-d" "ko_KR")
    	     nil utf-8)))
        (setq ispell-local-dictionary-alist
    	  '(("korean"
    	     "[가-힝]"
    	     "[^가-힝]"
    	     "" nil
    	     ("-d" "ko_KR")
    	     nil utf-8))))

    ;; 한국어를 기본 사전으로 지정
    (setq ispell-dictionary "korean")

Emacs 버전 22와 23에서 테스트 되었습니다. 버전 22에서는 유니코드로 표현 가능한 모든 글자를 지원하지 않기 때문에 정규식에 '힣'이 들어가면 제대로 보이지도 않고 동작하지도 않습니다.
버전에 따라서 `ispell-local-dictionary-alist` 설정을 다르게 한 것은 이 때문입니다.

### 이야기

그동안 공개된 한글 맞춤법 검사기가 없어서 얼마나 아쉬웠는지 모릅니다. 저 뿐만 아니라 많은 분들이 비슷하게 한글 맞춤법 검사기를 기다려왔을 것입니다.
류창우님이 해결책의 실마리를 [hunspell 에서 발견했습니다.][hunspell-1st-blog]
이후로 관심 있는 사람들이 모여서 [구글 그룹][구글 그룹]을 만들고 쓸만한 사전을 만들기 시작했습니다.

이런 일이 진행되고 있다는 것을 알게 된 것은 며칠 전 [KLDP][]에서 [먼지분투][]에 대한 글을 보면서였습니다. 글이 KDLP 메인 화면에 자주 나오 길래 뭔가 하고 한 번 들어가 본 것인데 _"* hunspell 기반 맞춤법 검사 사전 내장"_ 이라는 항목에 정신이 번쩍 들었습니다. '어? 한글 맞춤법 검사 사전이 있다고? 왜 이걸 여태 모르고 있었지?' 오버라고 생각할지도 모르겠지만, 저는 전율을 느꼈습니다. (조금 지난 후에 알았는데 KLDP 에 류창우님이 직접 올린 [소개의 글][kldp-hunspell-ko]이 있더군요.)

우선 아직 양이 얼마 되지 않기 때문에 관련 페이지들과 구글 그룹의 글을 대부분 살펴 볼 수 있었습니다. OpenOffice에서 잘 동작하는 것은 확인을 하였고, Firefox 에서는 빨간 줄이 나타나지 않아서 한참을 헤맸었습니다. 알고 보니 언어 선택을 바꾸지 않아서였더군요. ㅜㅜ 

이제 주로 사용하는 프로그램 중에 emacs에서만 한글 맞춤법 검사가 되면 사용에 문제가 없을 것 같았습니다.
그룹 글들을 검색해 보고 [nomos님의 시도][nomos-emacs]를 발견했습니다.
일단 창우님의 수정된 hunspell을 설치하고 my-emacs-hunspell을 만들었습니다.
무슨 문제인지 맞춤법 검사도 안 되고 오류도 발생하지 않아서 답답했습니다.
`hunspell -a -d ....` 을 직접 실행시키고 맞춤법이 틀린 문장을 넣으면 잘 동작했기 때문에 emacs와 hunspell의 통신 상에 문제가 있는 것이라는 의심을 했습니다.
둘 사이에 끼여서 관찰했으면 좋겠다는 생각을 했지만 간단한 방법이 떠오르지가 않았습니다. (아시는 분은 댓글 달아 주세요.^^)

차선책으로 떠오른 것이 `tee`였습니다. `tee` 는 표준 입력을 파일과 표준 출력에 동시에 써주는 명령입니다. 개선 과정을 거쳐서 나온 최종 my-emacs-hunspell은 아래와 같습니다.

    #!/bin/bash
    # /usr/bin/hunspell -a -d /usr/share/myspell/dicts/ko_KR
    tee ~/tmp/ei_$$.txt | /usr/bin/hunspell -a -d /usr/share/myspell/dicts/ko_KR | tee ~/tmp/eo_$$.txt

(`$$`는 자신의 process id로 바뀝니다. 한 번 `ispell-region`을 실행하는 경우에도 my-emacs-hunspell을 여러 번 부르는 경우가 있었기 때문에 pid 별로 다른 파일이 생기도록 했습니다. emacs에서 ispell 프로세스가 생길 때마다 `~/tmp/` 디렉터리에 ei\_와 eo\_로 시작하는 두 개의 파일이 만들어지게 됩니다.)

여러 번의 실패한 시도와 문서 찾아보기의 결과로 위의 `ispell-program-name` 부분을 제외한 emacs lisp 코드에 정착하게 되었고 nomos님의 결과와 같은 결과를 만들어 낼 수 있었습니다. (검사가 잘 되기도 하고 어떤 때는 `Ispell misalignment:...`에러가 나는 결과)

에러의 원인을 알아내는 데는 그리 오래 걸리지 않았습니다. 첫 번째 한글 단어가 맞춤법이 잘못되었다고 판단하면 에러가 발생하지 않지만, 첫 번째 한글 단어가 아닌 경우에는 오류가 어김 없이 발생했습니다. 이유는 hunspell의 출력과 emacs의 ispell.el에서 기대하는 입력이 일치 하지 않기 때문입니다.
둘 간의 통신은 emacs가 한 줄을 보내면 hunspell이 맞춤법 검사 결과를 돌려주는 방식입니다. 맞춤법이 잘못된 단어를 발견하면 잘못된 단어의 위치를 알려 주게 되어있습니다. 이 위치가 hunspell은 몇 번째 바이트인가를 알려주는데, ispell.el은 몇 번째 글자인가를 알려주기를 기대하고 그 위치에서 단어를 찾으려니 에러가 발생하는 것입니다.

ispell.el을 고치는 것이 맞는지, hunspell을 고치는 것이 맞는지, (좀 더 정확히는 어떤 것을 고치는 것이 더 간단한지) 고민하던 중에 제3의 프로젝트에서 해결책을 발견하게 되었습니다. 그것이 enchant입니다.

[Enchant][]는 말하자면 여러 맞춤법 라이브러리들을 enchant 라이브러리를 통해서 사용할 수 있게 해주자는 그런 프로젝트입니다. ([홈페이지][enchant] 참조)
명령행 실행기(command line program)에 대해서 그저 "technology demo"라고 하고 있기는 하지만, 일단 검사와 대체 단어 제시 기능을 사용하는 데는 문제가 없었습니다.
이렇게 해서 `ispell-program-name`을 `"enchant"`로 바꾸는 것으로 emacs에서 한글 맞춤법 검사 기능 사용하기 "완료".

### 남은 일들

좀 있습니다.

### 끝으로

hunspell로 한글 맞춤법 검사가 가능하도록 구체화하는 데 첫 삽을 떼시고 (아직까지는) 작업 대부분을 감당하고 계신 류창우님, *감사합니다.*
아울러 옆에서 류창우님의 작업을 도와주시고 격려해 주셨던 다른 여러분께도 감사의 말을 드립니다.
지금의 hunspell이 있기까지 애쓴 여러 연구자들과 개발자들께도 감사의 말을 드립니다.

[갈퀴][]를 이용해서 단어 추가/삭제/변경 등의 제안을 하실 수 있습니다.
작은 참여들이 모여서 훌륭한 맞춤법 검사 환경이 만들어질 수 있습니다. 주저하지 마시고 참여를...

(한두 시간 안에 끝날 줄 알았던 글을 쓰는 데 꽤나 오랜 시간이 걸렸습니다. 너무 오랫동안 글을 안 써서 그런가 봅니다. 잠시, 아직 자리잡히지도 않은 갈퀴에 사람이 너무 몰려서 오히려 방해가 되지는 않을까 어이없는 걱정을 했었습니다. 그리고 깨달은 현실은 '여기 보는 사람이 얼마나 된다고...')
 
### 웹페이지들

* [한글 맞춤법 검사 프로젝트 홈][spellchecker-ko]
* [맞춤법 검사 구글 그룹][구글 그룹]
* [갈퀴][]
* [hunspell][]
* [enchant][]
* [KLDP의 사전 소개][kldp-hunspell-ko]
* [먼지분투][]
* [hunspell 첫 실험][hunspell-1st-blog]
* [nomos님의 emacs에서의 첫 실험][nomos-emacs]

[spellchecker-ko]: http://code.google.com/p/spellcheck-ko/ "한글 맞춤법 검사 프로젝트 홈"
[hunspell]: http://hunspell.sourceforge.net/ "hunspell"
[enchant]: http://www.abisource.com/projects/enchant/ "enchant"
[hunspell-1st-blog]: http://cwryu.tistory.com/106 "hunspell 한글 검사 실험"

[구글 그룹]: http://groups.google.com/group/spellcheck-ko "한글 맞춤법 검사 구글 그룹"
[KLDP]: http://kldp.org "KLDP"
[먼지분투]: http://kldp.org/node/104865# "먼지분투"
[kldp-hunspell-ko]: http://kldp.org/node/103898# "kldp huspell 맞춤법 검사"
[nomos-emacs]: http://groups.google.com/group/spellcheck-ko/browse_thread/thread/7b25fdced5823636/53f57a7fa9323b4f?lnk=gst&q=emacs#53f57a7fa9323b4f
[갈퀴]: http://galkwi.appspot.com/ "갈퀴"
