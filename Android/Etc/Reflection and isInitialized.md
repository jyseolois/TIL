# :: 연산자와 리플렉션

- ::는 리플렉션 연산자로, 함수나 속성을 참조할 때 사용한다. 예를 들어, ::originalData는 originalData 속성에 대한 참조를 반환한다.
- 리플렉션은 런타임에 객체나 속성의 정보를 얻고 다룰 수 있게 해준다.

# ::A.isInitialized
- isInitialized는 lateinit 변수에만 사용 가능한 프로퍼티로, lateinit 변수의 초기화 상태를 확인한다.
- 만약 ::originalData라면, ::는 originalData라는 속성의 참조를 가져오게 해주는 연산자이다. 이 연산자를 통해 originalData 속성 참조에 접근하여 초기화되었는지를 확인할 수 있다. 즉, originalData라는 변수가 초기화되었는지 검사하는 데 사용된다.

이런 구문은 lateinit 변수가 초기화되지 않은 상태에서 접근하려 할 때 발생하는 UninitializedPropertyAccessException을 방지하는 데 유용하다.