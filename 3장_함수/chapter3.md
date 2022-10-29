# 3장 함수

어떤 프로그램이든 가장 기본적인 단위가 함수

```swift
public static func renderPagewithSetupsAndTeardowns(pageData: PageData, isSuite: Bool) -> String throws {
    let isTestPage = pageData.hsAttribute("Test")
    if isTestPage {
        let testPage: WikiPage = pageData.getWikiPage()
        let newPageContent: StringBuffer = StringBuffer()
        include(testPage, newPageContent, isSuite)
        newPageContent.append(pageData.getContent())
        includeTeardownPages(testPage, newPageContent, isSuite)
        pageData.setContent("\(newPageConent)")
    }
    
    return pageData.getHtml()
}
```

의도를 분명히 표현하는 함수를 어떻게 구현할 수 있을까?

함수에 어떤 속성을 부여해야 처음 읽는 사람이 프로그램 내부를 직관적으로 파악할 수 있을까?

## 작게 만들어라!
함수를 만드는 첫째 규칙은 '작게!'다. 함수를 만드는 둘째 규칙은 '더 작게!'다.

위의 함수를 아래처럼 줄여야 마땅하다

```swift
// 3-3
public static func renderPagewithSetupsAndTeardowns(pageData: PageData, isSuite: Bool) -> String throws {
    is (isTestPage(pageData)) {
        includeSetupAndTeardownPages(pageData, isSuite)
    }
    return pageData.getHtml()
}
```

### 블록과 들여쓰기
if / else / while 문 등에 들어가는 블록은 한 줄이어야 한다는 의미다.

대개 거기서 함수를 호출한다. 그러면 바깥을 감싸는 함수(enclosing function)가 작아질 뿐 아니라, 블록 안에서 호출하는 함수 이름을 적절히 짓는다면, 코드를 이해하기도 쉬워진다.

이 말은 중첩 구조가 생길만큼 함수가 커져서는 안된다는 뜻이다. 그러므로 함수에서 들여쓰기 수준은 1단이나 2단을 넘어서면 안된다.

## 한 가지만 해라!

> 함수는 한 가지를 해야 한다. 그 한 가지를 잘 해야 한다. 그 한 가지만을 해야 한다.

위 3-3은 한 가지만 하는가? 세 가지를 한다고 주장할 수도 있다

1. 페이지가 테스트 페이지인지 판단한다.
2. 그렇다면 설정 페이지와 해제 페이지를 넣는다.
3. 페이지를 HTML로 렌더링한다.

위에서 언급한 세 단계는 지정된 함수 이름 아래에서 추상화 수준이 하나다.

**지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행한다면 그 함수는 한가지 작업만 한다.**

## 함수 당 추상화 수준은 하나로!

함수가 확실히 '한 가지' 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야 한다.

`getHtml()`은 추상화 수준이 아주 높다.

반면, `let pagePathName = PathParser.render(pagePath)`는 추상화 수준이 중간이다.

그리고 `.append("\n")`와 같은 코드는 추상화 수준이 아주 낮다.

한 함수 내에 추상화 수준을 섞으면 코드를 읽는 사람이 헷갈린다.

### 위에서 아래로 코드 읽기: **내려가기** 규칙

코드는 위에서 아래로 이야기처럼 읽혀야 좋다. 한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 온다. 즉, 위에서 아래로 프로그램을 읽으면 함수 추상화 수준이 한 번에 한 단계씩 낮아진다.

이것을 **내려가기 규칙**이라 부른다.

하지만 추상화 수준이 하나인 함수를 구현하기란 쉽지 않다. 그렇지만 매우 중요한 규칙이다. 핵심은 짧으면서도 **'한 가지'만 하는 함수다.**

## 서술적인 이름을 사용하라!

