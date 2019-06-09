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
  - Prose
  - [딜리게이트(Delegates)](#delegates)
  - Use Type Inferred Context
  - Generics
  - Class Prefixes
  - Language
- Code Organization
  - Protocol Conformance
  - Unused Code
  - Minimal Imports
- Spacing
- Comments
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

 
 
<a name="delegates"/>

### Delegates
When creating custom delegate methods, an unnamed first parameter should be the delegate source. (UIKit contains numerous examples of this.)

#### Preferred:
``` swift
func namePickerView(_ namePickerView: NamePickerView, didSelectName name: String)
func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```
#### Not Preferred:
``` swift
func namePickerView(_ namePickerView: NamePickerView, didSelectName name: String)
func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```
