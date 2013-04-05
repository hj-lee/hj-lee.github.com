---
layout: post
title: "Jekyll 과 Github Pages 로 블로깅"
date: 2013-04-05
description: ""
category: Writing
tags: [jekyll, jekyll-bootstrap, github]
---
{% include JB/setup %}

[Blogger]: http://blogger.com/ "Blogger.com"

[Github]: http://github.com/
[Github Pages]: http://pages.github.com

[Jekyll]: http://github.com/mojombo/jekyll
[Jekyll Bootstrap]: http://jekyllbootstrap.com/

[Markdown]: http://daringfireball.net/projects/markdown/syntax
[rdoc]: http://rdoc.rubyforge.org/
[Textile]: http://txstyle.org/

[YAML Front Matter]: http://github.com/mojombo/jekyll/wiki/YAML-Front-Matter

[Pygments]: http://pygments.org/
[Pygments langs]: http://pygments.org/docs/lexers/

[Liquid 템플릿]: http://github.com/Shopify/liquid/wiki/Liquid-for-Designers
[확장된 Liquid 템플릿]: http://github.com/mojombo/jekyll/wiki/liquid-extensions

[http://localhost:4000]: http://localhost:4000
[My Blog]: http://hj-lee.github.com/

### 소개

예전에 잠깐 [Blogger][] 로 블로깅을 했었던 적이 있다.
글, 특히 프로그래밍 관련 글을 작성하고 올리는 것이 불편했던 것이 사실이다.

내가 원했던 것들:

* 제공되는 웹 에디터가 아닌 늘 사용하던 에디터를 사용하기
* HTML 직접 편집은 원치 않음 - 다른 마크업(markup) 필요
* 소스 코드를 추가 하는 것이 간단해야 함
* 편집을 마치면 바로 올릴 수 있을 것

당시에 [RDoc][rdoc] 을 [사용](http://hjlee-p.blogspot.kr/2008/08/rdoc-blog.html)하다가 [Markdown][] 을 사용했었는데, 앞의 세가지 조건은 만족했지만, 마지막이 여전히 불편했다.
글을 작성하고 나서 HTML 변환을 하고, Copy & Paste 를 해야 했고 제목은 또 따로 써야 했다. 약간의 불편함이라고 할 수 있겠지만, 성가셨던 것은 사실이다.

이러 저런 이유로 거의 블로그 글을 쓰지 않고 있었는데 최근에 여유가 많아지면서, 다시 블로깅 방법을 고민하다가 찾아낸 것이 [Github Pages][] 와 [Jekyll][] 의 조합이다. 이것이 만족스러운 해결책이 될 것 같다.
([Github Pages][] 는 [Github][] 에서 제공해주는 사용자 별 웹 호스팅이라 할 것이고, [Jekyll][] 은 말하자면 "정적 웹사이트 생성기"라고 할 수 있다.)

여기 저기 둘러보다가 [Jekyll Bootstrap][] 을 기반으로 작업하기로 했다.

### 필요한 것들

#### 필수적인 것:

* 텍스트 에디터
* [Github][] 계정
* Git client
* Git 기본 사용법에 대한 이해
* [Markdown][] 에 대한 이해

이것만 있으면 [Github][] 에 블로그를 운영할 수 있다. Push 될 때마다 [Github][] 가 알아서 페이지들을 다시 만들어 주기 때문이다. 하지만, 미리보기나 자동으로 포스트(post) 파일 생성 같은 것은 할 수 없기 때문에 그리 바람직하지는 않다.

#### [Jekyll][] 과 [Jekyll Bootstrap][] 을 사용하기 위해 필요한 것들

* Ruby (1.8)
* Rubygems (gem)
* Rake
* Python

### 작업

#### 일단 만들기

우선 [Jekyll][] 을 깔았다. 참고로 `--user-install` 옵션은 root로 깔기 싫어서 한 것이다. 이 경우 `jekyll` 실행 파일의 링크를 만들어 주거나 `PATH` 에 넣어 주어야 한다.

{% highlight bash %}
gem install --user-install jekyll
{% endhighlight %}

그리고 [Jekyll Bootstrap][] 을 clone 해 왔다. `hj-lee` 는 내 [Github][] 계정이다. 그대로 복사하지는 마시길.

{% highlight bash %}
git clone  http://github.com/plusjade/jekyll-bootstrap.git hj-lee.github.com
cd hj-lee.github.com
git remote set-url origin git@github.com:hj-lee/hj-lee.github.com.git
{% endhighlight %}

이제 [Jekyll Bootstrap][] 의 기본 페이지를 감상해 보자.

{% highlight bash %}
rake preview
{% endhighlight %}

`rake preview` 는 `jekyll --server --auto` 를 실행시키는 또 다른 방법이다.
[http://localhost:4000][] 에서 확인해 보자.
작업이 확실히 필요함을 알 수 있다.

* `_config.yml` - `title`, `author`, `production_url` 항목을 수정한다.
* 예제 블로그 포스트는 지워 버린다 - `rm -rf _posts/core-samples`

`index.md` 를 아래와 같이 바꿨다. `title` 을 바꾸고 글 목록만 나오게 했다.

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

이제 바뀐 것이 잘 적용되어서 만들어 지는지 확인해 볼 때다.
[http://localhost:4000][] 을 새로고침 해보자.

(`rake preview` 를 실행시켜 놓으면 보통 파일이 바뀔 때 HTML 파일들도 알아서 업데이트가 되는데, 가끔 시간이 걸리거나 잘 동작하지 않는 경우가 있다. 이럴 때는 `rake preview` 를 죽이고 `_site` 디렉터리를 지운 뒤에 `rake preview` 를 다시 띄워주면 된다.)

#### 새로운 블로그 포스트

[Jekyll Bootstrap][] 은 새로운 포스트를 쉽게 만들 수 있는 기능을 제공한다.

{% highlight bash %}
rake post title="jekyll blogging"
{% endhighlight %}

오늘 날짜로 `_posts` 디렉터리 아래에 [Markdown][] 파일이 만들어 진다. 혹은 아래와 같이 날짜를 지정할 수도 있다.

{% highlight bash %}
rake post title="jekyll blogging" date="2013-04-03"
{% endhighlight %}

이렇게 하면 `_posts` 디렉터리 안에 `2013-04-03-jekyll-blogging.md` 파일이 만들어진다. `rake` 로 포스트를 만들 때 `title` 은 파일 명과 생성된 파일 안의 `title` 항목을 미리 채우는 데 사용된다. 실제 제목은 파일 안에 있는 `title` 항목이 결정하기 때문에, 파일 명을 일치 시키려고 애쓸 필요는 없다.

[Jekyll][] 의 페이지(page)와 포스트(post) 파일은 [YAML Front Matter][] 부분과 내용 부분으로 나뉜다. 내용 부분은 [Liquid 템플릿][]의  [확장 버전][확장된 Liquid 템플릿]과 확장자에 따른 마크업 처리를 통해 최종 HTML 파일을 만들게 된다. 앞에서 언급한 대로 [Jekyll Bootstrap][] 은 기본 [Markdown][] 포스트 파일을 만들어 주는데, 포스트 파일에서 [Liquid 템플릿][] 을 쓸 일은 그리 많지 않다.

#### Github Pages에 올리기

[Github][] 계정에 `hj-lee.github.com` 라는 이름으로 빈 Repository 를 만든다.
혹, 지금까지 수정된 내용과 새로 만들어진 포스트를 `commit` 하지 않았다면 `commit` 해준다. 이제 `push` 하면 [Github][]에서 나의 블로그 사이트를 만들기 시작한다. 

{% highlight bash %}
git push origin master
{% endhighlight %}

성급하게 [http://hj-lee.github.com/][My Blog] 에 가보니, 조금 기다리라고 나온다. 최대 10분 걸린 단다. 그래서 잠시 다른 일 하다가 새로 고침 해보니, 이예~~ 이제 내 블로그가 만들어졌다.

#### 코드 하이라이팅(Code Highlighting)

[Jekyll][] 은 [Pygments][] 를 이용한 코드 하이라이팅을 지원한다. 문서 내에 사용하려면 [확장된 Liquid 템플릿][] 의 `highlight` 태그를 사용하면 된다.
예를 들어 아래와 같은 내용을 문서 파일 안에 추가 하면

{% raw %}
	{% highlight ruby %}
	desc "Launch preview environment"
	task :preview do
	  system "jekyll --auto --server"
	end # task :preview
	{% endhighlight %}
{% endraw %}

아래와 같은 결과가 보여진다.
(참고로 이 코드는 [Jekyll Bootstrap][] 의 Rakefile 일부이다.)

{% highlight ruby %}
desc "Launch preview environment"
task :preview do
  system "jekyll --auto --server"
end # task :preview
{% endhighlight %}

아쉽게도 [Jekyll Bootstrap][]의 기본 tweeter 테마에는 코드 하이라이팅을 위한 CSS 파일이 포함되어 있지 않아서 살짝 수정이 필요했다.

1. 예제 [syntax.css](http://github.com/mojombo/tpw/raw/master/css/syntax.css) 파일을 받아서 `assets/themes/twitter/css/syntax.css` 로 저장
2. `_includes/themes/twitter/default.html` 의 styles 부분에
`{%raw%}<link href="{{ ASSET_PATH }}/css/syntax.css" rel="stylesheet" type="text/css">{%endraw%}` 추가


### 기타

이것으로 일단 기본이 되었다. 새로운 글을 올리거나 바뀐 내용을 올리려면 그저 `commit` 하고 `push` 하기만 하면 된다.

이 외에도 [Jekyll Bootstrap][] 은 [Theme 지원](http://themes.jekyllbootstrap.com/)을 하고, 코멘트 지원 사이트나, 분석툴들과 연계가 쉽게 만들어져 있다. 현재 [disqus](http://disqus.com/) 에 가입해서 사용을 시작했다.

[Usage]: http://github.com/mojombo/jekyll/wiki/usage
[Configuration]: http://github.com/mojombo/jekyll/wiki/configuration

아래에 중요한 페이지들을 모아 보았다.

#### 전반적인 이해와 처음 셋업시

* [Github Pages][]
* [Jekyll][] - 더불어 [Usage][] 와 [Configuration][]
* [Jekyll Bootstrap][]

#### 문서(페이지나 포스트) 작성 시 필요한 부분

* [Markdown][] 혹은 [Markdown 한글 설명](http://blog-kkamagui.cloudfoundry.com/posts/1) - 필수
* [Liquid 템플릿][] - 포스트 작성 시에는 그다지 필요 없음.
  `raw` 태그는 가끔 유용
* [확장된 Liquid 템플릿][] - 포스트 작성 시 `highlight` 태그 외에는 그다지 필요 없음.
* [Pygments 지원 언어들][Pygments langs] - `highlight` 태그 사용 시
* [YAML Front Matter][]
