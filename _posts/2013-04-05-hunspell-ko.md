---
layout: post
title: "Emacs에서 hunspell 잘 사용하기"
description: ""
category: hunspell-ko
tags: [emacs, hunspell, 맞춤법 검사기]
date: 2013-04-06
published: false
---
{% include JB/setup %}

[char-position.patch]: /assets/codes/hunspell-patch/char-position.patch
[input-conv-putdic.patch]: /assets/codes/hunspell-patch/input-conv-putdic.patch

전에 enchant 를 emacs 와 함께 썼었는데, 최근에 다시 사용하려니 간혹 먹통이 되는 경우가 생기더군요.

마침 개인 사전이나 "Accept for this session" 같은 기능이 동작 하게 해야겠다고 맘 먹고 있었던 참이었기에 hunspell 을 고쳐 버렸습니다.

hunspell 1.3.2 버전 기준으로 2개의 패치입니다.

char-position.patch

ispell 호환 모드, 혹은 기본 출력 모드 에서 byte position 대신 character position 을 표시하도록 변경하였습니다.

이것은 hunspell 라이브러리를 직접 사용하는 프로그램들과는 무관하고,
pipe를 사용해서 통신하는 다른 프로그램들과는 문제가 될 수도 있습니다.
다만, 제가 아는 한에서는 emacs 가 이렇게 동작하는 대표 프로그램이고, 이 패치는 emacs 가 hunspell 과 잘 동작하게 해줍니다.

input-conv-putdic.patch

이 패치는 .aff 파일에 ICONV 항목이 있는 경우 개인 사전이나 세션 중의 단어를 추가할 때 무조건 ICONV 변환을 거치도록 합니다.
 
국어의 경우 문제가 해결 되는데, ICONV를 사용하는 다른 언어들은 혹, 문제가 될 수도 있을 것 같습니다. .aff 파일에 개인 사전 등록 시에 ICONV 변환을 할 것을 명시해주는 옵션을 넣거나 하면 딱 좋을 것 같기는 합니다만, 제가 결정할 일은 아니라 일단 공개합니다.

개인 사전은 홈 디렉터리에 .hunspell_ko_KR 과 같이 언어 별로 생기는데, 여기 있는 단어들에도 '단어/변형규칙' 과 같은 형태로 단어 변형 규칙을 명시해 줄 수 있습니다.
동사나 형용사는 포기하고, 명사의 경우 현재 ko.aff 기준으로 규칙 3, 단위 명사인 경우 규칙 8에 해당합니다. 예를 들어

김만중/3

과 같은 내용을 사전에 추가하면 조사가 붙은 단어들을 더 추가할 필요가 없어집니다.

두 패치 다 hunspell 팀에 보내 보긴 했습니다만, 약간 미심쩍은 부분들이 있어서 반영이 될지는 모르겠습니다.
