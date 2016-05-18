# 코딩 문제 템플릿 업데이트

[저번에](http://ehwaz-t.tumblr.com/post/142357060895/%EC%BD%94%EB%94%A9-%EB%AC%B8%EC%A0%9C-%ED%85%9C%ED%94%8C%EB%A6%BF-with-%ED%85%8C%EC%8A%A4%ED%8C%85) 블로그에 올린 코딩 문제 템플릿을 사용하다 세 가지 수정한 점을 정리해본다.

#### 1. 문제 풀이 메소드의 입력 클래스로 Scanner 대신 InputStream 사용

Sorting [알고리즘](https://github.com/Ehwaz/ProblemSolving/tree/master/PSInJava/src/ehwaz/problem_solving/algorithm/sorting)을 테스트하는 과정에서 한 줄이 1000000개의 int값으로 이뤄진  [테스트 케이스](https://github.com/Ehwaz/ProblemSolving/blob/master/PSInJava/src/ehwaz/problem_solving/algorithm/sorting/testcases/len1000000_less5000_bigger-5000_input.txt)를 읽어오게 되었다. 역시나 익히 [알려진](https://www.cpe.ku.ac.th/~jim/java-io.html) 대로 Scanner 클래스의 nextInt()로는 역부족이었고[[^1]], 몇 십초를 넘게 기다려도 입력 처리가 끝나지 않아 BufferedReader + StringTokenizer를 사용해야 했다. 따라서 입력 방식으로 Scanner와 BufferedReader를 필요에 따라 쓸 수 있도록 문제 풀이 메소드가 Scanner 대신 InputStream을 입력받도록 수정했다. 애당초 abstraction 관점에서도 InputStream을 받는 것이 더 적합하기도 했고.

#### 2. 여러 개의 테스트 케이스를 하나의 클래스에서 실행하기
예전 버전의 템플릿은 각 테스트 클래스당 하나의 테스트 케이스를 실행하도록 구성되어 있었다. 그러다보니 입출력 파일 여러 쌍 각각에 대해서 테스트 케이스를 구성하려면 파일을 복붙하고 클래스명과 입출력 파일명만 바꾸는 작업을 반복적으로 해야 했다. 이게 지루하기도 하거니와, 각 테스트 케이스들을 비교하면 중복되는 코드가 대부분이라 미관을 해친다.

공통되는 부분을 정리한 방법 설명은 아래 예제 코드로 갈음한다.   
지금 이 방법의 한계는, 각 @Test 메소드들마다 System.out을 각자 생성한 ByteArrayOutputStream 객체로 set하기 때문에, 반드시 순차적으로 실행되어야 한다는 점이다. 만약 각 @Test 메소드들을 parallel하게 실행한다면 테스트가 제대로 실행되지 않을 것이다.

#### 3. 테스트 추가시 수정해야 하는 코드 영역 한정시키기

테스트를 추가하기 위해 복붙 후 수정해야 하는 값들이 여기저기 있어 혼동의 여지가 있으므로 한데 모아 정리했다.   
이 과정에서 테스트할 문제 풀이 메소드를 함수 객체로 assign하기 위해 Java 8에서 추가된 [java.util.Function.Consumer](https://docs.oracle.com/javase/8/docs/api/java/util/function/Consumer.html) 인터페이스를 사용했다. Consumer 인터페이스는 반환 값이 없이 입력 객체만을 받는 함수의 타입으로 사용할 수 있다. [[^2], [^3]]   
(아래 SolutionTest 클래스의 SOLUTION_TO_TEST 객체 참고)

아래 코드는 수정된 템플릿의 문제 풀이 클래스와 테스트 클래스다.   
전체 코드는 Github [저장소](https://github.com/Ehwaz/ProblemSolvingTemplate/tree/master/Java_IntelliJ)에서 확인할 수 있다.

<pre><code class="prettyprint lang-java linenums">public class Solution {
    public static void solveProb(InputStream istream) {
        Scanner sc = new Scanner(istream);
        ... // Scanner로부터 입력을 받아 stdout으로 결과 출력
    }

    public static void main(String[] args) {
        solveProb(System.in);
    }
}
</code></pre>

<pre><code class="prettyprint lang-java linenums">public class SolutionTest {

    ///////////// Test-specific codes: you only need to modify these lines. /////////////////
    // Requires Java 8
    Consumer&lt;InputStream&gt; SOLUTION_TO_TEST = istream -> Solution.solveProb(istream);

    private final int numOfTestCases = 2;
    private final String[] INPUT_FILE_PATHS = {
            "src/ehwaz/problem_solving/template/input1.txt",
            "src/ehwaz/problem_solving/template/input2.txt"
    };
    private final String[] OUTPUT_FILE_PATHS = {
            "src/ehwaz/problem_solving/template/output1.txt",
            "src/ehwaz/problem_solving/template/output2.txt"
    };

    @Test private void runTestCase1() throws Exception {   runTest(0, SOLUTION_TO_TEST);   }
    @Test private void runTestCase2() throws Exception {   runTest(1, SOLUTION_TO_TEST);   }
    /////////////////////////////////////////////////////////////////////////////////////////

    private FileInputStream istream;
    private ByteArrayOutputStream outContent;
    private Path outputFilePath;
    PrintStream original = System.out;

    public void runTest(int testIdx, Consumer<InputStream> solutionToTest) throws Exception {
        // Input file로부터 테스트 input 준비
        istream = new FileInputStream(INPUT_FILE_PATHS[testIdx]);

        // 테스트 output을 ByteArrayOutputStream으로 받도록 준비
        outContent =  new ByteArrayOutputStream();
        System.setOut(new PrintStream(outContent));

        solutionToTest.accept(istream);   // Consumer.accept(...) 호출로 문제 풀이 메소드 실행
        String result = outContent.toString().trim();

        // Output answer 읽기
        outputFilePath = Paths.get(OUTPUT_FILE_PATHS[testIdx]);
        String answer = new String(Files.readAllBytes(outputFilePath)).trim();

        // Test 통과 여부 판별
        Assert.assertEquals(result, answer, "Test case #" + (testIdx+1) + " is failed.");

        istream.close();
        outContent.close();

        System.setOut(original);
    }
}
</code></pre>

[^1]: BufferedReader(+StringTokenizer)가 Scanner보다 빠른 이유로는 클래스 내부 buffer의 크기가 BufferedReader(8192 chars)가 Scanner(1024 chars)보다 더 크다는 점이 주로 거론된다. [[링크](http://stackoverflow.com/a/2231399)]   
Scanner는 BufferedReader에 비해 regex 매칭으로 문자열을 읽어서 변환하는 과정이 추가로 수행되긴 하지만, 대부분의 성능 비교에서는 BufferedReader와 StringTokenizer를 함께 사용하므로 두 경우 다 수행되는 과정은 같은 셈이다. Scanner의 내부 구현을 살펴보지 않는 이상 Scanner에서 regex와 value parsing이 수행되는 것이 속도 저하의 원인이라고 단언할 수는 없는 것 같다.

[^2]: Consumer 외에도 입력 객체 없이 객체를 반환하는 [Supplier](https://docs.oracle.com/javase/8/docs/api/java/util/function/Supplier.html) 인터페이스와, 객체를 입력받아 객체를 반환하는 [Function](https://docs.oracle.com/javase/8/docs/api/java/util/function/Function.html) 인터페이스가 있다.

[^3]: Java 8에서 함수형 프로그래밍 인터페이스가 추가됬다는 건 알고는 있었지만 이렇게 소소하게나마 적용해본건 처음이라 기쁘다. Java 8에 대한 좋은 [튜토리얼](http://winterbe.com/posts/2014/03/16/java-8-tutorial/)을 찾았는데 잘 정독하고 적용해보고 싶다.
