# 상태 기계(State Machine)
시스템이나 객체의 상태를 관리하고, 상태 간의 전이를 정의하는 데 사용되는 패턴. 상태 기계는 특정 상태에 있는 객체가 특정 이벤트에 반응하여 다른 상태로 전이하는 방식을 간결하고 명확하게 정의할 수 있게 한다.

## StateMachine의 주요 개념
1.	State (상태): 객체가 가질 수 있는 다양한 상태. 예를 들어, 사용자 로그인 상태를 관리하는 상태 기계에서는 “로그인됨”, “로그인되지 않음” 등이 상태가 될 수 있다.
2.	Event (이벤트): 상태를 전이시키는 트리거. 예를 들어, “로그인 버튼 클릭”, “로그아웃 버튼 클릭” 같은 것이 이벤트에 해당한ㅏ.
3.	Transition (전이): 이벤트가 발생했을 때 현재 상태에서 다른 상태로의 변환을 나타낸ㅏ. 전이는 유효한 전이(Valid)와 무효한 전이(Invalid)로 나뉠 수 있다.
4.	Side Effect (부수 효과): 상태 전이와 함께 발생할 수 있는 부수적인 작업. 예를 들어, “로그인” 상태로 전이될 때 사용자 데이터를 로드하는 작업이 부수 효과가 될 수 있다.

## 예시 
```
@HiltViewModel
class StampEventDialogViewModel @Inject constructor(
    private val claimStampEventRewardUseCase: ClaimStampEventRewardUseCase,
    private val stampEventRepository: StampEventRepository,
) : BaseViewModel() {

    private var sm = initStateMachine()
    val state = sm.getStateFlow()

    fun onClaimReward(entity: LoginStampInfoEntity) {
        sm.transition(StampEventDialogEvent.onStart(entity))
    }

    private fun claimReward(entity: LoginStampInfoEntity) {
        // 테스트용. 랜덤으로 성공 또는 실패 결과 반환.

        if (AppBuildConfig.DEBUG && isTest.value) {

            viewModelScope.launch(ioDispatcher) {
                // 결과는 랜덤 (성공 또는 실패)
                val success = stampEventRepository.testStampEventReward(entity)
                val failure =
                    IOResult.failure<LoginStampRewardDialogEntity>(ApiThrowable(message = "Error"))
                val randomResult = if (Random.nextBoolean()) success else failure
                sm.transition(
                    StampEventDialogEvent.onRequestFinished(
                        randomResult
                    )
                )
            }
        } else {
            viewModelScope.launch(ioDispatcher) {
                val result = claimStampEventRewardUseCase(entity)
                sm.transition(
                    StampEventDialogEvent.onRequestFinished(
                        result
                    )
                )
            }
        }
    }

    private fun initStateMachine(): StateMachine<
        StampEventDialogState, // Sealed interface State, Event, SideEffect
        StampEventDialogEvent, // State (ex. Initial, Start, Success, Failure)
        StampEventDialogSideEffect // Event (ex. onStart, onRequestFinished)
        > {
        return StateMachine.create {
            // 초기 상태는 Initial
            initialState(StampEventDialogState.Initial)

            // Initial 상태일 때
            state<StampEventDialogState.Initial> {
                // onStart Event가 일어났을 때
                // onCliamReward(): sm.transition(StampEventDialogEvent.onStart(entity))가 호출되어서 onStart Event가 일어남
                on<StampEventDialogEvent.onStart> {
                    // data class onStart(val entity: LoginStampInfoEntity) : StampEventDialogEvent
                    this.transitionTo(state = StampEventDialogState.Start(it.entity)) // Start 상태로 전환
                }
            }

            // Start 상태일 때
            state<StampEventDialogState.Start> {
                // onEnter: 상태에 진입했을 때
                onEnter {
                    claimReward(entity)
                    /**
                     * api 통신 결과 = result
                     * sm.transition(StampEventDialogEvent.onRequestFinished(result)) - onRequestFinished Event 발생
                     */
                }
                // onRequestFinished Event가 일어났을 때
                on<StampEventDialogEvent.onRequestFinished> { event ->
                    // 만약 result가 성공이면
                    if (event.result.isSuccess) {
                        // Success 상태로 전환
                        this.transitionTo(
                            state = StampEventDialogState.Success(
                                rewardEntity = event.result.getOrNull()!!
                            )
                        )
                    } else {
                        // 실패이면 Failure 상태로 전환
                        this.transitionTo(
                            state = StampEventDialogState.Failure(
                                throwable = event.result.exceptionOrNull()!!.asApiException()
                            )
                        )
                    }
                    // val state = sm.getStateFlow()를 통해 상태를 가져올 수 있음. 성공과 실패 시 처리는 뷰에서 LaunchedEffect 안에서.
                }
            }

            // Success 상태일 때
            state<StampEventDialogState.Success> {
                // do nothing
            }

            // Failure 상태일 때
            state<StampEventDialogState.Failure> {
                // onStart Event가 일어났을 때
                // onCliamReward(): sm.transition(StampEventDialogEvent.onStart(entity))가 호출되어서 onStart Event가 일어남
                on<StampEventDialogEvent.onStart> {
                    this.transitionTo(
                        state = StampEventDialogState.Start(it.entity)
                    )
                }
            }

            // Transition 유효하지 않을 때 로그 출력
            onTransition { transition ->
                if (transition is Transition.Valid) {
                    // nothing
                } else {
                    Logger.e("Invalid Transition ${transition.fromState} -> ${transition.event}")
                }
            }

        }
    }
}
```

## SideEffect 예시
```
sealed interface GameBadgeSideEffect {
    data class LoadMore(
        val requestIndex: Int,
        val order: GameBadgeOrderType,
    ) : GameBadgeSideEffect

    data object Finish : GameBadgeSideEffect

    data class Error(
        val error: Throwable
    ) : GameBadgeSideEffect
}
```
transitionTo에서 SideEffect를 발동할 수 있다
```
private val _sideEffect = MutableSharedFlow<GameBadgeSideEffect>()
val sideEffect = _sideEffect.asSharedFlow()

on<GameBadgeEvent.OnSaveCompleted> { event ->
                    event.result.fold(
                        onSuccess = {
                            transitionTo(
                                state = this.copy(
                                    originalDisplay = display,
                                    originalIsAutoBadgeEnabled = isAutoBadgeEnabled,
                                    isSaving = false
                                ),
                                sideEffect = GameBadgeSideEffect.Finish
                            )
                        },
                        onFailure = {
                            transitionTo(
                                this.copy(isSaving = false),
                                sideEffect = GameBadgeSideEffect.Error(it)
                            )
                        }
                    )
                }
```
그리고 onTransition에서 Transition이 valid하고 될 때마다 SideEffect를 처리
```
onTransition { transition ->
                if (transition is Transition.Valid) {
                    transition.sideEffects.map { sideEffect ->
                        when (sideEffect) {
                            is GameBadgeSideEffect.LoadMore -> {
                                loadNextPage(
                                    order = sideEffect.order,
                                    requestIndex = sideEffect.requestIndex
                                )
                            }

                            else -> {
                                // UI에서 소비하도록 함
                                viewModelScope.launch(ioDispatcher) {
                                    _sideEffect.emit(sideEffect)
                                }
                            }
                        }
                    }
                }
            }
```
