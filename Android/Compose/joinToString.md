# JoinToString()
- joinToString 함수는 Kotlin의 표준 라이브러리에서 제공하는 확장 함수로, Iterable 컬렉션의 모든 요소를 지정된 문자열 형식으로 결합하여 단일 문자열을 생성하는 데 사용된다.
- 이 함수는 Iterable<T>에 정의된 확장 함수로, 리스트나 집합 같은 컬렉션의 요소를 원하는 형식으로 연결하여 문자열로 변환할 수 있다. 함수에 전달할 수 있는 매개변수들이 있어, 원하는 형식에 맞게 요소들을 결합할 수 있다.

## 주요 매개 변수
- separator: 요소 사이에 들어갈 문자열. 기본값은 ", ".
- prefix: 결과 문자열의 앞부분에 추가될 문자열. 기본값은 빈 문자열.
- postfix: 결과 문자열의 뒷부분에 추가될 문자열. 기본값은 빈 문자열.
- limit: 표시할 최대 요소 수. 제한된 개수의 요소만 보여주고 싶다면 사용하며, 기본값은 -1로 모든 요소를 표시한다.
- truncated: 요소가 limit에 도달했을 때 남은 요소 대신 표시할 문자열. 기본값은 "..." 
- transform: 각 요소를 변환하는 데 사용하는 함수. 요소가 문자열로 변환되기 전에 가공하고자 할 때 사용.

```
fun main() {
    val list = listOf("Apple", "Banana", "Cherry", "Date", "Elderberry")

    // 기본 설정으로 joinToString 사용
    val result1 = list.joinToString()
    println(result1) // 출력: Apple, Banana, Cherry, Date, Elderberry

    // 구분자와 접두사, 접미사 지정
    val result2 = list.joinToString(separator = " | ", prefix = "[", postfix = "]")
    println(result2) // 출력: [Apple | Banana | Cherry | Date | Elderberry]

    // 제한된 수의 요소와 남은 요소에 대한 문자열 지정
    val result3 = list.joinToString(limit = 3, truncated = "등등")
    println(result3) // 출력: Apple, Banana, Cherry, 등등
    // 요소 제한: limit와 truncated 매개변수를 설정하여 최대 3개의 요소까지만 출력하고, 나머지는 "등등"으로 나타냄. 

    // 변환 함수를 사용하여 모든 요소를 대문자로 변환
    val result4 = list.joinToString(separator = " - ") { it.uppercase() }
    println(result4) // 출력: APPLE - BANANA - CHERRY - DATE - ELDERBERRY
}
```