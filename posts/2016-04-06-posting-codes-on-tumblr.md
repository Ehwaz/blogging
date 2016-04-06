# 개발 블로그, Tumblr, Code snippet

개발 블로그로 사용할 서비스로 Tumblr를 고른 이유는

1. 별도의 설치나 설정없이 간편하게 시작할 수 있을 것 (시작이 반이므로!)
2. 마음에 드는 테마를 쉽게 사용할 수 있을 것 (화단이 예쁘면 더 꾸미고 싶어지므로!)

이렇게 두 가지였다. 더군다나 글 작성 방식으로 Markdown을 지원한다는게 무척 마음에 들었다. WYSIWYG 에디터는 각 서비스들마다 미세하게 다르다는 점이, HTML은 소잡는 칼로 닭잡는 느낌이라는 점 때문에 아무래도 호감이 가지 않았다.

다만, 다른 분들의 개발 블로그 셋업 글들을 읽다보니 개발 블로그 용으로서 Tumblr는 소스 코드를 붙이기 어렵다는 점이 단점으로 지적[[1](http://blog.kivol.net/post/51141201640/%EA%B0%9C%EB%B0%9C%EB%B8%94%EB%A1%9C%EA%B7%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0), [2](http://item4.tumblr.com/post/88079470919/%ED%85%80%EB%B8%94%EB%9F%AC%EB%A5%BC-%EA%B0%9C%EB%B0%9C%EC%9E%90-%EB%B8%94%EB%A1%9C%EA%B7%B8%EB%A1%9C-%EC%93%B0%EA%B8%B0-%EA%B3%A4%EB%9E%80%ED%95%9C-%EC%9D%B4%EC%9C%A0)]되는게 맘에 걸렸다. 하지만 포스팅하신지 꽤 시간이 지난 글들이었기에 일단 먼저 구글님께 “tumblr posting code snippets"라고 공손히 물어보았다.

찾아보니 누군가 2달 전 글로 구글의 [code-prettify](https://github.com/google/code-prettify)라는 Javascript 라이브러리를 Tumblr theme에 import하여 syntax highlighting을 하는 [방법](http://ramyabot.tumblr.com/post/139379919001/tumblr-code-highlighting)을 그럴듯하게 올렸길래 냉큼 적용해보았다.

아래처럼 HTML 코드를 입력하면

<pre><xmp class="prettyprint lang-html linenums">
<pre><code class="prettyprint lang-java linenums">public static helloWorlds(int num) {
  for (int cnt=0; cnt &lt; num; cnt++) {
    System.out.println("Hello world!");
  }
}
</code></pre>
</xmp></pre>

아래처럼 나온다!

<pre>
<code class="prettyprint lang-java linenums">public static helloWorlds(int num) {
  for (int cnt = 0; cnt &lt; num; cnt++) {
    System.out.println("Hello world!");
  }
}
</code></pre>

보이는 결과만으로는 꽤 만족스럽지만, '&lt;', '&gt;'를 일일이 변환해줘야 한다는건 좀 성가시다. 사실 코드를 붙이기 어렵다라고 지적하셨던 건 syntax highlighting이 아니라 이 점을 가리켰던 것 같은데, Tumblr가 작성한 내용 자체를 별도의 변환 없이 그대로 Markdown이나 HTML로 처리하는 이상 어쩔수 없는 것 같다. 결국 내게 장점으로 느껴졌던 점이 단점으로도 작용한 셈이 되었다. (양날의 검?)

HTML이나 CSS 코드의 경우, StackOverflow에 관련 [질문](http://stackoverflow.com/questions/10441911/using-google-prettify-to-display-html/10441965#10441965)이 있어 우회적인 방법으로 일단 출력되게는 할 수 있었다. &lt;code&gt; 대신 &lt;xmp&gt;를 사용하는 방식인데, 사실 저 글의 댓글에서도 지적하듯이 W3C [표준](https://www.w3.org/TR/html5/obsolete.html#non-conforming-features)에서는 &lt;xmp&gt; 자체를 부적절한(Obsolete and non-conforming feature) 기능으로 명시하고 있어서 올바른 방향이라고 보긴 어려울 것 같다. 역시 표준에서는 &lt;xmp&gt; 대신 &lt;pre&gt;와 &lt;code&gt;를 사용하고 '&lt;'는 '&amp;lt;', '&gt;'는 '&amp;gt;'로 대체하도록 권장하고 있다.

일단 지금은 호스팅 없이 Markdown으로 블로깅을 해보고 싶은 마음이 문자 변환의 귀찮음보다는 커서 계속 Tumblr를 써볼 생각이다. 긴 코드를 첨부해야겠다 싶으면 [Github Gist](https://gist.github.com/)로 embed해도 될거 같고. 블로그에 재미를 붙였다 싶었을때 본격적으로 [Octopress](http://octopress.org/)로 넘어가면 좋을 것 같다.


참고) Line number가 매 5줄마다 표시되는게 기본 설정이었는데(대체 왜?) 공식 [README](https://github.com/google/code-prettify#how-can-i-put-line-numbers-on-every-line-instead-of-just-every-fifth-line) 대로 아래 css 코드를 Tumblr theme에 입력해서 해결했다.

<pre><xmp class="prettyprint lang-css">
<style>
li.L0, li.L1, li.L2, li.L3,
li.L5, li.L6, li.L7, li.L8
{ list-style-type: decimal !important }
</style>
</xmp></pre>

덧) Post를 올린 후 수정하려고 다시 열어보니 에디터에는 &amp;lt;, &amp;gt;가 죄다 다시 &lt;, &gt;로 바뀌어서 올라온다?! 덕분에 무심코 저장을 눌렀다가 변환된 HTML 태그들 때문에 글이 작살나버렸다. 일단 글 작성/수정은 atom의 [Markdown preview](https://github.com/atom/markdown-preview)로 하고 백업용으로 어디다 repo를 하나 파야겠다.
