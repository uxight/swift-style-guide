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
  - Delegates
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
최대한 워닝이 나지 않도록 한다. 이 규칙은 많은 정보를 준다. 
예) string literals 대신 #selector types을 사용하는 것 등

<a name="naming"/>

## 네이밍(Naming)
읽기만 해도 유추가 가능할 정도로 설명적으로 네이밍 한다. 
[API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)의 Swift 네이밍 컨벤션을 사용한다.
대략적이 요약 내용:
- 사용할 때 명확하게 이해할 수 있게 작성한다
- 간결하게 하는 것보다 명확하게 만드는 것이 훨씬 더 중요하다
- 카멜(camel) 케이스를 사용한다 (snake case 금지)
- 타입(type)과 프로토콜(protocol)은 UpperCamelCase를 사용하고 나머지엔 lowerCamelCase를 쓴다
  ``` swift
  var utf8Bytes: [UTF8.CodeUnit]
  ```
- 불필요한(의미적으로 중복되는) 단어를 빼고 필요한 모든 단어들을 포함시킨다
  ``` swift
  public mutating func removeElement(_ member: Element) -> Element? ( X )
  public mutating func remove(_ member: Element) -> Element?        ( O )
  ```
- 타입보단 역할에 기반해 네이밍한다.
  ``` swift
  var string = "Hello"    ( X )
  var greeting = "Hello"  ( O )
  ```
- 타입에 대한 정보가 부족할 땐 인자 역할이 명확히 이해되게 수정한다.
  ``` swift
  // 특히 타입이 NSObject, Any, AnyObject 이거나 Int, String 같은 fundamental type 일 경우 사용 시 의도대로 전달되지 않을 수 있다.
  func add(_ observer: NSObject, for keyPath: String)
  grid.add(self, for: graphics) // 사용할 때 이해하기 애매하다
  
  // 해당 인자의 역할을 이해할 수 있게 파라미터 명을 추가해준다
  func addObserver(_ observer: NSObject, forKeyPath path: String)
  grid.addObserver(self, forKeyPath: graphics) // clear
  ```
- 언어적으로 잘 읽히고 쓰일 수 있게 작성하려 노력한다(striving for fluent usage)
  ``` swift
  x.insert(y, at: z)          ( O ) -> “x, insert y at z”
  x.insert(y, position: z)    ( X ) 
  
  x.subViews(havingColor: y)  ( O ) -> “x's subviews having color y”
  x.subViews(color: y)        ( X ) 
  
  x.capitalizingNouns()       ( O ) -> “x, capitalizing nouns”
  x.nounCapitalize()          ( X ) 
  ```
- factory methods는 `make`로 시작한다 e.g. x.makeIterator()
- naming methods for their side effects
  - verb methods follow the -ed, -ing rule for the non-mutating version
  - noun methods follow the formX rule for the mutating version
  - boolean types should read like assertions
  - protocols that describe what something is should read as nouns
  - protocols that describe a capability should end in -able or -ible

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
