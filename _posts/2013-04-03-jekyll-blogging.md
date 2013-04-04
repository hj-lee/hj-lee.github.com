---
layout: post
title: "Jekyll과 Github Pages로 블로깅"
description: ""
category: Writing
tags: [jekyll, jekyll-bootstrap, github]
---
{% include JB/setup %}

[Blogger]: http://blogger.com/ "Blogger.com"
[Github]: http://github.com/
[Github Pages]: http://pages.github.com
[Jekyll]: http://github.com/mojombo/jekyll
[Workflow]: http://qubitlogs.com/Workflow/2013/01/22/jekyll-blogging-reference-and-perfect-workflow-guide/
 "Jekyll blogging workflow guide"
[Github Pages Help]: http://help.github.com/categories/20/articles
[Markdown]: http://daringfireball.net/projects/markdown/syntax
[rdoc]: http://rdoc.rubyforge.org/
[Textile]: http://txstyle.org/

[Jekyll Bootstrap]: http://jekyllbootstrap.com/

### 소개

예전에 잠깐 [Blogger][]로 블로깅을 했었던 적이 있다.
프로그래밍 관련 글을 작성하고 올리는 것이 약간 불편했던 것이 사실이다.

내가 원했던 것들:

* 제공되는 웹 에디터가 아닌 늘 사용하던 에디터를 사용하기
* HTML 직접 편집은 원치 않음 - 다른 마크업(markup) 필요
* 소스 코드를 추가 하는 것이 간단해야 함
* 편집을 마치면 바로 올릴수 있을 것

당시에는 [RDoc][rdoc]을 사용했었는데, 앞의 세가지는 만족했지만, 마지막이 여전히 불편했다.
글을 작성하고 나서 html 변환을 하고, Copy & Paste 를 해야했고 제목은 또 따로 써야 했다. 약간의 불편함이라고 할 수 있겠지만, 성가셨던 것은 사실이다.

이러 저런 이유로 거의 블로그 글을 쓰지 않고 있었는데 최근에 여유가 많아지면서, 다시 블로깅 방법을 고민하다가 찾아낸 것이 [Github Pages][]와 [Jekyll][]의 조합이다. 이것이 만족스러운 해결책이 될 것 같다.
([Github Pages][]는 [Github][]에서 제공해주는 사용자별 웹 호스팅이라 할 것이고, [Jekyll][]은 말하자면 "정적 웹싸이트 생성기"라고 할 수 있다.)

여기 저기 둘러보다가 [Jekyll Bootstrap][]을 기반으로 작업하기로 했다.

### 필요한 것들

#### 필수적인 것:

* 텍스트 에디터
* [Github][] 계정
* Git client
* Git 기본 사용법에 대한 이해
* [Markdown][]에 대한 이해(혹은 [Textile][])

이것만 있으면 Github에 블로그를 운영할 수 있다. Push 될 때마다 Github가 알아서 jekyll 을 돌려주기 때문이다. 하지만, 미리보기나 자동으로 post 화일 생성 같은 것은 할 수 없기 때문에 그리 바람직하지는 않다.

#### Jekyll 사용하기 위해 필요한 것들

* Ruby (1.8)
* Rubygems (gem)
* Rake
* Python

### 작업

#### 일단 만들기

우선 Jekyll을 깔았다. 참고로 `--user-install` 옵션은 root로 깔기 싫어서 한 것이다. 이 경우 `jekyll` 실행화일의 링크를 만들어 주거나 PATH 에 넣어 주어야 한다.
혹 Jekyll 배포본 페키지가 있으면 그걸 까는 것도 한가지 방법이다.

{% highlight console %}
$ gem install --user-install jekyll
{% endhighlight %}

그리고 Jekyll Bootstrap을 clone 해 왔다. `hj-lee`는 내 Github 계정이다. 그대로 복사하지는 마시길.

{% highlight console %}
$ git clone  https://github.com/plusjade/jekyll-bootstrap.git hj-lee.github.com
{% endhighlight %}

이제 Jekyll Bootstrap의 기본 페이지를 감상해 보자.

{% highlight console %}
$ cd hj-lee.github.com
$ jekyll --server
{% endhighlight %}

[http://localhost:4000](http://localhost:4000) 에서 확인해 보자.
이제 작업이 좀 필요하다.

* `_config.yml` - `title`, `author`, `production_url` 항목을 수정한다.
* 예제 블로그 포스트는 지워 버린다 - `rm -rf _posts/core-samples`

`index.md`를 아래와 같이 바꿨다. `title`을 바꾸고 블로그 목록만 나오게 빠꿨다.

{% highlight html+jinja %}
{% raw %}
---
layout: page
title: Hwijae Lee's Blog
---
{% include JB/setup %}

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> - <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
{% endraw %}
{% endhighlight %}

#### 새로운 블로그 포스트

이제 새로운 글을 써볼 때다. 

{% highlight console %}
$ rake post title="jekyll blogging"
{% endhighlight %}

오늘 날짜로 `_posts` 디렉토리 아래에 Markdown 화일이 만들어 진다. 혹은 아래와 같이 날짜를 지정할 수도 있다.

{% highlight console %}
$ rake post title="jekyll blogging" date="2013-04-03"
{% endhighlight %}

이렇게 하면 `_posts` 디렉토리 안에 `2013-04-03-jekyll-blogging.md` 화일이 만들어진다. `rake` 로 포스트를 만들 때 `title`은 화일명과 생성된 화일 안의 `title` 항목을 미리 채우는 데 사용된다. 실제 제목은 화일 안에 있는 `title` 항목이 결정하기 때문에, 화일명을 일치시키려고 애쓸 필요는 없다.

#### Push 하고 확인

#### Comment 관리

disqus 등록

analytics 일단 보류

#### Theme 변경

http://themes.jekyllbootstrap.com/

$ rake theme:install git="git://github.com/jekyllbootstrap/theme-mark-reid.git"

#### 소스 코드 포함하기

