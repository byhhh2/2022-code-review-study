# Level1 Youtube Step1(담당 PR 번호들) - 마르코

- 분석 담당 코드
  - 마르코 [#100](https://github.com/woowacourse/javascript-youtube-classroom/pull/100)
  - 위니 [#103](https://github.com/woowacourse/javascript-youtube-classroom/pull/103)
  - 병민 [#111](https://github.com/woowacourse/javascript-youtube-classroom/pull/111)
  - 해리 [#112](https://github.com/woowacourse/javascript-youtube-classroom/pull/112)
  - 코카콜라 [#77](https://github.com/woowacourse/javascript-youtube-classroom/pull/77)
  - 후이 [#105](https://github.com/woowacourse/javascript-youtube-classroom/pull/105)
  - 샐리 [#84](https://github.com/woowacourse/javascript-youtube-classroom/pull/84)

# 1. 코드 분석

## 병민[#111], 해리[#112]

# 2. 피드백 정리

## 1. 함수

### 1-1. 함수분리

- [#100] 분리함수 분리 기준
  - 로직이 길어지는 경우(코드 10줄 초과)
  - 두 번 이상 재사용될 수 있는 경우
  - 그리고 함수가 길다고 느껴진다면 함수 분리 전에, 함수명이 잘못되었을 수도 있으니 함수명과 그 기능을 다시 잘 살펴보자. (MVC에서 controller는 model과 view가 만나는 전쟁터라서 비대해지기 쉽다 😿 )

## 2. 클래스와 메서드

## 3. DOM

## 4. 이벤트

## 5. 클린코드

## 6. CSS, HTML

### 6-1. BEM

- [#100] BEM은 궁극적으로 유니크한 클래스를 보장하기 때문에, BEM을 class name으로 사용한다면 굳이 id를 설정할 필요 없이, 유니크함이 보장되는 class name을 사용하는 것이 낫다. 또한, BEM을 class name으로 제공하셨기에 id과 혼동할 여지를 주지 않는게 중요하다.

## 7. 테스트

### 7-1. 유닛테스트와 통합(E2E)테스트의 차이를 생각하자

- [#100] 보통 더미 데이터로 많이 사용하지만 실제 api를 테스트해서 하는 것만큼 통합테스트에 적합한 것이 없다. 여기서 중요한 점은 "유닛 테스트" 와 "통합 테스트"의 차이를 집중해야 한다는 점이다.

  - 비디오의 데이터를 활용해 다양한 메소드나 함수들이 정상동작하는지 여부를 파악하고 싶다면 더미데이터를 이용해서 유닛테스트를 진행하면 될 것 같다.
  - 반면, api가 정상적으로 동작되는가에 대한 통합 테스트는 jest의 mock 기능 등을 이용해 진행하는 것이 적합할 것 같다.

- [#103] `API를 통한 테스트`는 `통합테스트`을 할 때 진행하면 될 것으로 생각됩니다. `유닛테스트`에서는 `더미데이터`를 사용하시면 될것 같습니다.

### 7-2. AAA 테스트

- [#100] GWT(given, when, then) 및 AAA 테스팅
  - Arrange : 테스트에 필요한 데이터를 생성한다.
  - Act : 테스트하려는 코드를 실행한다.
  - Assert : 예상하는 결과를 확인한다.

### 7-3. 오작동하는 케이스를 중심을 테스트하자

- [#100] 로직의 정상 작동 결과는 하나뿐이지만, 오작동하는 케이스는 수십가지가 넘는다.
  - 따라서 유저가 어떤 행동을 했을 때 오작동을 하면 유저에게 그에 대한 피드백이 필요하다. 이러한 오작동 케이스에 대한 수많은 문제를 정상적으로 사용할 수 있도록 유도하는 코드가 필요하다.
  - 테스트코드는 오작동 문제 상황에 대한 코드 보완 여부를 협업하는 사람들과 공유하는 성격도 갖고 있다.
    - 따라서 에러 상황에 대한 테스트코드를 많이 고민하고 작성수록, 이 테스트코드를 코드단위로 이행시켜서 효율성을 증대시킬 수 있다.
  - 결론: 유저 관점에서 유저가 일으킬 수 있는 수많은 오작동 케이스를 커버할 수 있는 테스트코드를 작성하자. 그리고 테스트코드는 에러 상황 커버리지를 코드단위로 이행시키며 다양한 사람들에게 선한 영향력을 줄 수 있음을 염두하고 정성껏 작성하자!

## 8. 설계

## 9. 기타

### 9-1. eslint 설정

- [#100] 불필요한 eslint 설정 제거

  - globals 프로퍼티는 선언되지 않은 전역변수를 사용하는 경우 ESLint 경고가 발생하지 않도록 사용자 전역 변수를 추가하는 곳이다. ESLint는 V8 엔진에 있는 Atomics와 SharedArrayBuffer를 인식하지 못하기 때문에, 이러한 전역 객체를 사용하는 경우 이와 관련한 ESLint 설정을 추가해주는 것 같다. 이와 관련된 기능을 사용하지 않는다면, eslint 설정에 둘 이유가 없다.

    > 참고
    > [Eslint what is meaning of Atomics Read only and Shared Array Buffer Readonly](https://www.onooks.com/tag-globals-atomics-readonly/) > [MDN-Atomics](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Atomics)

```js
  globals: {
    Atomics: 'readonly',
    SharedArrayBuffer: 'readonly',
  },
```

###