* 서술적인 이름을 붙여라. 좋은 이름이 주는 가치는 아무리 강조해도 지나치지 않다.
* 이름이 길어도 괜찮다. 겁먹을 필요없다. **길고 서술적인 이름이 짧고 어려운 이름보다 좋다.**
* 이름이 붙일때는 일관성이 있어야 한다. ex) `includeSetupAndTeardownPages`, `includeSetupPages`, `includeSuiteSetupPage`, `includeSetPage`)

## 함수 인수

함수에서 이상적인 인자 개수는 0개(무항)다. 다음은 1개고, 다음은 2개다. 3개는 가능한 피하는 편이 좋다. 4개 이상은 특별한 이유가 필요하다. 특별한 이유가 있어도 사용하면 안 된다.

테스트 관점에서 보면 인수는 더 어렵다. 갖가지 인수 조합으로 함수를 검증하는 테스트 케이스를 작성한다고 상상해보라! 인수가 없다면 간단하다. 인수가 하나라도 괜찮다. 인수가 2개면 조금 복잡해진다. **인수가 3개를 넘어가면 인수마다 유효한 값으로 모든 조합을 구성해 테스트하기가 상당히 부담스러워진다.(인수가 늘어날수록 테스트 케이스가 늘어나므로 인수 개수는 적을수록 좋다)**

출력 인수는 입력 인수보다 이해하기 어렵다.

### 많이 쓰는 단항 형식
* 인수에 질문을 던지는 경우 많이 사용한다. `fileExist("MyFile") -> Bool` 이 좋은 예다.
* 인수를 뭔가로 변환해 결과를 반환하는 경우다. `fileOpen("MyFile") -> InputStream`은 String 형의 파일 이름을 InputStream으로 변환한다.
* 다소 드물게 사용하지만 그래도 아주 유용한 단항 함수 형식이 이벤트 함수다. passwordAttemptFailedNtimes(attempts: Int) 가 좋은 예다. iOS 의 경우, func buttonDidTap(sender: UIButton) 가 있다. 이벤트 함수는 조심해서 사용한다. 이벤트라는 사실이 코드에 명확히 드러나야 한다.
* 출력 인수는 피한다. `transform(in: StringBuffer) -> StringBuffer`이 `transform(out: StringBuffer)`보다 좋다.

### 플래그 인수
* 플래그 인수는 추하다. 함수로 부울 값을 넘기는 관례는 정말로 끔찍하다. **함수가 한꺼번에 여러 가지를 처리한다고 대놓고 공표하는셈이니까 말이다. 사용하지 말자.**
* `render(true)` 는 `renderForSuite()`와 `renderForSingleTest()`라는 함수로 나눠야 마땅하다.

### 이항 함수
* 인수가 2개인 함수는 인수가 1개인 함수보다 이해하기 어렵다.
* 이항 함수가 무조건 나쁘다는 소리가 아니다. 프로그램을 짜다보면 불가피한 경우도 생긴다. **하지만 그만큼 위험이 따른다는 사실을 이해하고 가능하면 단항함수로 바꾸도록 애써야 한다.** 예를 들어, writeField 메서드를 outputStream 클래스 구성원으로 만들어 outputStream.writeField(name)으로 호출한다. 아니면 outputStream을 현재 클래스 구성원 변수로 만들어 인수로 넘기지 않는다. 아니면 FieldWriter라는 새 클래스를 만들어 구성자에서 outputStream을 받고 write 메서드를 구현한다.
* 이항 함수가 적절한 경우도 있다. `let p = CGPoint(x: 0, y: 0)`가 좋은 예다. **하지만 여기서 인수 2개는 한 값을 표현하는 두 요소다. 두 요소에는 자연적인 순서도 있다.**

### 삼항 함수
삼항 함수를 만들 때는 신중히 고려하라 권고한다.

### 인수 객체
인수가 2-3개 필요하다면 일부를 **독자적인 클래스 변수**로 선언할 가능성을 짚어본다.

