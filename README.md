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
  - [Delegates](#delegates)
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
개략적인 내용:
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
  grid.addObserver(self, forKeyPath: graphics) // 뭘 하는 건지 명확하다
  ```
- 언어적으로 잘 읽히고 쓰일 수 있게 작성하려 노력한다 (striving for fluent usage)
  ``` swift
  x.insert(y, at: z)          ( O ) -> “x, insert y at z”
  x.insert(y, position: z)    ( X ) 
  
  x.subViews(havingColor: y)  ( O ) -> “x's subviews having color y”
  x.subViews(color: y)        ( X ) 
  
  x.capitalizingNouns()       ( O ) -> “x, capitalizing nouns”
  x.nounCapitalize()          ( X ) 
  ```
- factory methods는 `make`로 시작한다 e.g. x.makeIterator()
- 함수는 어떤 일을 수행하는지를 고려해 네이밍한다
  - 함수 호출로 어딘가에 영향을 받는다면(값이 바뀌거나) 동사형을 사용한다, e.g. print(x), x.sort(), x.append(y).
  - 다른 곳에 영향을 주지 않는다면 명사형을 사용한다, e.g. x.distance(to: y), i.successor().
  - 수행하는 일이 동사로 서술되는 함수라면 동사형을 사용하고 mutating 일 땐 -ed, -ing 를 끝에 붙인다.
    ``` swift
    //[non-mutating]    <->     [mutating]
    x.sort()            <->	    z = x.sorted()
    x.append(y)         <->	    z = x.appending(y)    
    ```
  - 수행하는 일이 명사로 서술되는 함수라면 명사형을 사용하고 mutating 일 땐 from- 를 처음에 붙인다.
    ``` swift
    //[non-mutating]      <->     [mutating]
    x = y.union(z)        <->     y.formUnion(z)
    j = c.successor(i)    <->     c.formSuccessor(&i)
    ```
  - boolean 타입은 assertions 처럼 읽혀야 한다, e.g. x.isEmpty, line1.intersects(line2).
  - 무언가를 설명하는 protocol은 명사로 읽혀야 한다, e.g. Collection
  - 가능한지에 대한 protocol은 -able, -ible 혹은 -ing 로 끝나야 한다, e.g. Equatable, ProgressReporting
  - 나머지 type, propertie, variable, and constant 들은 모두 명사로 읽혀야 한다
- 전문가나 초보자 모두에게 쉬운 용어를 사용한다
- 웬만하면 약자를 쓰지 않는다
- 기존에 쓰이는 용어를 그대로 쓰지 않고, 초보자를 위해 풀어쓰거나 쉬운 대체 용어를 쓰지 않는다
- 자유 함수(free function)는 특별한 경우에만 사용한다
  ``` swift
  min(x, y, z) // 명백히 주체가 없을 경우
  
  print(x) // 일반적으로 제약이 없는 함수일 때
  
  sin(x) // 함수 구문이 설정된 도메인 표기법의 일부일 때(When function syntax is part of the established domain notation)
  ```
- casing acronyms and initialisms uniformly up or down
- giving the same base name to methods that share the same meaning
- avoiding overloads on return type
- choosing good parameter names that serve as documentation
- preferring to name the first parameter instead of including its name in the method name, except as mentioned under Delegates
- labeling closure and tuple parameters
- taking advantage of default parameters


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
