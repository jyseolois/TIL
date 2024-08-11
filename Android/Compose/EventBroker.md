# EventBroker

## 원리
1. 리스너 등록:
- 특정 이벤트에 대해 미리 리스너를 등록해 둔다. 이 리스너는 이벤트가 발생했을 때 수행할 동작을 정의한 콜백 함수이다.
- 예를 들어, 어떤 UI 컴포넌트가 특정 이벤트에 반응해야 한다면, 그 이벤트에 대해 리스너를 등록해둔다.
2. 이벤트 발생:
- 이벤트가 발생하면, EventBroker의 dispatch 메서드를 통해 그 이벤트를 발행한다.
- 이때, 이벤트와 함께 관련된 데이터를 전달할 수도 있다.
3. 리스너 동작 수행:
- 이벤트가 발행되면, EventBroker는 해당 이벤트에 등록된 모든 리스너들을 찾아서, 그 리스너들이 미리 정의해둔 동작을 수행하게 한다.
- 예를 들어, “로그인 성공” 이벤트가 발행되면, 그 이벤트에 등록된 모든 리스너가 로그인 성공 후 수행해야 할 동작(예: 사용자 정보를 갱신하거나, 환영 메시지를 표시하는 작업 등)을 수행한다.

## 장점
- 느슨한 결합: 각 컴포넌트가 서로를 직접 호출하지 않고, EventBroker를 통해 간접적으로 소통할 수 있다. 이는 유연성을 높이고, 유지보수를 용이하게 한다.
- 확장성: 새로운 컴포넌트를 쉽게 추가할 수 있으며, 기존의 이벤트 처리 로직을 변경할 필요 없이 새로운 리스너만 등록하면 된다.
- 비동기 처리: 이벤트를 비동기적으로 처리할 수 있어, UI 스레드의 부하를 줄이고 성능을 개선할 수 있다. 

이러한 구조를 통해 애플리케이션 내에서 발생하는 다양한 이벤트를 효과적으로 관리하고 처리할 수 있다.

## 예시

1. 이벤트 정의

```
internal object MainEvent {
    /**
     * 메인화면이 띄워짐
     */
    data object Enter : Event<Unit>(name = "MainEvent.enter")
}

internal object StampEvent {
    /**
     * 스탬프 이벤트 다이얼로그 띄우기 
     */
    data object ShowStampInfoDialog : Event<LoginStampInfoEntity>(name = "StampEventInfoLoaded")
}
```

2. 리스너 등록 및 이벤트 발생 시 동작 정의 (MainActivity에 정의)
리스너 등록 (EventHandler를 통해 등록). 

이벤트를 받을 시 할 동작 정의 
```
@Composable fun <T : Any> EventHandler(event: Event<T>, onEvent: (T) -> Unit) {}
```
```
@Composable
private fun StampEventHandler() {

    val queue = LocalTaskQueue.current
    val coroutineScope = rememberCoroutineScope()
    val activity = LocalActivity.current

    EventHandler(event = StampEvent.ShowStampInfoDialog, onEvent = { stampInfo ->
        coroutineScope.launch {
            queue.addDialogForResult(
                activity = activity,
                builder = LoginStampEventDialog,
                req = LoginStampEventDialog.Req(stampInfo = stampInfo)
            ).onSuccess { rewardEntity ->

                queue.addDialogForResult(
                    activity = activity,
                    builder = PIOAssetDialogBuilder,
                    req = PIOAssetDialogData(
                        reward = rewardEntity.rewardInfo,
                        isAdLoaded = false,
                        title = rewardEntity.rewardInfo.inner().name ?: "",
                        // ....
                )
            }.onFailure { error ->

                if (error is ApiThrowable) {
                    when (handleErrors(error)) {
                        Errors.CODE_OK -> {}
                        // ….
                        }
                    }
                }

            }
        }
    })
}
```


3. 이벤트 발생시키기 (메인 액티비티 화면 진입 시)
   
```
private val eventBroker = EventBroker()

fun <T : Any> dispatch(event: Event<T>, data: T)
```
1) 메인 화면 진입 시 발생 시키기 

```
// 메인 화면 진입 시 MainEvent.Enter 이벤트 발생시키기
LaunchedEffect(Unit) {
    globalEventBroker.dispatch(MainEvent.Enter, Unit)
}

// MainViewModel에서 MainEvent.Enter 구독 및 해당 이벤트 발생 시 StampEvent 발생시키기 
viewModelScope.launch(ioDispatcher) {
    eventBroker.listenAsFlow(MainEvent.Enter).collectLatest {
        getStampEventInfoUseCase().firstOrNull()?
       .onSuccess { stampInfo ->
            val filteredStampInfo = stampInfo.takeIf {
                !it.stepCompleted && (it.step <= it.rewardList.size)
            }
            if (filteredStampInfo != null) {
                eventBroker.dispatch(StampEvent.ShowStampInfoDialog, filteredStampInfo)
            }
        }?.onFailure {
            Logger.d("failed to get stamp info: ${it.message}")
        }
    }
}
```

2) 미션 홈 텝에서 스탬프 이벤트 아이템 클릭 시 발생시키기
```
eventBroker.dispatch(StampEvent.ShowStampInfoDialog, stampEventInfo!!)
```

dispatch와 listenAsFlow()를 사용해서 간단하게 이벤트를 처리할 수도 있다.
```
eventBroker.dispatch(GameEvents.UpdateRecentResources, Unit)
```
```
  override fun subscribeObservers() {
        super.subscribeObservers()
        lifecycleScope.launch {
            repeatOnLifecycle(Lifecycle.State.RESUMED) {
                eventBroker.listenAsFlow(GameEvents.UpdateRecentResources).collect {
                    updateRecentResources(true)
                }
            }
        }
    }
```
