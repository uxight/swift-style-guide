# Swift Style Guide
iOS팀 내 협업을 위해 정의한 스위프트 코딩 스타일/규칙 문서입니다.
이와 관련해 기존의 잘 정의된 문서가 많은데 읽어보면 대부분 합리적이고 보편적인 이야기들이 적혀있고 공통적인 내용이 많았습니다.
따라서 새로 자체 문서를 만들기보단 그 중 하나를 그대로 써도 되겠다 생각했고 
해당 문서에서 빼거나 추가하고 싶은 항목을 개발팀 내에서 토론 후 편집하는 방향으로 진행하기로 했습니다.
 
기준이 되는 문서는 [The Official raywenderlich.com Swift Style Guide](https://github.com/raywenderlich/swift-style-guide)로 정했습니다.

#### 우선순위
1. 애플의 iOS 공식 문서나 라이브러리 스타일을 최우선 순위로 한다.
2. 그 외 [기준 문서](https://github.com/raywenderlich/swift-style-guide)를 따른다.
3. 빼고 싶거나 추가할 부분을 편집해 본 문서에 반영한다.


## 목차
- [정확성(Correctness)](#correctness)
- [네이밍(Naming)](#naming)
  - [설명문(Prose)](#prose)
  - [접두어(Class Prefixes)](#class_prefixes)
  - [딜리게이트(Delegates)](#delegates)
  - [축약 형태 사용(Use Type Inferred Context)](#use_type_inferred_context)
  - [제너릭(Generics)](#generics)
  - [언어(Language)](#language)
- [코드 구조화(Code Organization)](#code_organization)
  - [프로토콜 적용(Protocol Conformance)](#protocol_conformance)
  - [필요없는 코드(Unused Code)](#unused_code)
  - [import 최소화(Minimal Imports)](#minimal_imports)
- [빈공간(Spacing)](#spacing)
- [주석(Comments)](#comments)
- Classes and Structures
  - Use of Self
  - Protocol Conformance
  - Computed Properties
  - Final
- Function Declarations
- Function Calls
- Closure Expressions
- Types
  - Constants
  - Static Methods and Variable Type Properties
  - Optionals
  - Lazy Initialization
  - Type Inference
  - Syntactic Sugar
- Functions vs Methods
- Memory Management
  - Extending Lifetime
- Access Control
- Control Flow
  - Ternary Operator
- Golden Path
  - Failing Guards
- Semicolons
- Parentheses
- Multi-line String Literals
- No Emoji
- Organization and Bundle Identifier
- Copyright Statement
- Smiley Face
- References


<a name="correctness"/>

## 정확성(Correctness)
컴파일 시 최대한 워닝이 나지 않도록 한다. 이 규칙은 많은 정보를 준다. 
예) string literals 대신 #selector types을 사용하는 것 등

<a name="naming"/>

## 네이밍(Naming)
읽기만 해도 유추가 가능할 정도로 설명적으로 네이밍 한다. 
[API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)의 Swift 네이밍 컨벤션을 사용한다.
**(좋은 내용이 많으니 꼭 읽어보도록)**
 
개략적인 내용:
- 사용할 때 명확하게 이해할 수 있게 작성한다
- 간결하게 하는 것보다 명확하게 만드는 것이 훨씬 더 중요하다 (줄여쓰지말고 설명적으로 작성)

- 불필요한(의미적으로 중복되는) 단어는 뺀다
  ``` swift
  // 타입이 명시 돼 있어 의미가 잘 전달되고 있는데 굳이 함수명에 중복해서 한번 더 적어줄 필요는 없다
  public mutating func remove(_ member: Element) -> Element?        ( O )
  public mutating func removeElement(_ member: Element) -> Element? ( X )
  ```
- 필요한 모든 단어들을 포함시킨다
  ``` swift
  extension List {
    public mutating func remove(at position: Index) -> Element
  }
  employees.remove(at: x) ( O )
  employees.remove(x)     ( X ) // at이 빠지면 x라는 element를 제거한다고 읽힐 수 있음
  ```
- 타입보단 역할에 기반해 네이밍한다.
  ``` swift
  var greeting = "Hello"  ( O )
  var string = "Hello"    ( X )
  ```
- 타입에 대한 정보가 부족할 땐 인자 역할이 명확히 이해되게 수정한다.
  ``` swift
  // 특히 타입이 NSObject, Any, AnyObject 이거나 Int, String 같은 fundamental type 일 경우 사용 시 의도대로 전달되지 않을 수 있다.
  func add(_ observer: NSObject, for keyPath: String)
  grid.add(self, for: graphics) // 사용할 때 이해하기 애매하다
  
  // 해당 인자의 역할을 이해할 수 있게 파라미터 명을 추가해준다
  func addObserver(_ observer: NSObject, forKeyPath path: String)
  grid.addObserver(self, forKeyPath: graphics) // 뭘 하는 건지 명확하다
  ```
  
- 함수 호출 부분을 읽었을 때 언어적으로 이해가 잘 되도록 작성한다
  ``` swift
  x.insert(y, at: z)          ( O ) -> “x, insert y at z”
  x.insert(y, position: z)    ( X ) 
  
  x.subViews(havingColor: y)  ( O ) -> “x's subviews having color y”
  x.subViews(color: y)        ( X ) 
  
  x.capitalizingNouns()       ( O ) -> “x, capitalizing nouns”
  x.nounCapitalize()          ( X ) 
  ```
- factory methods는 `make`로 시작한다, *e.g. x.makeIterator()*
- 초기화 함수(initializer)와 factory methods 호출은 첫번째 인자명을 함수명과 이어서 읽지 않는다, *e.g. x.makeWidget(cogCount: 47)*
  ``` swift
  let foreground = Color(red: 32, green: 64, blue: 128)                     ( O )
  let foreground = Color(havingRGBValuesRed: 32, green: 64, andBlue: 128)   ( X ) 
  
  let newPart = factory.makeWidget(gears: 42, spindles: 14)                 ( O )
  let newPart = factory.makeWidget(havingGearCount: 42, andSpindleCount: 14)( X ) 
  
  let ref = Link(target: destination)         ( O ) -> “x, capitalizing nouns”
  let ref = Link(to: destination)             ( X ) 
  ```
- 함수는 어떤 일을 수행하는지를 고려해 네이밍한다
  - 함수 호출로 어딘가에 영향을 받는다면(값이 바뀌거나 등) 동사형을 사용한다, *e.g. print(x), x.sort(), x.append(y).*
  - 다른 곳에 영향을 주지 않는다면 명사형을 사용한다, *e.g. x.distance(to: y), i.successor().*
  - 수행하는 일이 동사로 서술되는 함수라면 동사형을 사용하고 mutating 일 땐 -ed, -ing 를 끝에 붙인다.
  
    non-mutating | mutating
    ------------ | -------------
    x.sort()     | z = x.sorted()
    x.append(y)  | z = x.appending(y)    

  - 수행하는 일이 명사로 서술되는 함수라면 명사형을 사용하고 mutating 일 땐 form- 를 처음에 붙인다.
  
    non-mutating | mutating
    ------------ | -------------
    x = y.union(z) | y.formUnion(z)
    j = c.successor(i) | c.formSuccessor(&i)

- boolean 타입은 assertions 처럼 읽혀야 한다, *e.g. x.isEmpty, line1.intersects(line2).*
- 무언가를 설명하는 프로토콜(protocol)은 명사로 읽혀야 한다, *e.g. Collection*
- 가능한지에 대한 프로토콜(protocol)은 -able, -ible 혹은 -ing 로 끝나야 한다, *e.g. Equatable, ProgressReporting*
- 나머지 타입(type), 속성(propertie), 변수(variable), 상수(constant) 들은 모두 명사로 읽혀야 한다

- 대체 가능한 일반적이고 쉬운 용어가 있다면 전문 용어를 쓰지 않는다
  - 전문 용어를 사용해야만 뜻이 잘 전달되는 경우라면 전문 용어를 쓰되 정확한 의미에 맞게 사용한다
- 웬만하면 약자를 쓰지 않는다
- 기존에 통용되는 용어가 있다면 굳이 초보자를 위해 풀어쓰거나 쉬운 대체 용어를 쓰지 않는다

- 소유자가 없는 함수(free function)는 특별한 경우에만 사용한다
  ``` swift
  min(x, y, z) // 명백히 주체가 없을 경우
  print(x)     // 제약이 없는 제너릭 함수일 때
  sin(x)       // 함수 구문이 일반적으로 사용되는 형태와 비슷할 때
  ```
- 타입(type)과 프로토콜(protocol)은 UpperCamelCase를 사용하고 나머지엔 lowerCamelCase를 쓴다
  - 미국 영어에서 대문자 표시되는 약어 및 이니셜은 모두 대문자로 쓰거나 모두 소문자로 써야 한다.
    ``` swift
    var utf8Bytes: [UTF8.CodeUnit] // 첫 글자를 소문자로 써야한다면 모두 소문자로
    var isRepresentableAsASCII = true
    var userSMTPServer: SecureSMTPServer // userSmtpServer 처럼 첫 글자만 대문자로 쓰지 않음
    ```
- 동일한 성격의 함수는 동일한 함수명을 쓸 수 있다
  ``` swift
  extension Shape {
    /// Returns `true` iff `other` is within the area of `self`.
    func contains(_ other: Point) -> Bool { ... }

    /// Returns `true` iff `other` is entirely within the area of `self`.
    func contains(_ other: Shape) -> Bool { ... }
  }  
  ```
  - return 타입은 오버로드 하지 않는다 (동일한 함수명을 쓰며 서로 다른 타입을 리턴하지 않도록 한다)

- 설명적인 역할을 할 수 있도록, 사용 시 노출되지 않는 파라미터명일지라도 잘 작성한다
  ``` swift
  /// Replace the given `subRange` of elements with `newElements`.
  mutating func replaceRange(_ subRange: Range, with newElements: [E])  ( O )
  mutating func replaceRange(_ r: Range, with: [E])                     ( X )
  ```
- 기본 인자를 활용한다 (인자 유무에 따라 여러개의 함수를 각각 만들지 않아도 됨)
  ``` swift
  let order = lastName.compare(royalFamilyName)                                       ( O )
  let order = lastName.compare(royalFamilyName, options: [], range: nil, locale: nil) ( X )
  ```
- 기본 인자는 인자들 중 맨 마지막에 배치한다

- 함수를 구성하는 문장에 전치사구가 들어갈 때 첫번째 인자명은 전치사부터 시작한다,
  *e.g. x.removeBoxes(havingLength: 12)*
  - 예외적으로 인자들이 공통적인 전치사를 갖을 땐 함수명으로 뺀다
  ``` swift
  a.moveTo(x: b, y: c)                  ( O )
  a.move(toX: b, y: c)                  ( X )
  ```
- 첫번째 인자명이 함수를 구성하는 문장에 포함된다면 인자명을 빼고 함수에 추가한다,
  *e.g. x.addSubview(y)*
  - 그렇지 않다면(인자명을 함수명과 이어서 읽었을 때 하나의 뜻을 갖는 문장이 아닐 경우) 생략해선 안된다
  ``` swift
  view.dismiss(animated: false)   ( O )
  view.dismiss(false)             ( X ) -> dismiss 시키지 말라는건지? Bool을 dismiss 하라는건지?
  ```

- 클로저(closure)와 튜플(tuple) 인자명은 모두 명시한다

 
 
<a name="prose"/>

### 설명문(Prose)
주석과 같은 설명문에서 함수(method)를 지칭할 땐 최대한 심플한 형태로 쓴다.
1. 인자없이 함수명만. 예시: 다음, addTarget을 호출하세요.
2. 함수명과 인자명 둘다. 예시: 다음, addTarget(_:action:)을 호출하세요.
3. 함수명에 인자와 타입까지 명시. 예시: 다음, addTarget(_: Any?, action: Selector?)을 호출하세요.
 
위의 UIGestureRecognizer 을 사용하는 예시 에서는 1처럼 쓰는 것이 덜 혼동되고 선호된다.

Tip: Xcode's jump bar 에서 함수를 인자명과 함께 찾아볼 수 있다. 혹은 커서를 함수명에 놓고 Shift-Control-Option-Command-C 4개의 키를 동시에 누르면 클립보드에 함수 형태가 복사된다.


<a name="class_prefixes"/>

### 접두어(Class Prefixes)
클래스에 RW 같은 특정 접두어를 붙이지 않는다. 만약 두 이름이 같아서 헷갈리면 앞에 모듈명을 붙여 명확히 할 수 있다. 이런 경우는 거의 없으므로 굳이 모듈명을 앞에 붙이진 않는다.
``` swift
import SomeModule

let myClass = MyModule.UsefulClass()
```

<a name="delegates"/>

### 딜리게이트(Delegates)
커스텀 딜리게이트를 만들 때, 첫번째 인자는 생략 가능한 형태로 딜리게이트 소스를 사용한다. (UIKit에 다양한 예제들이 있다.)
또한 아래 예시처럼 함수명에 namePickerView를 모두 붙여줌으로써, 여러개의 딜리게이트를 사용하거나 비슷한 이름의 함수가 존재하는 경우 혼동을 줄이고
어떤 프로토콜에서 사용되는 함수인지 바로 알기 쉽다.

#### Preferred:
``` swift
func namePickerView(_ namePickerView: NamePickerView, didSelectName name: String)
func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```
#### Not Preferred:
``` swift
func didSelectName(namePicker: NamePickerViewController, name: String)
func namePickerShouldReload() -> Bool
```

<a name="use_type_inferred_context"/>

### 축약 형태 사용(Use Type Inferred Context)
컴파일러가 추론 가능한 형태로, 생략 가능한 부분은 줄여서 짧게 쓴다.

#### Preferred:
``` swift
let selector = #selector(viewDidLoad)
view.backgroundColor = .red
let toView = context.view(forKey: .to)
let view = UIView(frame: .zero)
```
#### Not Preferred:
``` swift
let selector = #selector(ViewController.viewDidLoad)
view.backgroundColor = UIColor.red
let toView = context.view(forKey: UITransitionContextViewKey.to)
let view = UIView(frame: CGRect.zero)
```

<a name="generics"/>

### 제너릭(Generics)
제너릭 타입은 설명적이고 upper camel case 형태여야 한다. 관련된 뜻이나 역할이 없을 경우 T, U, V 등의 대문자 한글자로 쓴다.

#### Preferred:
``` swift
struct Stack<Element> { ... }
func write<Target: OutputStream>(to target: inout Target)
func swap<T>(_ a: inout T, _ b: inout T)
```
#### Not Preferred:
``` swift
struct Stack<T> { ... }
func write<target: OutputStream>(to target: inout target)
func swap<Thing>(_ a: inout Thing, _ b: inout Thing)
```

<a name="language"/>

### 언어(Language)
US English 를 표준으로 사용하고 애플의 API 형식에 맞게 사용한다.

#### Preferred:
``` swift
let color = "red"
```
#### Not Preferred:
``` swift
let colour = "red"
```

<a name="code_organization"/>

## 코드 구조화(Code Organization)
익스텐션(extension)을 활용해 코드를 기능에 따라 분류한다. 분류한 각 익스텐션 마다 MARK 설정을 해준다. 
 
*// MARK: - 코드를 잘 구조화하기 위해 주석을 단다*

<a name="protocol_conformance"/>

### 프로토콜 적용(Protocol Conformance)
특히, 모델에 프로토콜을 적용시킬 땐 각 프로토콜을 종류별로 각각 extension으로 적용시킨다. 이렇게하면 관련된 코드들을 그룹화할 수 있고 추가할 때도 어디에 추가해야할지 쉽게 파악할 수 있다.

#### Preferred:
``` swift
class MyViewController: UIViewController {
  // 클래스 관련 코드들
}

// MARK: - UITableViewDataSource
extension MyViewController: UITableViewDataSource {
  // 테이블뷰의 데이터소스 함수들
}

// MARK: - UIScrollViewDelegate
extension MyViewController: UIScrollViewDelegate {
  // 스크롤뷰의 딜리게이트 함수들
}
```
#### Not Preferred:
``` swift
class MyViewController: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
  // 모든 함수들을 한 곳에
}
```

자식 클래스(derived class)에서는 부모 클래스(base class)에서 이미 적용한 프로토콜을 다시 선언하고 적용시키는 것이 불가능하기 때문에, 
부모 클래스의 그룹화된 익스텐션 코드들을 그대로 복사해 넣을 필요는 없다. 특히 자식 클래스가 터미널 클래스(terminal class)이고 오버라이드된 함수가 몇 개 없을 경우 더 그렇다. 언제 익스텐션 그룹들을 유지할지는 작성자의 재량에 달려있다.

UIKit의 뷰컨틀롤러에선 라이프사이클(lifecycle), 커스텀 접근자(accessors), IBAction 을 따로 클래스 익스텐션에 그룹화하는 것을 고려한다.

Since the compiler does not allow you to re-declare protocol conformance in a derived class, it is not always required to replicate the extension groups of the base class. This is especially true if the derived class is a terminal class and a small number of methods are being overridden. When to preserve the extension groups is left to the discretion of the author.

For UIKit view controllers, consider grouping lifecycle, custom accessors, and IBAction in separate class extensions.


<a name="unused_code"/>

### 필요없는 코드(Unused Code)
엑스코드에서 기본 작성 돼 있는 코드, 주석들을 포함해 쓰지 않는 코드들은 지운다. 사용자에게 주석된 코드를 사용하도록 알려주는 튜토리얼은 예외다.

튜토리얼과 상관없이 구현부에 단순히 부모 클래스(superclass)에 호출하는 코드만 있는 함수도 삭제한다. 사용하지 않거나 빈 UIApplicationDelegate 함수들도 마찬가지다.
 
Unused (dead) code, including Xcode template code and placeholder comments should be removed. An exception is when your tutorial or book instructs the user to use the commented code.
 
Aspirational methods not directly associated with the tutorial whose implementation simply calls the superclass should also be removed. This includes any empty/unused UIApplicationDelegate methods.

#### Preferred:
``` swift
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  return Database.contacts.count
}
```
#### Not Preferred:
``` swift
override func didReceiveMemoryWarning() {
  super.didReceiveMemoryWarning()
  // Dispose of any resources that can be recreated.
}

override func numberOfSections(in tableView: UITableView) -> Int {
  // #warning Incomplete implementation, return the number of sections
  return 1
}

override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  // #warning Incomplete implementation, return the number of rows
  return Database.contacts.count
}
```

<a name="minimal_imports"/>

### import 최소화(Minimal Imports)
꼭 필요한 모듈만 import 한다. Foundation 으로 충분한 상태인데 굳이 UIKit 을 import 하지 않는다. 마찬가지로 UIKit 을 써야하는 상황이라면 Foundation 까지 한번 더 import 하지 않는다. 

#### Preferred:
``` swift
import UIKit
var view: UIView
var deviceModels: [String]
ㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡ
import Foundation
var deviceModels: [String]
```
#### Not Preferred:
``` swift
import UIKit
import Foundation
var view: UIView
var deviceModels: [String]
ㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡ
import UIKit
var deviceModels: [String]
```

<a name="spacing"/>

## 빈공간(Spacing)
- ~~들여쓰기를 탭보다는 스페이스 2번으로 하고, Xcode 프로젝트 설정에서 탭과 들여쓰기를 2개의 스페이스로 설정하라~~ -> 굳이 기본 설정 값을 바꾸지 않는다
- if/else/switch/while 등의 { 괄호는 항상 같은 줄에서 시작하고 닫는 괄호(})는 새로운 줄에 해준다.
- Tip: 특정 코드를 선택(혹은 커맨드+A로 전체 선택)하고 컨트롤+I(혹은 메뉴에서 Editor ▸ Structure ▸ Re-Indent)를 눌러 들여쓰기를 자동으로 재적용 시킬 수 있다. 

#### Preferred:
``` swift
if user.isHappy {
  // Do something
} else {
  // Do something else
}
```
#### Not Preferred:
``` swift
if user.isHappy
{
  // Do something
}
else {
  // Do something else
}
```

- 시각적으로 명확하게 구조를 파악하기 쉽도록 함수 사이엔 한 줄을 띄운다. 함수 내의 공백은 기능에 따라 구분을 시켜줘야 하지만 너무 많은 섹션이 있을 경우는 여 함수로 리팩토링을 한다.
- 중괄호({})를 열고 난 다음 줄이나 닫기 전 줄엔 빈 줄이 없어야 한다.
- 콜론(:)의 왼쪽은 띄어쓰기 하지 않고 오른쪽은 한 칸 띄어쓴다. 삼항 연산자 ? :, 빈 딕셔너리 [:], #selector에서의 addTarget(_:action:) 등은 예외.

#### Preferred:
``` swift
class TestDatabase: Database {
  var data: [String: CGFloat] = ["A": 1.2, "B": 3.2]
}
```
#### Not Preferred:
``` swift
class TestDatabase : Database {
  var data :[String:CGFloat] = ["A" : 1.2, "B":3.2]
}
```

- 긴 줄은 70자 내외로 끝나도록 한다. 엄격하게 제한하진 않는다.
Long lines should be wrapped at around 70 characters. A hard limit is intentionally not specified.
- 마지막 줄의 끝에 공백은 피한다.
- 각 파일의 마지막 줄엔 빈 줄을 하나 추가해준다. 
Add a single newline character at the end of each file.

<a name="comments"/>

## 주석(Comments)
When they are needed, use comments to explain why a particular piece of code does something. Comments must be kept up-to-date or deleted.

Avoid block comments inline with code, as the code should be as self-documenting as possible. Exception: This does not apply to those comments used to generate documentation.

Avoid the use of C-style comments (/* ... */). Prefer the use of double- or triple-slash.

