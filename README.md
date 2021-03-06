# Redux Homework ('17 Open Source S/W Class)
*****
[![N|Solid](https://camo.githubusercontent.com/f28b5bc7822f1b7bb28a96d8d09e7d79169248fc/687474703a2f2f692e696d6775722e636f6d2f4a65567164514d2e706e67)](https://deminoth.github.io/redux/)
*****
## 동기
    자바스크립트 싱글 페이지 애플리케이션이 갖추어할 요건이 점점 더 복잡해지고있는 만큼, 어느 때보다도 많은 상태를 자바스크립트 코드로 관리할 필요가 생겨났습니다. 여기에서 상태에는 서버 응답, 캐시 데이터, 지역적으로 생성해서 사용하고 있지만 아직 서버에 저장되지 않은 데이터를 의미합니다. 이외에도 활성화된 라우트, 선택된 탭, 로딩을 보여줄지 여부, 페이지네이션 컨트롤 등 다양한 UI 상태도 포함합니다.
    항상 변하는 상태를 관리하기란 어렵습니다. 모델이 다른 모델을 업데이트하고, 그리고 뷰가 모델을 업데이트 할 수 있고, 이 뷰가 다시 다른 모델을 업데이트하고, 이에 따라 또 다른 뷰가 업데이트 됩니다. 어느 시점에서는 프로그래머조차 애플리케이션에서 무슨 일이 일어나는지 알 수 없게 됩니다. 상태를 언제, 왜, 어떻게 업데이트할지 제어할 수 없는 지경에 이르고 맙니다. 시스템이 불투명하고 비결정적일 때 버그를 재현하거나 새로운 기능들을 추가하기란 매우 어렵습니다.
    더욱 안 좋은 소식은 프론트엔트 제품 개발에 있어서 새로 갖춰야할 요건들이 늘어나고 있다는 점입니다. 이러한 예로 낙관적 업데이트(Optimistic update), 서버 렌더링, 라우트가 일어나기 전에 데이터 가져오기 등이 있습니다. 프론트엔드 개발자들은 이전에 겪어보지 못 했던 복잡한 상황에 둘러싸여 있습니다. 
    이러한 복잡함은 변화(mutation)나 비동기(asyncronicity)와 같이 사람이 추론해내기 어려운 두 가지 개념을 섞어서 사용한다는 데서 옵니다. 두 가지를 멘토스와 콜라라고 부르는데, 이 둘은 나눠서 보면 훌륭하지만 함께 두면 엉망이 됩니다. React와 같은 라이브러리들에서는 이 문제를 해결하기 위해 뷰 레이어에서 비동기와 DOM 조작들을 없애버렸습니다. 하지만 React는 데이터를 관리하는 일에는 관여하지 않습니다.
    Flux, CQRS, Event Sourcing을 따라, Redux는 상태 변화가 일어나는 시점에 제약을 두어 상태 변화를 예측 가능하게 만들고자 시도합니다. 이러한 제약은 세가지 원칙에 반영되어 있습니다.
*****
## 3가지 원칙
### 진실은 하나의 소스로부터
    애플리케이션의 모든 상태는 하나의 스토어 안에 하나의 객체 트리 구조로 저장됩니다.
    이를 통해 범용적인 애플리케이션(universal application, 하나의 코드 베이스로 다양한 환경에서 실행 가능한 코드)을 만들기 쉽게 만들 수 있습니다. 서버로부터 가져온 상태는 시리얼라이즈되거나(serialized) 수화되어(hydrated) 전달되며 클라이언트에서 추가적인 코딩 없이도 사용할 수 있습니다. 또한 하나의 상태 트리만을 가지고 있기 때문에 디버깅에도 용이할 것입니다. 빠른 개발 사이클을 위해 개발중인 애플리케이션의 상태를 저장해놓을 수도 있습니다. 하나의 상태 트리만을 가지고 있기 때문에 이전에는 굉장히 구현하기 어려웠던 기능인 실행취소/다시실행(undo/redo)을 손쉽게 구현할 수 있습니다.
### 상태는 읽기 전용이다
    상태를 변화시키는 유일한 방법은 무슨 일이 벌어지는 지를 묘사하는 액션 객체를 전달하는 방법뿐입니다.
    이를 통해서 뷰나 네트워크 콜백에서 결코 상태를 직접 바꾸지 못 한다는 것을 보장할 수 있습니다. 모든 상태 변화는 중앙에서 관리되며 모든 액션은 엄격한 순서에 의해 하나하나 실행되기 때문에, 신경써서 관리해야할 미묘한 경쟁 상태는 없습니다. 액션은 그저 평범한 객체입니다. 따라서 기록을 남길 수 있고, 시리얼라이즈할 수 있으며, 저장할 수 있고, 이후에 테스트나 디버깅을 위해서 재현하는 것도 가능합니다.
### 변화는 순수 함수로 작성되어야한다
    액션에 의해 상태 트리가 어떻게 변화하는 지를 지정하기 위해 프로그래머는 순수 리듀서를 작성해야합니다.
    리듀서는 그저 이전 상태와 액션을 받아 다음 상태를 반환하는 순수 함수입니다. 이전 상태를 변경하는 대신 새로운 상태 객체를 생성해서 반환해야한다는 사실을 기억해야 합니다. 처음에는 하나의 리듀서만으로 충분하지만, 애플리케이션이 성장해나가면 상태 트리의 특정한 부분들을 조작하는 더 작은 리듀서들로 나누는 것도 가능합니다. 리듀서는 그저 함수이기 때문에 호출되는 순서를 정하거나 추가적인 데이터를 넘길 수도 있습니다. 심지어 페이지네이션과 같이 일반적인 재사용 가능한 리듀서를 작성하는 것도 가능합니다.
*****
## 기존 기술들
    Redux는 기존 기술들의 복합적인 유산입니다. Redux를 기존 기술들과 비교해보면 일부 패턴이나 기술적인 면에서 비슷한 점을 가지고 있습니다. 하지만 중요한 차이점도 있습니다. 이 절에서는 이러한 유사성과 차이점에 대해서 설명합니다.
### Flux
    Redux를 Flux의 구현 중 하나라고 생각할 수 있기도 하고 아니기도 합니다.
    Redux는 Flux의 중요한 특징들로부터 영감을 얻었습니다. Flux와 마찬가지로 Redux에서는 애플리케이션의 특정 레이어에 있을 모델 업데이트 로직에 집중할 수 있도록 해줍니다(Flux의 '스토어', Redux의 '리듀서'). 스토어나 리듀서는 애플리케이션 코드가 직접 데이터를 조작하는 대신 액션이라고 불리는 평범한 객체로만 모든 데이터 변화를 묘사하도록 강제합니다.
    Flux와 달리 Redux에는 디스패처라는 개념이 존재하지 않습니다. 이는 Redux가 이벤트 에미터보다 순수 함수들에 의존하고 있기 때문입니다. 그리고 순수 함수는 이것들을 관리하는 추가적인 엔티티 없이도 조합하기 쉽습니다. Flux를 어떻게 보느냐에 따라서 이를 상세 구현 방법의 하나라고 생각할 수도 있습니다. Flux는 (state, action) => state 형식으로 묘사되곤 합니다. 따라서 Redux 역시 Flux 아키텍처라고 이야기할 수 있지만, 순수 함수를 통해 이를 더 간단하게 만듭니다.
    Flux와 또 다른 중요한 차이점은 Redux는 당신이 결코 데이터의 상태를 바꾸지 않는다고 가정한다는 점입니다. 상태는 평범한 객체나 배열이면 충분히 다룰 수 있지만 리듀서에서 상태를 변경하지 않는 것을 강력히 권장합니다. 리듀서에서는 항상 새로운 객체를 반환해야합니다. 이를 위해 Babel에 구현되어있는 ES7에서 제안된 object spread 문법이나 Immutable을 사용할 수 있습니다.
    성능이나 다른 이유 때문에 위해 순수하지 않은 리듀서를 작성하는 것도 기술적으로는 가능하지만, 이렇게 하지 않을 것을 권장합니다. 순수하지 않은 리듀서 구현은 시간 여행, 기록/재생, 핫 로딩과 같은 개발 지원 기능을 망가뜨립니다. 더욱이 불변성 때문에 대부분의 실제 애플리케이션에서 성능 문제가 있을 것 같아보이지만, Om이 증명했듯이 객체 할당에 있어서 성능에서 불리할 지라도 순수 함수를 통해 무엇이 바뀌었는지 정확히 판단할 수 있기 때문에 재렌더링이나 재계산 같은 값비싼 연산을 피한다는 점에서는 여전히 유리합니다.
### Elm
    Elm은 Evan Czaplicki이 Haskell에서 영감을 받아 만든 함수형 프로그래밍 언어입니다. Elm은 업데이트가 (state, action) => state 시그니처를 따르도록 하는 “모델 뷰 업데이트” 아키텍처을 강제합니다. 엄밀히 말해 Elm의 업데이터(updater)와 Redux의 리듀서(reducer)는 같습니다.
    하지만 Redux와 달리 Elm은 언어입니다. 따라서 강제된 순수성, 정적 타입, 뛰어난 불변성과 (case 표현식을 활용한) 패턴 매칭 등 다양한 장점을 누릴 수 있습니다. Elm을 사용할 계획이 없더라도 Elm의 아키텍처를 공부하거나 가지고 놀아보기를 권합니다. 비슷한 아이디어를 구현해둔 흥미로운 자바스크립트 라이브러리 플레이그라운드)도 있습니다. Redux가 어디서 영감을 받았는 지 찾을 수 있습니다. Elm의 정적 타입에 가까워질 수 있는 한 가지 방법은 Flow와 같은 점진적인 타입(gradual typing) 솔루션을 사용하는 것입니다.
### Immutable
    Immutable은 영속 데이터 구조를 구현한 자바스크립트 라이브러리입니다. 이는 성능도 괜찮을 뿐만 아니라 자바스크립트 API와도 잘 어울립니다.
    Immutable이나 유사 라이브러리들은 Redux와도 문제없이 잘 어울립니다. 
    Redux는 애플리케이션에서 어떻게 상태를 저장할 지에 대해서 신경쓰지 않습니다. 평범한 객체, 불변 객체, 그 외의 무엇이라도 상관하지 않습니다. 범용적인 애플리케이션 작성을 위해 (디)시리얼라이제이션 매커니즘이나 서버에서 상태를 가져와 수화하는(hydrating, 기존 객체에 값을 채우는 것을 의미함.) 기능을 원할 수도 있는데, 불변성을 지원하기만 한다면 어떤 데이터 스토리지 라이브러리를 사용해도 무방합니다. 예를 들어 Redux 상태를 관리하기 위해 Backbone을 사용하는 것은 어울리지 않습니다. 왜냐면 Backbone 모델은 가변적이기 때문입니다.
    사용하고자 하는 불변 라이브러리에서 커서(cursor)를 지원하더라도 이를 사용해서는 안 됩니다. 전체 상태 트리는 읽기 전용으로 다뤄져야 하며, Redux로 상태를 업데이트하고 업데이트를 구독해야합니다. 따라서 커서를 사용하는 것은 Redux와는 어울리지 않습니다. 커서를 오직 상태 트리와 UI 트리를 분리하거나 점진적으로 커서를 한정짓는(refine) 경우에만 사용하더라도, 커서 대신에 셀렉터를 검토해보아야합니다. 셀렉터는 조합 가능한 getter 함수들입니다. 조합 가능한 훌륭하며 간결한 셀렉터를 제공하는 reselect를 살펴보기 바랍니다.
### Baobab
    Baobab은 플레인 자바스크립트 객체를 업데이트하기 위해 불변(immutable) API를 구현한 또 다른 유명한 라이브러리입니다. 하지만 Redux와 함께 사용해서 누릴 수 있는 이점은 크지 않습니다.
    Baobab에서 제공하는 대부분의 기능은 커서를 사용해 데이터를 업데이트하는 일이므로, 액션을 디스패치하는 것이 데이터를 변경하는 유일한 방법인 Redux와는 어울리지 않습니다. 따라서 Baobab은 다른 방법으로 이 문제를 해결하지만, 이들은 서로 보완적이지 않습니다.
    Immutable과 달리 Baobab은 내부적으로 어떤 특별하고 효율적인 데이터 구조를 구현하지는 않습니다. 따라서 Redux와 Baobab을 함께 사용해도 큰 장점은 없습니다. 평범한 객체를 사용하는 편이 더 쉽습니다.
### Rx
    Reactive Extensions (그리고 현대적으로 재작성하고 있는 RxJS)는 비동기 애플리케이션의 복잡도를 제어하는 아주 뛰어난 방법입니다. 실제로 독립적인 observables로서 사람과 컴퓨터가 인터렉션하는 모델을 구현하는 라이브러리를 만드려는 노력도 있습니다.
    Redux와 Rx를 함께 사용하는 게 합리적입니다! Redux와 Rx는 아주 잘 어울립니다. 예를 들어 Redux 스토어를 observable로 사용하는 것은 아주 쉽습니다.
    마찬가지로 서로 다른 비동기 스트림을 조합해서 store.dispatch()에서 사용하기 전에 액션에서 사용할 수도 있습니다.
    이미 Rx를 잘 사용하는 데 Redux가 꼭 필요하지는 않습니다. Rx 안에서 Redux를 재구현하는 것은 그리 어렵지 않습니다. Rx의 .scan() 메서드를 활용해 두 줄이면 충분하다고도 합니다.
*****
## 생태계
    Redux는 작은 라이브러리입니다. 하지만 Redux의 규약과 API는 조심스럽게 선택되고 있으며 다양한 도구와 확장 라이브러리의 생태계가 만들어져가고 있습니다.
### 바인딩
- `react-redux — React 바인딩`
- `ng-redux — Angular 바인딩`
### 미들웨어
- `redux-thunk — 비동기 액션을 만들기 위한 가장 쉬운 방법`
- `redux-promise — FSA를 따르는 프로미즈 미들웨어`
- `redux-rx — Observable을 위한 미들웨어를 포함한 Redux용 RxJS 유틸리티`
- `redux-batched-updates — Redux 디스패치에 따른 React 배치 업데이트 미들웨어`
- `redux-logger — 모든 Redux 액션과 다음 상태에 대한 기록 도구`
- `redux-immutable-state-invariant — Redux 디스패처가 아닌 곳에서 상태 변화가 일어나면 경고해주는 미들웨어`
### 컴포넌트
- `redux-form — Redux 스토어를 통한 HTML form을 포함한 React 컴포넌트 상태 관리 지원`
### 스토어 확장
- `redux-batched-subscribe — 디스패치에 따른 배치 구독 알림(subscribe notification)을 보낼 수 있는 스토어 확장`
### 유틸리티
- `reselect — NuclearJS에서 영감을 받아 만들어진 효율적인 파생 데이터 셀렉터`
- `normalizr — 중첩된 API 응답을 React나 Flux에서 활용하기 쉬운 형태로 만들어주는 도구`
- `redux-actions — Flux 표준 액션 유틸리티`
- `redux-transducers — Redux용 트랜스듀서`
- `redux-immutablejs - Redux와 Immutable 통합을 위한 도구`
### 개발자 도구
- `redux-devtools — React Europe에서 처음 소개된 시간여행 UI, 핫 리로딩, 리듀서 에러 핸들링을 위한 액션 로거`
