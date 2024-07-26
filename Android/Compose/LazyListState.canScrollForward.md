# LazyListState.canScrollForward (+derivedStateOf)

### LazyList에서 더 이상 스크롤 할 수 없을 때를 구분하는 방법
- LazyList에서 더 이상 스크롤 할 수 없을 때 (목록 끝까지 스크롤 했을 때)를 알 수 있는 방법

```
val lazyListState = rememberLazyListState()

val isScrollEnd by remember(uiState.games.size) {
    derivedStateOf {
        !lazyListState.canScrollForward
    }
}
```

- **DerivedStateOf**   
derivedStateOf는 상태가 변화할 때만 다시 계산되며, 이는 불필요한 계산을 줄이고 성능을 향상시킨다.
lazyListState.canScrollForward를 직접 사용하는 대신 derivedStateOf로 감싸서 isScrollEnd가 LazyList의 스크롤 가능 여부가 변경될 때만 재계산되도록 함으로써,
컴포저블이 불필요하게 다시 렌더링되는 것을 방지할 수 있다.


***uiState.games.size가 변경되면***:  
remember 블록이 다시 실행되어 derivedStateOf가 재정의된다.   
이 과정에서 derivedStateOf 블록 내의 코드가 다시 계산된다.   
***lazyListState.canScrollForward가 변경되면***:    
derivedStateOf 블록이 다시 계산된다.


`derivedStateOf`는 특정 상태가 다른 상태에 따라 동적으로 변해야 할 때 유용하다. `derivedStateOf`를 사용하면 성능을 최적화할 수 있으며, 불필요한 재구성을 피할 수 있다.

```kotlin
@Composable
fun Example() {
    var count by remember { mutableStateOf(0) }
    val isEven by derivedStateOf { count % 2 == 0 }

    Column {
        Text(text = "Count: $count")
        Text(text = "Is even: $isEven")
        Button(onClick = { count++ }) {
            Text("Increment")
        }
    }
}
```

이 예시에서 `count`가 증가할 때마다 `isEven` 값이 자동으로 갱신된다. `isEven`은 `derivedStateOf`에 의해 `count`의 변경 사항에 따라 재계산되지만, `count`가 변경되지 않으면 `isEven`도 재계산되지 않는다. 이는 성능 최적화에 도움이 된다.

따라서, `derivedStateOf`는 상태 변화에 따라 자동으로 업데이트되는 값을 계산할 때 매우 유용하다.