# Avoid initializing state in the init {} block

ViewModel의 init{} 블록에서 데이터를 로드하는 것을 지양해야 하는 이유.

### 유연성 제한
init{} 블록에서 데이터를 로드하면 ViewModel이 생성되는 즉시 데이터를 로드하게 되어, 데이터 로드 시점을 유연하게 제어하기 어렵다. 예를 들어, 사용자가 특정 작업을 수행한 후 데이터를 로드하거나, 권한이 부여된 후 데이터를 로드해야 할 때 유연하게 대응하기 어렵다. 

### 테스트 어려움
init{} 블록에서 데이터를 로드하면 ViewModel이 인스턴스화될 때마다 데이터 로드가 트리거되어, 단위 테스트를 수행할 때 복잡해질 수 있다. 네트워크 요청이나 데이터베이스 쿼리를 테스트할 때 어려움이 있을 수 있다.

### 리소스 관리
즉시 데이터를 로드하면 사용자가 실제로 데이터를 필요로 하지 않을 때도 리소스가 소비될 수 있다. 이는 특히 네트워크 비용이나 배터리 소모가 중요한 모바일 환경에서 문제가 된다.

### UI 반응성
init{} 블록에서 데이터 로드를 시작하면 UI 반응성이 저하될 수 있다. 데이터 로드 작업이 길어지거나 메인 스레드를 차단할 수 있습니다.

**해결 방안: StateFlow와 MutableStateFlow를 사용하여 상태가 변할 때마다 데이터를 로드**

Source: [MASTERING ANDROID VIEWMODELS][link]

[link]: [https://android-developers.googleblog.com/2024/05/whats-new-in-jetpack-compose-at-io-24.html]