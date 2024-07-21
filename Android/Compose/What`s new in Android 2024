# What`s new in Android 2024

#### Compose 컴파일러와 Kotlin
컴포즈 컴파일러가 코틀린에 직접 배포됨에 따라 Kotlin 버전과 일치하는 컴파일러 버전을 지정할 필요가 없음

#### Strong Skipping Mode
Strong Skipping Mode:
모든 매개변수가 stable 하지 않더라도 컴포저블을 건너뛸 수 있음.
Compose Compiler 1.5.4부터 실험적으로 도입되었고, 2.2.20부터는 기본으로 활성화.

```
data class User(val id: Int, val name: String)

@Composable
fun UserList(userList: List<User>) {
    // ...
}
```

User 데이터 클래스는 모두 불변이므로 Stable
하지만 UserList(userList: List<User>)의 경우 User는 Stable한 상태이지만, List는 Unstable한 객체

- Strong Skipping Mode가 활성화된 경우 UserList는 recomposition을 피할 수 있음 (활성화 안된 상태이면 매번 리컴포지션)
- 람다 메모리제이션:
Lambda 호출을 위해 remember로 따로 감싸줄 필요 없이, Strong Skipping Mode가 활성화된 상태라면 자동으로 모든 lambda가 remember로 감싸짐.

#### Indication API 변경
Modifier.clickable(indication = rememberRipple()에서 indication = ripple()로 마이그레이션 해야 함.

#### Compose 1.7.0-beta01
아름다운 화면 효과 전환:
Compose Navigation과 함께 사용할 수 있음.
 SharedTransitionLayout, Modifier.sharedElements(), Modifier.sharedBounds()

#### Compose 1.7.0-beta05
LazyList의 Modifier.animateItem():
항목 삽입, 제거, 재정렬을 자동으로 애니메이션 처리.
FadeIn, FadeOut, 재정렬 애니메이션

#### AnnotatedString.fromHtml() 익스텐션
HTML 텍스트를 바로 AnnotatedString으로 변환할 수 있는 익스텐션이 추가.

#### Android 15, Target SDK 35
Edge to Edge 모드:
앱이 기본적으로 Edge to Edge 모드로 설정.
Status bar가 기본으로 투명 처리되며 setStatusBarColor가 비활성화됨..
Material3 컴포넌트 TopBar는 자동으로 인셋을 처리.
그 외에는 수동으로 WindowInsets을 적용해야 할 수 있음.

#### Glance 1.1로 위젯 제작
   
#### Credential Manager
간편한 로그인 지원:
Credential Manager를 사용하여 쉽게 로그인 기능을 구현할 수 있음.

#### 접근성
이미지 description:
시각장애인 사용자들을 위해 이미지에 적절한 텍스트 설명을 넣어주는 것이 좋음.

---

Source: [GoogleDevelopersBlog][googlelink]

[googlelink]: https://android-developers.googleblog.com/2024/05/whats-new-in-jetpack-compose-at-io-24.html 

