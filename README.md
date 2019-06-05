# Swift Style Guide
iOS팀 내 협업을 위해 정의한 스위프트 코딩 스타일/규칙 문서입니다.
이와 관련해 기존의 잘 정의된 문서가 많은데 읽어보면 대부분 합리적이고 보편적인 이야기들이 적혀있고 공통적인 내용이 많았습니다.
따라서 새로 자체 문서를 만들기보단 그 중 하나를 그대로 써도 되겠다 생각했고 
해당 문서에서 빼거나 추가하고 싶은 항목을 개발팀 내에서 토론 후 편집하는 방향으로 진행하기로 했습니다.
기준이 되는 문서는 [The Official raywenderlich.com Swift Style Guide](https://github.com/raywenderlich/swift-style-guide)로 정했습니다.

## 우선순위
1. 애플의 iOS 공식 문서나 라이브러리 스타일을 최우선 순위로 한다.
2. 그 외 [기준 문서](https://github.com/raywenderlich/swift-style-guide)를 따른다.
3. 빼고 싶거나 추가할 부분은 편집해 본 문서에 반영한다.


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
