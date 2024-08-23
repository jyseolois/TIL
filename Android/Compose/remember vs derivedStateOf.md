# Remember(key)와 derivedStateOf의 차이

remember(key)는 key가 바뀔 때마다 리컴포지션되지만, derivedStateOf는 컴포즈 상태로 안의 최종 계산 값이 바뀌어야 리컴포지션된다.

그래서 만약 key가 end result보다 더 자주 바뀐다면, derivedStateOf를 사용하는 것이 좋다.

### 예시

```
val showScrollToTopButton by remember {
	derivedStateOf {
		state.firstVisibleItemIndex >= 5 // end. Esult
	}
}
```

```
val showScrollToTopButton = remember(state.firstVisibleItemIndex) {
	state.firstVisibleItemIndex >= 5 // end result
}
````

- remember(key)의 목적은 안의 상태가 리컴포지션을 살아남도록, 즉, 리컴포지션되도 변하지 않도록 캐싱하는 것이다.key가 달라질 때만 업데이트. 즉, key가 달라질 때마다 새로운 메모리 할당이 필요하다. 
- 반면, derivedStateOf는 end result가 바뀔 때만 새로운 메모리 할당을 필요로한다. 

### 그럼 언제 remember(key)와 derivedStateOf를 같이 써야 할까?
- 예를 들면 scrollToTopBotton을 isEnabled value가 true일 때만 활성해야 할 때.
  derivedStateOf가 처음으로 계산할 때의 상태를 모두 캡처하기 때문이다. (state.firstVIsibleItemIndex에서 사실 state는 변하지 않았고 그 property firstVisibleItemIndex만 변했기 때문에 문제가 없었지만 isEnabled은 변하기 때문)

```
Val showScrollToTOpButton = remember(isEnabled) {
	derivedStateOf {
		state.firstVisibleItemIndex >= 5 && isEnabled
	}
}
```

## 결론 
- 만약 어떤 상태가 다른 상태에 따라 변하고, 해당 상태가 end result보다 더 많이 바뀐다면 derivedStateOf를 써야 한다. 
- derivedStateOf 안의 상태가 완전히 바뀔 가능성이 있다면, remember의 key로 써야 한다.