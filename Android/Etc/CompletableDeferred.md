# CompletableDeferred

- CompletableDeferred<T>는 Kotlin 코루틴에서 사용하는 비동기 작업의 결과를 나타내는 객체이다.
- 이는 Deferred<T>의 하위 클래스이며, 특정 시점에 완료되는 비동기 작업의 결과를 표현한다. 
- CompletableDeferred는 특히 작업을 완료할 수 있는 권한을 가지고 있어, 외부에서 작업의 결과를 설정하거나, 완료 상태로 만들 수 있다.

1. 비동기 작업의 성공/실패 조건 설정: CompletableDeferred<T>는 외부에서 성공 조건과 실패 조건을 명시적으로 결정할 수 있다. 
성공 시 complete(result), 실패 시 completeExceptionally(exception)을 호출한다.
2. 비동기 작업의 결과 설정: 성공 시 반환할 결과값을 result로 설정하고, 실패 시 반환할 예외를 exception으로 설정할 수 있다.
3. 성공/실패 시의 액션을 명시적으로 정의: 성공하면 결과를 받아 처리하고, 실패하면 예외를 처리하는 명시적인 로직을 작성할 수 있다.

이처럼 CompletableDeferred<T>는 비동기 작업의 성공과 실패를 관리하며, 그 결과에 따른 후속 작업을 명시적으로 제어할 수 있는 강력한 도구이다.