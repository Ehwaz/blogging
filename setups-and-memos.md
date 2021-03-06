* Tumblr 설정 관련
 * 기본 theme으로 Lightweight 선택
 * Theme 수정 내역
   * Post 제목(&lt;h3>) 글꼴이 Georgia로 되어 있어 한글 글자가 이상하게 나온다.  
   이 [포스트](http://valleyofwind.tumblr.com/post/47005704523/tumblr%EC%97%90-%EA%B5%AC%EA%B8%80-%EC%9B%B9%ED%8F%B0%ED%8A%B8-%EB%82%98%EB%88%94%EA%B3%A0%EB%94%95-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0) 참고하여 나눔명조로 변경. 사이즈는 28px/1.6
   * Code highlighting을 위해 [링크](http://ramyabot.tumblr.com/post/139379919001/tumblr-code-highlighting) 참조하여 code-prettify 적용.
   * 너비 850px로 변경("wrapper" div 속성).
   * 블로그 제목 폰트 사이즈 40px로 변경.
   * Archive 링크가 없어서 메뉴 바에 추가.
   * &lt;h4> 태그 크기가 본문과 같게 나와서 아래 css 추가.  
    ```css
     .post-content h4 {
       font: 22px/1.6 'Nanum Myeongjo';
       margin-bottom: 7px;
     }
    ```
   * 아래 css로 code-prettify 폰트 설정.
   ```css
   .prettyprint {
        font-family: Consolas, Monaco, "Courier New", monospace;
        font-size: 14px;
    }
   ```
   * 댓글 창 추가: [Disqus](https://disqus.com) 가입 후 ehwaz-dev-log로 댓글 페이지 만든 뒤 Tumblr theme에 입력 (무지 쉽다!)


* Markdown 관련
 * 간편 가이드: ( [1](http://blog.kalkin7.com/2014/02/10/lets-write-using-markdown/), [2](http://sergeswin.com/1013),
 [3](http://markdown-guide.readthedocs.org/en/latest/basics.html) )
 * line break 삽입하기
   * 그 전 line 끝에 2개 이상의 space를 입력하고 새로운 줄에 내용 입력.
   * space가 하나인지 두개인지 보기 까다롭다. 이런 방식은 명시적이지 않아서 별론데..
 * Github markdown 문법 가이드: ( [1](https://nolboo.github.io/blog/2014/03/25/github-flavored-markdown) )
