# Pocker Game

- [x] 게임보드 만들기 - 2022.02.22 13:40
- [x] 카드 클래스 구현하기 - 2022-02-24 16:14
- [x] 카드덱 구현하고 테스트하기 - 2022-02-25 16:55
- [ ] 게임로직 구현하기
- [ ] 포커게임 화면 만들기
- [ ] 승자 표시하기
- [ ] 마무리하기

# Step 1. 게임보드 만들기

## 작업목록

- [x] 화면 크기에 따라 뷰를 균등하게 분리해서 생성할 수 있다
- [x] 이미지 뷰에 content mode에 대해 학습하고 원하는 비율로 표시할 수 있다
- [x] 이미지를 배경에 패턴으로 추가할 수 있다
- [ ] 코드로 뷰를 추가한 다음에, 추가학습으로 UIStackView에 대해 학습하고 뷰를 균등하게 나눠서 표시할 수 있다

## 결과물

<image src="./images/step1-1.png" width="150px" />

## 학습 키워드

- Info.plist
- StatusBar
- safeAreaLAyoutGuide
- UIImageView.ContentMode

## 고민과 해결

- 문제: 기기의 화면 크기에 따라 ImageView 의 크기가 비율에 맞게 가변적으로 변화해야하는 문제가 있었습니다.
- 시도와 해결: 스크린의 너비값을 구해 카드 갯수만큼 나누어 각 카드의 너비값과 비율에 맞는 높이 값을 계산했습니다. frame.x 에 추가 값을 더해 이미지간 균일한 간격을 주었습니다.

---

- 문제: ImageView의 Frame 기준이 View 의 (0,0) 으로 StatusBar 영역을 침범하는 문제가 있었습니다.
- 시도 1: StatusBar 의 높이를 구해 ImageView.frame.y 값으로 설정하고자 했으나 ViewController 가 모달로 표시된 경우에는 불필요한 작업이라는 생각이 들었습니다.
- 시도 2: SafeArea 의 frame.y 값을 얻어 ImageView 의 y축 frame 으로 설정하려했으나 의도했던 값을 얻을 수 없었습니다.
- 해결: root view 가 View 계층에 추가되지 않거나 스크린에 보이지 않는 단계에서는 safeAreaLayoutGuide 와 View 의 크기와 동일하다는 것을 알게 되었고, ImageView 생성 시점을 viewDidLoad 가 아닌 viewDidAppear 메서드로 옮겨 문제를 해결했습니다.

# Step 2. 카드 클래스 구현하기

## 작업 목록

- [x] Card class 추가
- [x] Card 정보를 출력하는 코드 분리
- [x] 임의의 카드 객체 인스턴스를 하나 만들어서 출력한다.
- [x] 주석 추가

## 결과물

<image src="./images/step1-2.png" width="100px" />

## 학습 키워드

- Nested Type
- Comparable Protocol
- Swift 성능 최적화

## 고민

- 카드 클래스가 채택한 프로토콜(Comparable CustomStringConvertible)의 구현체는 카드 클래스의 extension 에 정의하였는데 관리적인 측면에서 코드가 분리되어 보여 장점으로 보인다.
- 결과적으로 카드 클래스의 메서드로 추가되는 것이기 때문에 데이터 흐름의 관점에서는 코드가 분리되어 역할이 나뉘는 것은 아니다. 결국 작성할 떄에만 따로 구분하는 것이지 데이터 흐름과는 관계가 없기 떄문에 `extension` 를 사용할 때 데이터 흐름을 고려해야한다.

# Step 3. 카드덱 구현하고 테스트하기

## 작업 목록

- [x] Card 클래스, CardDeck  구현
- [x] 팩토리 패턴을 적용한 CardDeckFactory 구조체 구현
- [x] 테스트 코드 작성

## 학습 키워드

- Factory Pattern
- Class & 구조체
- Sattolo Shuffle 알고리즘
- Durstenfeld Shuffle 알고리즘
- Unit Testing

## 고민과 해결
<details>
<summary>문제</summary>
  - CardDeck 인스턴스 생성에 필요한 복잡한 작업을 CardDeckFactory에 모두 위임하였는데 CardDeck.reset 호출 시 다시 Card 인스턴스를 만들어 덱에 채워야하는 문제가 생겼습니다.
</details>

<details>
  <summary>시도</summary>
  - CardDeck.reset 내부에서 CardDeckFactory 를 사용해 다시 덱을 채워우면 해결되나 코드간 결합도가 높아지는 문제가 발생했습니다.
</details>

<details>
  <summary>해결</summary>
  - Card 클래스는 Reference Semantic 방식으로 동작한다는 점을 고려했습니다.
  - CardDeck.cache 속성을 추가해 CardDeck.removeOne 호출 시 내부에서 cache 배열에 draw 된 카드를 저장시켰습니다.
  - reset 호출 시 cache 의 저장된 덱 정보([Card]) 를 활용하는 방법으로 CardDeckFactory 와 분리하여 해결했습니다.
</details>

<details>
  <summary>적용한 해결방법의 장점</summary>
  - 상위 모듈에서 removeOne 을 사용해 draw 한 카드와 cache에 담긴 카드 모두 같은 인스턴스를 가리키고 있어 메모리 활용 측면에서도 효율적일 것이라고 생각합니다.
  - Card 의 속성은 immutable 하게 정의되었기 때문에 상위 모듈과 하위 모듈이 같은 인스턴스를 참조하더라도 문제가 되지 않을 것이라고 생각합니다.
</details>