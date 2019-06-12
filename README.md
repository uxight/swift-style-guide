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
- [클래스와 스트럭트(Classes and Structures)](#classes_and_structures)
  - [Self의 사용(Use of Self)](#use_of_self)
  - [Protocol Conformance](#protocol_conformance)
  - [Computed Properties](#computed_properties)
  - [Final](#final)
- [함수 선언(Function Declarations)](#function_declarations)
- [함수 사용(Function Calls)](#function_calls)
- [클로저 표현(Closure Expressions)](#closure_expressions)
- [타입(Types)](#types)
  - [상수(Constants)](#constants)
  - [스태틱 함수와 타입 속성 변수(Static Methods and Variable Type Properties)](#static_methods_and_variable_type_properties)
  - [옵셔널(Optionals)](#optionals)
  - [Lazy 초기화(Lazy Initialization)](#lazy_initialization)
  - [타입 추론(Type Inference)](#type_inference)
  - [문법적 편의(Syntactic Sugar)](#syntactic_sugar)
- [함수 vs 메소드(Functions vs Methods)](#functions_vs_methods)
- [메모리 관리(Memory Management)](#memory_management)
  - [객체 주기 설정(Extending object lifetime)](#extending_object_lifetime)
- [접근 제어(Access Control)](#access_control)
- [흐름 제어(Control Flow)](#control_flow)
  - [삼항 연산자(Ternary Operator)](#ternary_operator)
- [최적의 코드 배치(Golden Path)](#golden_path)
  - [Guard의 단점(Failing Guards)](#failing_guards)
- [세미콜론(Semicolons)](#semicolons)
- [괄호(Parentheses)](#parentheses)
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
필요할 땐 특정 코드가 수행하는 작업에 대해 주석을 달아 설명한다. 주석은 최신 상태를 유지하거나 지워야 한다.
 
코드는 그 자체적으로도 설명적이어야 하므로 코드내에서 주석을 다는 것을 피한다. 예외: 문서(documentation) 생성할 때 사용되는 주석.
 
~~C-style 주석(/* ... */) 사용을 피하고 // 혹은 /// 를 사용한다.~~ -> 애플 공식 문서에서도 둘다 혼용해서 쓰이므로 굳이 한 쪽을 배제하진 않는다

<a name="classes_and_structures"/>

## 클래스와 스트럭트(Classes and Structures)
### 어떤 걸 사용해야 하는가?
스트럭트(struct)는 밸류(value) 타입으로 고유한 성질을 갖을 필요가 없을 때 사용한다. a, b, c 를 포함하고 있는 [a, b, c] 라는 배열은 똑같은 원소를 포함하고 있다면 다른 배열 [a, b, c] 와 완전히 똑같다. 두 배열을 구분해야할 이유가 전혀 없고 둘 중 어느걸 쓰던 바꿔서 써도 전혀 문제가 없다. 따라서 배열처럼 이런 성질을 갖을 땐 스트럭트를 사용한다.
 
클래스(class)는 레퍼런스(reference) 타입으로 특정 주기동안 고유한 값을 갖어야 할 때 사용한다. 클래스로 사람이라는 모델을 만들 때 두 사람은 서로 고유한 다른 객체이다. 두 사람의 이름, 생일이 같더라도 같은 사람은 아니다. 그러나 사람이라는 모델 안에 있는 생일이라는 속성은 스트럭트이다. 1950년 3월 3일 이라는 날짜 값이 있다 할 때 같은 날짜의 다른 값과 구분되어야 하는 경우는 없다. 날짜는 그 자체로 고유한 성질을 갖을 필요가 없다.
 
가끔 스트럭트로 만들어야 하는데 AnyObject 를 상속해야해서 어쩔 수 없거나 이미 클래스로 만들어진 경우(NSDate, NSSet)가 있다. 최대한 가이드라인을 따르려고 노력한다.

### 예제 정의
아래 잘 정의된 클래스 스타일 예제이다.
``` swift
class Circle: Shape {
  var x: Int, y: Int
  var radius: Double
  var diameter: Double {
    get {
      return radius * 2
    }
    set {
      radius = newValue / 2
    }
  }

  init(x: Int, y: Int, radius: Double) {
    self.x = x
    self.y = y
    self.radius = radius
  }

  convenience init(x: Int, y: Int, diameter: Double) {
    self.init(x: x, y: y, radius: diameter / 2)
  }

  override func area() -> Double {
    return Double.pi * radius * radius
  }
}

extension Circle: CustomStringConvertible {
  var description: String {
    return "center = \(centerString) area = \(area())"
  }
  private var centerString: String {
    return "(\(x),\(y))"
  }
}
```
 
위의 예제는 아래 스타일 가이드라인이 적용 돼 있다:

- 속성, 변수, 상수, 인자 등의 선언은 콜론(:) 다음 한 칸의 공백을 띄우되 앞엔 공백이 없어야 한다, e.g. x: Int, and Circle: Shape.
- 동일한 목적이나 맥락의 변수 등은 한 줄에 선언한다.
- 게터와 세터 정의부나 속성 옵저버(observer) 들여쓰기.
- internal 과 같은 기본 속성은 굳이 붙여주지 않는다. 비슷하게 오버라이드한 함수의 접근 제어자(access modifier)를 반복해서 적지 않는다.
- 부가적인 기능은 익스텐션에 따로 빼서 구성한다(e.g. printing)
- centerString 처럼 공유될 필요가 없는 세부 구현부는 익스텐션에 프라이빗(private access control)으로 구현한다.

<a name="use_of_self"/>

## Self의 사용(Use of Self)
~~간결함을 위해, 함수나 변수에 접근하 때 self 없이도 가능하다면 self는 생략한다.~~
 
~~self는 컴파일러단에서 필수적일 때만 사용한다(@escaping 클로저(closures)안 혹은 초기화 함수 내에서 속성과 인자의 명확한 구분을 위해). 즉 없이써도 컴파일 에러가 없다면 생략한다.~~
 
->
- 변수명이 짧거나 일반적인 단어라 변수 외의 용도로 사용 가능성이 높을 경우, 코드의 추적을 위해 변수를 검색할 때 어려울 수 있다.
- 안드로이드에서 멤버 변수 앞엔 mVar식으로 m-을 앞에 붙이는 네이밍처럼, self.- 를 붙여서 사용하면 멤버 변수임을 시각적으로 더 잘알 수 있다.

<a name="protocol_conformance"/>

## 프로토콜 적용(Protocol Conformance)
간결함을 위해, 연산 속성(computed property)이 read-only일 땐 get 구문을 제거한다. get 구문은 set 구문이 함께 있을 때만 필수적이다.

#### Preferred:
``` swift
var diameter: Double {
  return radius * 2
}
```
#### Not Preferred:
``` swift
var diameter: Double {
  get {
    return radius * 2
  }
}
```

<a name="final"/>

## 파이널(Final)
클래스나 멤버를 파이널(final)로 만드는 것은 튜토리얼에서 전달하려는 핵심을 분산시킬 수 있고 필수적이지 않다. 그럼에도 불구하고 파이널을 사용하는 것이 가끔은 구현 의도를 명확하 할 수 있고 투자할 가치가 있는 작업이다. 아래 예제에서, Box는 특정한 목적으로 만든 것이므로 상속하거나 바꿔서 사용하는 것을 막아야 한다. 파이널을 사용할 경우 이런 용도를 명확하게 할 수 있다.

``` swift
// Turn any generic type into a reference type using this Box class.
final class Box<T> {
  let value: T
  init(_ value: T) {
    self.value = value
  }
}
```

<a name="function_declarations"/>

## 함수 선언(Function Declarations)
중괄호({)를 열 때 함수 선언부 한 줄 안에서 열고, 짧은 형태 유지한다:
``` swift
func reticulateSplines(spline: [Double]) -> Bool {
  // reticulate code goes here
}
```
긴 함수의 경우 각 인자를 새로운 줄에서 들여쓰기 후에 시작한다.
``` swift
func reticulateSplines(
  spline: [Double], 
  adjustmentFactor: Double,
  translateConstant: Int, comment: String
) -> Bool {
  // reticulate code goes here
}
```
인자가 없을 땐 (Void)를 사용하지 않고 ()로 표시한다. 클로저나 함수의 리턴은 () 대신 Void를 사용한다.
#### Preferred:
``` swift
func updateConstraints() {
  // magic happens here
}

typealias CompletionHandler = (result) -> Void
```
#### Not Preferred:
``` swift
func updateConstraints() -> () {
  // magic happens here
}

typealias CompletionHandler = (result) -> ()
```
> 문서와 다르게, 함수 선언 시 리턴이 Void 일 경우 생략 가능하도록 정했다. 
함수 선언에 한해서는 [구글 문서](https://google.github.io/swift/#line-wrapping)가 더 잘 돼 있어 이것을 따르기로 했다.
#### Preferred:
``` swift
public func index<Elements: Collection, Element>(
  of element: Element,
  in collection: Elements
) -> Elements.Index?
where
  Elements.Element == Element,
  Element: Equatable
{  // GOOD.
  for current in elements {
    // ...
  }
}
```
#### Not Preferred:
``` swift
public func index<Elements: Collection, Element>(
  of element: Element,
  in collection: Elements
) -> Elements.Index?
where
  Elements.Element == Element,
  Element: Equatable {  // AVOID.
  for current in elements {
    // ...
  }
}

public func index<Elements: Collection, Element>(of element: Element,  // AVOID.
                                                 in collection: Elements) -> Elements.Index?
    where Elements.Element == Element, Element: Equatable {
  doSomething()
}
```

<a name="function_calls"/>

## 함수 사용(Function Calls)
호출할 때도 선언부와 비슷하게 사용한다. 한줄짜리 함수는 아래와 같이 사용한다:
``` swift
let success = reticulateSplines(splines)
```
만약 한 줄이 넘어간다면 각 인자를 새로운 줄에 들여쓰기해서 호출한다:
``` swift
let success = reticulateSplines(
  spline: splines,
  adjustmentFactor: 1.3,
  translateConstant: 2,
  comment: "normalize the display"
  )
```
> 해당 문서에선 인자가 끝나는 마지막줄에 괄호를 닫았는데 선언부와 일치시켜서 사용하기 위해 다음 줄에 적기로 정했다. (둘다 허용)
함수 사용도 선언과 마찬가지로 [구글 문서](https://google.github.io/swift/#function-calls)가 더 잘 돼 있어 이것을 따르기로 했다.

<a name="closure_expressions"/>

## 클로저 표현(Closure Expressions)
~~마지막 클로저 인자 축약 표현(trailing closure syntax)은 인자가 하나일 때만 사용한다.~~ 클로저 인자의 성격을 파악하 쉽게 인자 이름을 모두 붙인다.
> 생소한 함수이거나, 애플의 API 가 아니고 사용자가 만든 함수일 경우 다른 사람이 읽을 땐 인자의 성격에 대한 파악이 힘들 수 있다. 마지막 클로저 인자 축약 표현(trailing closure syntax)은 최대한 지양하기로 했다.

#### Preferred:
``` swift
UIView.animate(withDuration: 1.0, animations: {
  self.myView.alpha = 0
}, completion: { finished in
  self.myView.removeFromSuperview()
})
```
#### Not Preferred:
``` swift
UIView.animate(withDuration: 1.0, animations: {
  self.myView.alpha = 0
}) { f in
  self.myView.removeFromSuperview()
}
```

클로저 표현식에서 맥락이 확실하다면 return 은 생략한다:
``` swift
attendeeList.sort { a, b in
  a > b
}
```
함수 연쇄 호출 시 trailing closures 는 읽기 쉽고 명확해야 한다. 띄어쓰기나 줄바꿈, 인자 생략의 사용과 시기는 작성자의 재량에 달려 있다. 
예시:
``` swift
let value = numbers.map { $0 * 2 }.filter { $0 % 3 == 0 }.index(of: 90)

let value = numbers
  .map {$0 * 2}
  .filter {$0 > 50}
  .map {$0 + 10}
```
> map 등의 함수에서 괄호 안의 코드는 가독성을 위해 한 칸씩 공백을 두기로 했다:
``` swift
let value = numbers.map({ $0 * 2 })    ( O )
let value = numbers.map({$0 * 2})      ( X )
```

<a name="types"/>

## 타입(Types)
스위프트(swift)는 오브젝티브씨(Objective-C) 브릿징을 제공해 여전히 사용할 수 있지만 최대한 스위프트 타입과 표현을 그대로 사용한다.

#### Preferred:
``` swift
let width = 120.0                                    // Double
let widthString = "\(width)"                         // String
```
#### Less Preferred:
``` swift
let width = 120.0                                    // Double
let widthString = (width as NSNumber).stringValue    // String
```
#### Not Preferred:
``` swift
let width: NSNumber = 120.0                          // NSNumber
let widthString: NSString = width.stringValue        // NSString
```
드로잉 코드같은 경우, `CGFloat`을 사용하면 많은 변환을 안하고 간결하게 쓸 수 있다.

<a name="constants"/>

### 상수(Constants)
상수는 let 으로 선언하고 변수는 var 을 쓴다. 변수의 값이 변경되지 않는다면 무조건 let 을 쓴다.
 
Tip: 컴파일 에러가 날 때만 var 을 쓰고 나머지는 모두 let 을 쓰면 쉽다.
 
타입 내에서 static let 을 사용해 타입 속성으로 상수를 선언할 수 있다. 이렇게 선언할 경우 글로벌 상수로 선언했을 때보다 인스턴트 속성과 쉽게 구별된다.
#### Preferred:
``` swift
enum Math {
  static let e = 2.718281828459045235360287
  static let root2 = 1.41421356237309504880168872
}

let hypotenuse = side * Math.root2 // 여러 루트 중, 수학의 루트 한가지 뜻으로만 해석한다.
```
**Note:** case 없이 사용하는 enum 을 상수를 모아두는 타입명(네임스페이스)으로 쓸 경우, 클래스나 스트럭트 등을 썼을 때 처럼 초기화하거나해서 다른 용도로 잘못 사용될 여지가 없어 순수하게 변수만을 담는 용도라고 이해하기 쉽다.
#### Not Preferred:
``` swift
let e = 2.718281828459045235360287  // global namespace가 난잡해진다.
let root2 = 1.41421356237309504880168872

let hypotenuse = side * root2 // root2가 뭘 의미하는지 명확하지 않다.
```

<a name="static_methods_and_variable_type_properties"/>

### 스태틱 함수와 타입 속성 변수(Static Methods and Variable Type Properties)
스태틱 함수와 타입 속성 역시 글로벌 함수, 변수와 비슷하게 꼭 필요할 때만 쓴다. 기능적으로 특정 타입 범위내에서 적용한다던가 오브젝티브씨와 호환되어야할 때 유용하다.

<a name="optionals"/>

### 옵셔널(Optionals)
변수와 함수의 리턴 타입이 `nil` 이 될 수 있다면 옵셔널 `?` 로 선언한다.
 
`!` 를 사용해 강제 언랩하는(implicitly unwrapped) 형태는 확실할 때만 사용한다. ~~`viewDidLoad` 에서 할당시키는 서브뷰들처럼 사용 전에 할당되는 것이 확실한 경우를 제외하고 나머지는 옵셔널 바인딩(optional binding)을 사용해 안전하게 쓴다.~~
> 애초에 개발자는 에러가 나지 않을 것이라고 판단하고 코딩하지만 에러가 나는 것처럼, 사용 전에 할당될 것이 확실하다는 판단은 자의적이다. IBOutlet 처럼 기본이 아니라면 최대한 사용하 않는다.
 
옵셔널 밸류에 접근할 때 단 한번만 사용하거나 옵셔널이 중첩되어 있다면 옵셔널 체이닝(optional chaining)을 사용한다:
``` swift
textContainer?.textLabel?.setNeedsDisplay()
```

옵셔널을 벗긴(unwrap) 후 여러번 사용해야 한다면 옵셔널 바인딩을 사용하는 것이 편리하다:
``` swift
if let textContainer = textContainer {
  // do many things with textContainer
}
```

옵셔널 변수나 속성은 타입에서 이미 명시 돼 있으므로 네이밍할 때 `optionalString` 혹은 `maybeView` 식으로 이름에 뜻을 내포시키지 않는다.
 
옵셔널 바인딩 할 땐 `unwrappedView` 혹은 `actualLabel` 같은 변수명으로 받기보단 원래의 이름 그대로 받는다.

#### Preferred:
``` swift
var subview: UIView?
var volume: Double?

// later on...
if let subview = subview, let volume = volume {
  // do something with unwrapped subview and volume
}

// another example
UIView.animate(withDuration: 2.0) { [weak self] in
  guard let self = self else { return }
  self.alpha = 1.0
}
```
#### Not Preferred:
``` swift
var optionalSubview: UIView?
var volume: Double?

if let unwrappedSubview = optionalSubview {
  if let realVolume = volume {
    // do something with unwrappedSubview and realVolume
  }
}

// another example
UIView.animate(withDuration: 2.0) { [weak self] in
  guard let strongSelf = self else { return }
  strongSelf.alpha = 1.0
}
```

<a name="lazy_initialization"/>

### Lazy 초기화(Lazy Initialization)
객체에 특정한 주기 설계가 필요한 경우 lazy 초기화를 사용한다. 특히 `UIViewController` 에서 뷰들을 lazy 하게 로드할 때 좋다. 호출 시 수행하는 `{ }()` 와 같은 클로저를 사용하거나 private 생성 함수(factory method)를 사용한다. 예:
``` swift
lazy var locationManager = makeLocationManager()

private func makeLocationManager() -> CLLocationManager {
  let manager = CLLocationManager()
  manager.desiredAccuracy = kCLLocationAccuracyBest
  manager.delegate = self
  manager.requestAlwaysAuthorization()
  return manager
}
```
#### Notes:
- `[unowned self]` 는 여기서 필요하지 않음. 리테인 사이클이 생성되지 않음.
- 위의 Location manager 같은 경우 위치 사용 권한을 사용자에게 묻는 팝업이 뜨므로 이런 경우엔 lazy를 사용해 시점을 조정하면 좋다.

<a name="type_inference"/>

### 타입 인터페이스(Type Inference)
~~컴파일러가 변수나 상수의 타입을 추론할 수 있다면 코드를 간결하게 한다. 타입 추론(Type inference)은 작고 빈 배열, 딕셔너리에도 적합하다. 필요하다면 `CGFloat` 나 `Int16` 은 써준다.~~
> 멤버 변수를 선언할 땐, 일관성 있고 가독성이 좋게 타입을 항상 명시해주기로 정했다.

#### Preferred:
``` swift
let message = "Click the button"
let currentBounds = computeViewBounds()
var names = ["Mic", "Sam", "Christine"]
let maximumWidth: CGFloat = 106.5
```
#### Not Preferred:
``` swift
let message: String = "Click the button"
let currentBounds: CGRect = computeViewBounds()
var names = [String]()
```

#### 빈 배열과 딕셔너리를 위한 타입 주석(Type Annotation)
빈 배열과 딕셔너리는 타입 주석을 사용한다. (크고 여러줄의 리터럴이 들어가는 배열이나 딕셔너리엔 타입 주석을 사용한다.)
For empty arrays and dictionaries, use type annotation. (For an array or dictionary assigned to a large, multi-line literal, use type annotation.)
#### Preferred:
``` swift
var names: [String] = []
var lookup: [String: Int] = [:]
```
#### Not Preferred:
``` swift
var names = [String]()
var lookup = [String: Int]()
```
**NOTE:** 이 가이드라인을 따를 경우 설명적인 네이밍이 더 중요해진다.

<a name="syntactic_sugar"/>

### 문법적 편의(Syntactic Sugar)
긴 문법보다는 짧은 버전을 선호한다.

#### Preferred:
``` swift
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```
#### Not Preferred:
``` swift
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```

<a name="functions_vs_methods"/>

## 함수 vs 메소드(Functions vs Methods)
클래스나 타입에 속해있지 않은 함수(Free function)는 최대한 쓰지 않으려고 한다. 가능하면 가독성이 높아지고 추적하 쉽도록 메소드(method)가 소속될 곳을 정한다.
 
특정 타입이나 인스턴스와 연관성이 전혀 없는 경우에만 Free function 을 사용한다.

#### Preferred:
``` swift
let sorted = items.mergeSorted()  // easily discoverable
rocket.launch()  // acts on the model
```
#### Not Preferred:
``` swift
let sorted = mergeSort(items)  // hard to discover
launch(&rocket)
```
#### Free Function Exceptions:
``` swift
let tuples = zip(a, b)  // feels natural as a free function (symmetry)
let value = max(x, y, z)  // another free function that feels natural
```

<a name="memory_management"/>

## 메모리 관리(Memory Management)
실제 프로젝트 아니라 데모 코드일지라도 순환 참조를 만들지 않는다. 객체 그래프를 분석하고 `weak` and `unowned` 참조로 강한 순환 참조를 방지한다. 또는 밸류타입(`struct`, `enum`)을 사용하여 순환 참조를 방지하는 방법도 있다.
Code (even non-production, tutorial demo code) should not create reference cycles. Analyze your object graph and prevent strong cycles with `weak` and `unowned` references. Alternatively, use value types (`struct`, `enum`) to prevent cycles altogether.

<a name="extending_object_lifetime"/>

### 객체 주기 설정(Extending object lifetime)
`[weak self]` 와 `guard let self = self else { return }` 구문을 사용해 객체 주기를 설정한다. `self` 가 클로저 바깥에서 해제될 수도 있는 경우엔 `[unowned self]` 보다 `[weak self]` 를 선호한다. 옵셔널 체이닝은 지양한다. 
Explicitly extending lifetime is preferred to optional chaining.

#### Preferred:
``` swift
resource.request().onComplete { [weak self] response in
  guard let self = self else {
    return
  }
  let model = self.updateModel(response)
  self.updateUI(model)
}
```
#### Not Preferred:
``` swift
// 응답이 오는 시점에 self 가 이미 해제 되어 있다면 crash 가 난다
resource.request().onComplete { [unowned self] response in
  let model = self.updateModel(response)
  self.updateUI(model)
}
```
#### Not Preferred:
``` swift
// model 업데이트와 UI 업데이트 사이에 해제될 수 있음
resource.request().onComplete { [weak self] response in
  let model = self?.updateModel(response)
  self?.updateUI(model)
}
```

<a name="access_control"/>

## 접근 제어(Access Control)
전체 접근 가능한 annotation(Full access control annotation)은 튜토리얼에서 전달하려는 주제를 분산시킬 수 있고 필수적이지도 않다. `private` 과 `fileprivate` 을 적절하게 사용해 명확하게 하고 캡슐화(encapsulation)를 하기 좋다. `fileprivate` 보다는 `private` 을 선호하고 `fileprivate` 은 컴파일러가 제안할 때만 쓴다.
 
`open`, `public`, `internal` 은 전체 접근 권한이 필요할 때 명시적으로 사용한다.

접근 제어는 속성을 지정할 때 맨 앞에 쓰고 이것볻 앞에 쓸 수 있는 속성은 `static` 이나 `@IBAction`, `@IBOutlet`, `@discardableResult` 뿐이다.

#### Preferred:
``` swift
private let message = "Great Scott!"

class TimeMachine {  
  private dynamic lazy var fluxCapacitor = FluxCapacitor()
}
```
#### Not Preferred:
``` swift
fileprivate let message = "Great Scott!"

class TimeMachine {  
  lazy dynamic private var fluxCapacitor = FluxCapacitor()
}
```

<a name="control_flow"/>

## 흐름 제어(Control Flow)
`for` 문에서 `while-condition-increment` 보다 `for-in` 스타일을 선호한다.

#### Preferred:
``` swift
for _ in 0..<3 {
  print("Hello three times")
}

for (index, person) in attendeeList.enumerated() {
  print("\(person) is at position #\(index)")
}

for index in stride(from: 0, to: items.count, by: 2) {
  print(index)
}

for index in (0...3).reversed() {
  print(index)
}
```
#### Not Preferred:
``` swift
var i = 0
while i < 3 {
  print("Hello three times")
  i += 1
}


var i = 0
while i < attendeeList.count {
  let person = attendeeList[i]
  print("\(person) is at position #\(i)")
  i += 1
}
```

<a name="ternary_operator"/>

### 삼항 연산자(Ternary Operator)
삼항연산자 `?:` 는 코드를 명확하게 하거나 깔끔하게 만들어주는 경우에만 사용한다. 보통 여러 조건의 분기가 들어가는 경우엔 `if` 문이나 인스턴스 변수로 따로 빼서 리팩토링하는 것이 더 이해하기 쉽다. 일반적으로 어떤 값을 사용할지 결정하고 변수를 할당하는 경우가 삼항 연산자 사용에 가장 적합하다.

#### Preferred:
``` swift
let value = 5
result = value != 0 ? x : y

let isHorizontal = true
result = isHorizontal ? x : y
```
#### Not Preferred:
``` swift
result = a > b ? x = c > d ? c : d : y
```

<a name="golden_path"/>

## 최적의 코드 배치(Golden Path)
조건문으로 코딩할 때 코드의 왼쪽 여백이 복잡하게 중첩된 울퉁불퉁한 공간을 만들지 않도록 한다. `if` 문을 중첩하지 않는다. 여러개의 return 구문은 괜찮다. `guard` 문은 이런 경우를 위해 만들어졌다.

#### Preferred:
``` swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {

  guard let context = context else {
    throw FFTError.noContext
  }
  guard let inputData = inputData else {
    throw FFTError.noInputData
  }

  // use context and input to compute the frequencies
  return frequencies
}
```
#### Not Preferred:
``` swift
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {

  if let context = context {
    if let inputData = inputData {
      // use context and input to compute the frequencies

      return frequencies
    } else {
      throw FFTError.noInputData
    }
  } else {
    throw FFTError.noContext
  }
}
```
여러개의 옵셔널을 `guard` 나 `if let` 을 사용해 언랩(unwrapped)할 때 구문의 중첩 사용을 최소화하고 변수 사이에 `,` 를 사용해 하나의 구문안에서 해결한다. 이 때, `guard` 다음 변수(혹은 조건)을 선언할 땐 새로운 줄에 들여쓰기를 한 후 시작하고 `else` 는 다음 줄에 새로 적는다. 예제:
#### Preferred:
``` swift
guard 
  let number1 = number1,
  let number2 = number2,
  let number3 = number3 
  else {
    fatalError("impossible")
}
// do something with numbers
```
#### Not Preferred:
``` swift
if let number1 = number1 {
  if let number2 = number2 {
    if let number3 = number3 {
      // do something with numbers
    } else {
      fatalError("impossible")
    }
  } else {
    fatalError("impossible")
  }
} else {
  fatalError("impossible")
}
```

<a name="failing_guards"/>

### Guard의 단점(Failing Guards)
`Guard` 구문은 로직을 타지 않고 중간에 끝내야할 때 사용된다. 일반적으로 `return`, `throw`, `break`, `continue`, `fatalError()` 처럼 한 줄로 되어야 하며 길고 복잡한 코드는 피해야 한다. 만약 여러개의 탈출 코드가 있고 중복되는 코드들이 생기는 경우라면 깔끔한 코드를 위해 `defer` 사용을 고려한다

<a name="semicolons"/>

## 세미콜론(Semicolons)
스위프트는 코드가 끝나는 지점을 구분하기 위해 세미콜론이 필요하지 않다. 세미콜론은 한 줄에 여러개의 (수행을 하는) 코드를 적고 싶을 때만 사용한다.
  
세미콜론을 사용하여 여러개의 코드를 한 줄에 적지 않는다.

#### Preferred:
``` swift
let swift = "not a scripting language"
```
#### Not Preferred:
``` swift
let swift = "not a scripting language";
```
**NOTE:** 스위프트는 세미콜론이 없으면 안전하지 않다고 생각하는 자바스크립트와 다르다.

<a name="parentheses"/>

## 괄호(Parentheses)
괄호를 조건문 양 옆에 붙일 필요 없으므로 사용하지 않는다.

#### Preferred:
``` swift
if name == "Hello" {
  print("World")
}
```
#### Not Preferred:
``` swift
if (name == "Hello") {
  print("World")
}
```
표현이 길어질 경우 괄호는 때때로 코드를 더 명확하고 읽기 쉽게 해준다.
#### Preferred:
``` swift
let playerMark = (player == current ? "X" : "O")
```

