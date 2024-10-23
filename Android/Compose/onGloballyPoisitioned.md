# Modifier.onGloballyPositioned
onGloballyPositioned는 Jetpack Compose에서 컴포저블의 전역적인 배치 상태가 결정된 후, 해당 컴포저블의 위치나 크기 정보를 제공하는 콜백을 설정하는 데 사용되는 Modifier이다.

컴포저블이 화면 상에 그려지고 나서 위치나 크기와 같은 정보를 알고 싶을 때 onGloballyPositioned를 사용한다. 이 콜백은 컴포저블이 전역 좌표계에 배치된 직후 호출되며, LayoutCoordinates 객체를 통해 해당 컴포저블의 크기 및 위치를 조회할 수 있다.

주요 기능

**1. 크기 확인: 컴포저블이 배치된 후 실제 크기를 확인할 수 있다. 예를 들어, 컴포저블의 높이나 너비를 얻을 수 있다.**
```
Modifier.onGloballyPositioned { layoutCoordinates ->
    layoutHeight = layoutCoordinates.size.height
}
```
- layoutCoordinates.size.height를 통해 레이아웃의 높이를 추출하고, 이를 layoutHeight 변수에 저장
- LayoutCoordinates.size는 레이아웃의 크기(너비와 높이)를 제공하며, 이 경우 높이(height)만 추출하여 이후 로직에 활용.
- 이 코드는 컴포넌트가 화면에 배치될 때 해당 컴포넌트의 높이 값을 계산하고 이를 사용하고 싶은 상황에 적합.           
   
**2. 위치 확인: 컴포저블이 화면 상에서 어느 위치에 배치되었는지를 알 수 있다. 이때 위치는 로컬 좌표 또는 전역 좌표로 확인 가능하다.**
```
var position by remember { mutableStateOf(Offset.Zero) }
position: (Offset) -> Unit

Modifier.onGloballyPositioned { coordinates ->
    val pos = coordinates.localToWindow(Offset.Zero)
    position(pos)
}

Modifier.padding(
	start = with(LocalDensity.current) { position.x.toDp() },
)
```
- coordinates라는 LayoutCoordinates 객체에서 좌표 정보를 추출
- coordinates.localToWindow(Offset.Zero)는 해당 컴포넌트의 화면 상에서의 위치를 Offset 객체로 변환. 이 값은 전체 화면의 좌표계에서 현재 컴포넌트의 위치를 나타냄.
- 추출한 위치 정보를 position(pos) 함수로 전달. 이 함수는 커스텀 함수로 보이며, 위치 데이터를 외부에서 사용할 수 있도록 설정하는 역할을 함.
  