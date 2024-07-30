# Comparable 인터페이스

Comparable 인터페이스는 객체의 자연 순서를 정의하는 메서드를 제공한다. 

- Comparable<T> 인터페이스를 구현하는 클래스는 compareTo(other: T): Int 메서드를 반드시 구현해야 한다.
- compareTo() 메서드는 두 객체의 순서를 결정하는 기준을 정의한다.

#### compareTo(other: T): Int 메서드 

이 메서드는 두 객체를 비교하여 세 가지 값을 반환할 수 있다 - 0, 양수, 또는 음수. 각각의 값은 두 객체의 상대적 순서를 나타낸다:

- 0: 두 객체가 동일한 순서에 있음을 나타낸다. 정렬 기준으로 볼 때, 이 값은 순서를 변경하지 않는다.
- 양수(1): 현재 객체가 비교 대상 객체보다 뒤에 위치해야 함을 나타낸다. 즉, 현재 객체가 리스트에서 더 뒤에 위치하게 된다.
- 음수(-1): 현재 객체가 비교 대상 객체보다 앞에 위치해야 함을 나타낸다. 즉, 현재 객체가 리스트에서 더 앞에 위치하게 된다.

#### sorted() 확장 함수

sorted()는 Kotlin에서 리스트를 정렬하기 위해 사용하는 확장 함수이다. 이 함수는 Comparable 인터페이스를 구현하는 객체들의 리스트를 compareTo 메서드를 사용하여 정렬한다.

## 예시 
```
sealed interface DailyMissionEntity {

    data class Item(
        val status: DailyMissionStatus = DailyMissionStatus.OnGoing,
        // ...
    ) : DailyMissionEntity, Comparable<Item> {
    

        override fun compareTo(other: Item): Int {
            // 완료 가능한 미션이 상단에 노출 되도록.
            return when {
                // 두 Item 객체의 status가 동일한 경우 - 순서 변경하지 않는다
                status == other.status -> 0
                // 현재 객체가 Completable 상태이고, 다른 객체가 Completable 상태가 아닌 경우 -1을 반환하여 현재 객체가 리스트에서 앞으로 오게 한다
                status == DailyMissionStatus.Completable && other.status != DailyMissionStatus.Completable -> -1
                // 현재 객체가 Completable 상태가 아니고, 다른 객체가 Completable 상태인 경우 1을 반환하여 현재 객체가 리스트에서 뒤로 가게 한다
                status != DailyMissionStatus.Completable && other.status == DailyMissionStatus.Completable -> 1
                // 두 객체 모두 Completable 상태가 아닌 경우 0을 반환하여 순서를 변경하지 않는다 
                else -> 0
            }
        }
    }
}
```