```swift
makeCircle(x: Double, y: Double, radius: Double) -> Circle
makeCircle(center: Point, radius: Double) -> Circle
```

### 인수 목록
때로는 인수 개수가 가변적인 함수도 필요하다.

가변 인수 전부를 동등하게 취급하면 List 형 인수 하나로 취급할 수 있다. **이런 논리로 따져보면 String.format은 사실상 이항 함수다.**

### 동사와 키워드

* 단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다. ex) `write(name)`, `writeField(name)`
* 키워드를 추가하자. `assertEquals` 보단 `assertEquals(expected: expected, actual: actual)` 이 더 좋다. 이러면 인수 순서를 기억할 필요가 없어진다.


## 부수 효과를 일으키지 마라!
* 함수에서 한 가지를 하겠다고 약속하고 남몰래 다른 일을 하는 행위이다. 따라서 예측 불가능하다.

```swift
class UserValidator {
    private var cryptographer: Cryptographer
    
    public checkPassword(userName: String, password: String) -> Bool {
        let user = UserGatewa.findByName(userName)
        
        if user != nil {
            let codedPhrase = user.getPhraseEncodedByPassword()
            let phrase = cryptographer.decrypt(codedPhrase, password)
            if "Valid Password" == phrase {
                Session.initialze()
                return true
            }
        }
        return false
    }
}
```

여기서, 함수가 일으키는 부수 효과는 Session.initialize() 호출이다.

따라서 함수 이름만 보고 함수를 호출하는 사용자는 사용자를 인증하면서 **기존 세션 정보를 지워버릴 위험에 처한다.**
 
해당 checkPassword 함수는 세션을 초기화해도 괜찮은 경우에서만 가능하다.

**자칫 잘못 호출하면 의도하지 않게 세션 정보가 날아간다. 즉 자신이 목적한대로 기능이 동작한것이 아니므로 버그다.** 

즉, `checkPasswordAndInitializeSession`이라는 이름이 훨씬 좋다, 함수가 한 가지 역할을 한다는 규칙을 위반하지만 말이다.

### 출력 인수
일반적으로 우리는 인수를 함수 입력으로 해석한다.

`appendFooter(s)`

어떤 생각이 드나 Footer에 s를 바닥갈로 추가?, s에 바닥글을 추가? 함수 선언부를 찾아보자

`func appendFooter(report: StringBuffer)`

인수가 s라는 출력 인수라는 사실은 분명하지만 함수 선언부를 찾아보고 나서야 알았다.

우리가 보통 인수를 입력 인수라고 생각하는데 출력 인수로 사용하는것 자체가 **부수 효과**다.

## 명령과 조회를 분리하라!
함수는
1. 뭔가를 수행하거나
2. 뭔가에 답하거나
3. 둘 중 하나만 해야한다.

`func set(attribute: String, value: String) -> Bool`

이 함수는 이름이 attribute인 속성을 찾아 값을 value로 설정한 후 성공하면 true를 반환하고 실패하면 false를 반환한다. value 설정하는 것을 수행하고 해당 수행에 대한 결과를 답한다. 둘 다 하는 함수다. 이 함수를 사용한 아래 코드를 보자.

`if set(attribute: "username", value: "unclebob")`

if문에서 이런 괴랄한 함수가 사용된다...

이렇게 바꿔보자

```swift
if attributeExists("username") {
    setAttribute("username", "unclebob");
}
```

## 오류 코드보다 예외를 사용하라!
명령 함수에서 오류 코드를 반환하는 방식도 명령/조회 분리 규칙을 위반한다. 동사/형용사 혼란을 일으키지 않지만 오류 코드를 반환하는 방식의 문제는 **여러 단계로 중첩되는 코드를 야기한다는 점이다.**

* 또 오류 코드를 반환하면 호출자는 **오류 코드를 곧바로 처리해야 한다는 문제**가 있다(코드가 더러워진다).

```swift
if deletePate(page) == E_OK
```

