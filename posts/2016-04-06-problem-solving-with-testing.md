# 코딩 문제 템플릿 with 테스팅

나는 코딩 문제 사이트로 [HackerRank](https://www.hackerrank.com)를 즐겨 이용하고 있다. 이 사이트가 다른 서비스들과 소소하게 다른 점은 문제를 풀면 원하는 테스트 케이스를 열 수 있는 Hacko라는 포인트를 준다는 것이다. 끙끙 앓지 않고 내가 통과하지 못한 케이스를 직접 받아서 테스트해볼 수 있다는게 참 마음에 든다.[^1] 테스트 케이스는 input, output txt 파일으로 각각 주어지는데, 사실 이런 웹기반 online judge에서는 입력 파일이 조금만 커도 입력이 안되는 경우가 다반사라서 결국 로컬 환경에서 직접 돌려야 한다. 하지만 암만 익숙한 코드여도 File I/O API를 써넣을 때마다 여간 거슬리는게 아니었다.

그래서 이 참에 파일 입출력도 적절히 처리하고 결과 테스트도 자동 판별할 수 있는 적절한 IDE 템플릿을 만들어보자고 마음먹었다. 애당초 [테스트 주도 개발](https://ko.wikipedia.org/wiki/%ED%85%8C%EC%8A%A4%ED%8A%B8_%EC%A3%BC%EB%8F%84_%EA%B0%9C%EB%B0%9C)이라는 개발 프로세스도 있는 마당에 테스트 라이브러리 적용해보자 생각도 하고 있었고. 그리고 코딩 문제는 성공/실패 조건이 분명하게 주어지니 어찌보면 테스트 케이스를 적용하기에 가장 간편한 예제다.

먼저 사용하기 편리한 템플릿의 조건은 무엇일까 생각해보았다.

1. 테스트 라이브러리를 사용하여 테스트 케이스를 간편하게 구성할 수 있다.
2. Online judge 서비스에서 짠 문제 풀이 코드를 최대한 그대로 사용할 수 있다.
3. 표준 입출력과 파일 입출력 각각을 동일한 interface를 통해 다룰 수 있다.

### 1. 테스트 라이브러리 선택
코딩 문제 풀이에는 Java를 주로 사용하고 있다. Java 테스트 라이브러리로는 [Junit](http://junit.org/junit4/)과 [TestNG](http://testng.org/doc/index.html)가 많이 사용되는 것 같다. 차이점을 비교한 [포스트](http://www.mkyong.com/unittest/junit-4-vs-testng-comparison/)를 보니 TestNG에서는 test group 구성과 method간의 dependency test가 추가로 가능하다고 한다. 필요한 기능들은 아니지만 두 라이브러리의 interface가 거의 같기 때문에 이왕이면 기능이 더 많은 TestNG를 선택했다. 사용 방법은 IntelliJ의 친절한 [도움말](https://www.jetbrains.com/help/idea/2016.1/testing.html)을 참고했다.


### 2. 문제 풀이 코드 그대로 사용하기
어디서 [유닛 테스트](https://ko.wikipedia.org/wiki/%EC%9C%A0%EB%8B%9B_%ED%85%8C%EC%8A%A4%ED%8A%B8)에 대해 주워들은 건 있었던지라, 맨처음 생각했던건 아래처럼 문제를 풀때 입출력 부분은 별도 코드로 빼고, 입력 구조체를 인자로 받고 결과를 리턴하는 문제 풀이 메소드를 따로 구현하는 방향이었다.

<pre><code class="prettyprint lang-java linenums">public class Solution {
  public static int[] solveProb(int a, String[] b) {
    ... // a, b를 사용해서 문제를 처리하고 결과를 리턴
  }

  public static void main(String[] args) {
    int a, maxSize = 50;
    String[] b = new String[maxSize];
    ... // stdin의 내용을 받아 입력 자료 구조체 a, b에 저장한다.

    int[] result = solveProb(a, b);

    ... // result의 내용을 출력한다.
  }
}
</code></pre>

이 방향으로 시험삼아 짜보다 두 가지 문제를 발견했다.

1. 테스팅 과정에서 입력 구조체를 구성하는 것이 너무 번거롭다.  
'@BeforeTest' 메소드에서 입력 구조체를 구성해야 하는데, 풀이 코드에서 테스트 코드로 복붙하는 식이라 여전히 품이 많이 든다. 게다가 입력 구조체는 각 문제마다 대부분 다르므로 테스트 케이스를 위해 매 문제마다 따로 코드를 짜야 한다.

2. 입출력 처리와 풀이 메소드를 나누는 방식 자체가 제한으로 작용한다.  
대부분의 간단한 풀이들은 입출력 처리와 문제 풀이 로직이 함께 돌아간다. (예: 같은 for문 내에서 stdin에서 값을 받아 처리하고 stdout으로 출력)  
풀이 메소드와 입출력 처리를 분리하느라 간단한 문제도 복잡하게 짜야 하게 된다.

개선한 두번째 방식은 아래와 같다. 코드는 별도의 메소드에 짜되, 인자값으로는 Scanner 객체만을 받는다.

<pre><code class="prettyprint lang-java linenums">public class Solution {
  public static void solveProb(Scanner sc) {
    ... // Scanner 객체로부터 입력을 받아 문제를 풀고 stdout으로 출력한다.
  }

  public static void main(String[] args) {
    Scanner sc = new Scanner(System.in);
    solveProb(sc);
  }
}
</code></pre>

테스트 케이스에서는 input 파일을 Scanner 객체로 열어 넘겨주고, stdout 결과를 별도의 방법으로 String을 뽑아 output 파일 내용과 비교해서 옳은 답인지 판별하면 된다.

사실 두번째 방식처럼 짤 수 있었던건 표준 입출력과 파일 입출력 처리를 원하는 방식으로 다룰 수 있었던 덕분이다.


### 3. 표준 입출력과 파일 입출력 처리
입력의 경우 Scanner object를 만들때 stdin 또는 파일 object를 넘겨주는 방식으로 간편하게 인터페이스를 통일할 수 있었다. ([참고](http://stackoverflow.com/a/25992945))

<pre><code class="prettyprint lang-java linenums">
  Scanner stdSc = new Scanner(System.in);  // 표준 입력

  File file = new File("file_path");
  Scanner fileSc = new Scanner(file);  // 파일 입력
</code></pre>


출력의 경우 단순하게는 문제 풀이 메소드에서 System.out과 파일 PrintStream을 필요에 따라 하나의 PrintStream 객체에 대입하여 사용하면 되겠지만, 이는 '코드 그대로 사용하기' 조건을 충족시키지 못하는 방법이다. 즉, 결과를 System.out으로 출력하도록 짠 기존 코드를 별도의 PrintStream 객체로 대체하도록 수정해야 하는데 이는 번거롭고 부자연스럽다.

다른 방법을 찾던 와중에 발견한 것이 테스트 실행 전에 System.out을 임시로 다른 PrintStream 객체로 set하고, 테스트가 끝난 뒤 다시 되돌려놓는 방법[[참고1](http://stackoverflow.com/a/1119559), [참고2](http://epicdevs.com/8)]이었다.

간단히 정리하면 아래처럼 사용하는 것인데,
<pre><code class="prettyprint lang-java linenums">public class SolutionTest {
  private final ByteArrayOutputStream outContent = new ByteArrayOutputStream();
  PrintStream original = System.out;
  ...

  @BeforeTest
  public void setUp() throws Exception {
    ...
    System.setOut(new PrintStream(outContent));
  }

  @AfterTest
  public void tearDown() throws Exception {
    System.setOut(original);
    ...
  }

  @Test
  public void testSolveProb() throws Exception {
    ... // 풀이 메소드를 호출한다.
    ... // output file의 내용을 answer에 넣는다.

    String result = outContent.toString().trim();
    Assert.assertEquals(result, answer);
  }
}
</code></pre>

'코드 그대로 사용하기' 조건에도 부합하고 테스트 라이브러리에서 제공하는 '@beforeTest', '@afterTest' annotation과도 딱 어울리는 방법이라 정말 마음에 너무너무 쏙 든다.

### 정리

중간에 기능적 분리를 만족시키기 위해 입출력 처리와 풀이 로직을 나누었던 것이 제약으로 작용하는걸 겪으며 Knuth옹께서 말씀하신 ['Premature optimization is the root of all evil'](http://c2.com/cgi/wiki?PrematureOptimization)이 떠올랐다.

어쩄든 이렇게 원하던 조건을 나름 만족하는 템플릿을 만들고 써보니 꽤 마음에 든다(사실 그러니 블로그로 정리까지 하는거지만ㅎㅎ). 앞으로도 두고두고 유용하게 써보련다.

위의 코드들은 내 Github의 problem solving 저장소에 [올려놓았다](https://github.com/Ehwaz/ProblemSolving/tree/master/PSInJava/src/ehwaz/problem_solving/ds/arrays/SparseArrays).  
(SparseArrays, SparseArraysFileTest가 첫번째 방법, SparseArrays2, SparseArrays2FileTest가 두번째 방법의 풀이와 테스트다.)  
SparseArrays2FileTest가 SparseArraysFileTest보다 훨씬 간단한걸 확인할 수 있다.

- - -
덧) 그외에 자잘하게 문제가 됬던 부분들은

1. Windows에서 돌리다보니 System.out.println(...)으로 출력하면 [캐리지 리턴]이 붙어서 매 행마다 '\r'이 추가되는 점
 * '\r'을 제거하는 방법으로 해결

2. TestNG 실행시 아래 같이 NullPointerException이 나오는 문제
<pre><code class="prettyprint linenums">java.lang.NullPointerException
	at org.testng.SuiteRunnerWorker.runSuite(SuiteRunnerWorker.java:80)
	at org.testng.SuiteRunnerWorker.run(SuiteRunnerWorker.java:86)
</code></pre>
 * 참고 코드에 System.out을 되돌려 놓는 코드가 System.setOut(null);으로 구현이 되어 있어 발생. null 대신 따로 저장해놓은 System.out 객체를 넘겨주는 것으로 해결.

[^1]: [leetCode](https://leetcode.com/), [Algospot](https://algospot.com/) 등의 다른 서비스들보다 HackerRank를 선호하는데는 이것 외에도 다른 이유들이 몇 개 있는데, 나중에 별도 포스트로 간단히 정리해보련다.