=> 오류 코드를 반환하는 방식은 여러 단계로 중첩되는 코드를 야기한다.

대신 Exception을 사용하고, try/catch 문을 사용하자. 여러 단계로 중첩되는 코드를 한방에 해결한다. 처리를 한 방에 모아준다. 오류 코드 대신 예외를 사용하면 **오류 처리 코드가 원래 코드에서 분리되므로 코드가 깔끔해진다.**

```swift
try {
	deletePage(page)
	registry.deleteReference(page.name)
	configKeys.deleteKey(page.name.makeKey())
} catch let e {
	logger.log(e.getMessage())
}
```

### Try/Catch 블록 뽑아내기
* try/catch 블록은 원래 추하다. 따라서 아래처럼 try/catch 블록을 별도 함수로 뽑아내자.

```swift
public func delete(page: Page) {
    try {
        deletePageAndAllReferences(page)
    } catch let e {
    	logError(e)
    }
}

private func deletePageAndAllReferences(page: Page) throws {
    deletePage(page)
    registry.deleteReference(page.name)
    configKeys.deleteKey(page.name.makeKey())
}

private logError(e: Error) {
    logger.log(e.getMessage())
}
```

### 오류 처리도 한 가지 작업이다.
함수처럼 오류 처리도 '한 가지'작업에 속한다. 그러므로 오류를 처리하는 함수는 오류만 처리해야 마땅하다.

### Error.swift 의존성 자석
오류 코드를 반환한다는 이야기는, 클래스든 열거형 변수든, 어디선가 오류코드를 정의한다는 뜻이다.

```swift
public enum Erorr {
	case ok
	case invalid
	case noSuch
	case locked
	case outOfResources
	case waitingForEvent
}
```

=> 위와 같은 열거형은 의존성 자석이다. 다른 클래스에서 Error enum을 import해 사용해야 하므로. (근데 Swift enum이 열거형인데..?)

즉, Error enum이 변한다면 Error enum을 사용하는 클래스 전부를 다시 컴파일하고 다시 배치해야 한다. 그래서 Error 클래스 변경이 어려워진다.

그래서 Error 열거형 변겨잉 어려워진다. 프로그래머는 재컴파일/재배치가 번거롭기에 새 오류 코드를 정의하고 싶지 않다. 그래서 새 오류 코드를 추가하는 대신 기존 오류 코드를 재사용한다.

## 반복하지 마라!
* **어쩌면 중복은 소프트웨어에서 모든 악의 근원이다.** 많은 원칙과 기법이 중복을 없애거나 제어할 목적으로 나왔다. 하위 루틴을 발명한 이래로 소프트웨어 개발에서 지금까지 일어난 혁신은 **소스 코드에서 중복을 제거하려는 지속적인 노력**으로 보인다.
* **Don't Repeat Yourself!**

## 함수를 어떻게 짜죠?
함수를 짜는 것은 여느 글짓기와 비슷하다(초안 작성 -> 다듬기).
* 처음에는 길고 복잡하게 코드를 작성한다. 하지만 이 서투른 코드를 빠짐없이 테스트하는 테스트케이스도 만든다.
* 그런 다음 코드를 다듬고,함수를 만들고, 이름을 바꾸고, 중복을 제거한다. 이와중에 **단위 테스트는 계속 통과한다.**

## 결론
대가(master) 프로그래머는 시스템을 (구현할) 프로그램이 아니라 (풀어갈) 이야기로 여긴다. 프로그래밍 언어라는 수단을 사용해 좀 더 풍부하고 좀 더 표현력이 강한 언어를 만들어 이야기를 풀어간다.
진짜 목표는 시스템이라는 이야기를 풀어가는데 있다는 사실을 명심하자. 우리가 작성하는 함수가 분명하고 정확한 언어로 깔끔하게 같이 맞아떨어져야 이야기를 풀어가기가 쉬워진다는 사실을 기억하자.