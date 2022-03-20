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

- 진입점: index.js
  - css 및 App 클래스 실행
  - 페이지별, 이벤트의 흐름대로 설계됐다. 메인 페이지와 검색 모달창을 담당하는 두 클래스가 중심이 된다.
- App 클래스

  - 의존하는 클래스
    - SearchModal, VideoStorage, VideoItem
  - constructor()
    - 선택한 dom요소를 멤버변수로 할당
    - 이벤트리스너 등록
      - 대상: 모달열기버튼 -`openModal`, 본영상 볼영상 필터버튼 래퍼 - `handleFilterClick`, 저장 영상 목록 래퍼(하위요소에 이벤트 위임하기 위해) - `handleClickVideoList`
    - 로컬스토리지 관련 인수를 받은 VideoStorage 클래스의 인스턴스(이하 this.video)를 SearchModal 클래스의 인스턴스에게 인수로 전달한다. 또한 SearchModal 클래스의 인스턴스는 App의 (미래에 생성될)인스턴스를 `this`를 통해 인수로 전달받고, 초기실행(init 메서드)된다.
    - `this.storage`에서 저장된 비디오의 id 배열을 가져와서 `requestVideos(ids)`메서드에 전달하고 api에서 fetch된 데이터 목록를 받는다. 데이터 목록을 받으면, `renderVideoItems(videoList)`메서드에 데이터 목록을 전달하고 호출하여 저장되어 있는 영상 목록을 메인 화면에 렌더링하도록 한다.
  - searchResultTemplate()
    - 검색결과 htmlString
  - openModal()
    - modal의 클래스 hide 여부 toggle
  - handleClickVideoList()
    - 이벤트 위임을 하위 요소 중 button 태그에 대해서만 줄 것이므로, target의 태그가 button이 아니면 처음부터 early return하도록 if문을 주었다.
    - `상태변경` 버튼이나 `삭제`버튼에 대해서만 이벤트 위임을 하도록 분기를 짰고, 분기에 따라 각각의 로직(model, view 모두)이 내부에 구현되어 있다.
  - handleFilterClick()
    - '볼 영상', '본 영상' 탭 버튼 클릭 시, 이벤트에 대한 콜백함수
  - renderVideoItems(videoList)
    - 영상 데이터 목록을 전달받아서, map을 통해 아이템 한 개씩 videoItemTemplate메서드에게 전달하여 반환된 html string을 하나로 합친(join) 뒤, 이를 메인 화면에 렌더링한다.
  - videoItemTemplate()
    - 영상 아이템 한 개의 htmlString
  - requestVideos(ids)
    - async/await 적용, api로부터 fetch하여 받은 영상 목록 배열에 대해 map을 돌려, VideoItem 인스턴스들를 만들고, 이들을 배열로 합쳐서 반환한다.

- SearchModal 클래스

  - 영상 검색 모달에 대한 view의 역할(이벤트바인딩, 렌더링 등) 및 바인딩된 이벤트의 콜백함수 등이 함께 있다.
  - 의존
    - VideoItem 클래스에 의존한다.
    - App 클래스에도 의존한다고 보여진다.
      - 왜냐하면, App 클래스의 인스턴스를 본 클래스에서 전달받고 멤버변수로 할당하여 App의 메서드를 호출하고 있기 때문이다.
  - constructor(storage, delegate)
    - App 클래스의 constructor에서 SearchModal 클래스의 인스턴스의 인수에 VideoStorage 클래스의 인스턴스와 App의 (미래에 생성될)인스턴스를 전달하였다.
  - init()
    - App 클래스의 constructor에서 SearchModal의 인스턴스의 init 메서드를 실행했었다.
    - 선택한 dom요소(검색버튼, 검색결과)를 멤버변수로 할당하고 각각의 이벤트에 콜백함수(handleClickSearchButton, handleClickVideoList)를 바인딩하였다.
    - loadMoreObserver() 메서드를 멤버변수로 할당했다.
  - loadMoreObserver()
    - IntersectionObserver의 인스턴스를 반환한다.
      - requestVideos() 호출
  - handleClickSearchButton()
    - 검색 버튼 클릭 시 콜백함수, requestVideos() 호출
  - handleClickVideoList(event)
    - 이벤트 위임을 통해 버튼 태그만 골라서 handleClickSaveButton 메서드에 event 인수를 전달하여 호출한다.
  - handleClickSaveButton(event)

    - 저장 버튼 클릭 시 콜백함수
    - this.delegate.handleSaveVideo(videoId) 호출
      - App 클래스의 메서드이며, 저장 버튼 클릭하면 해당 id를 fetch하여 메인화면(본영상 탭)에 렌더링한다.

  - renderVideoItems(videos)
    - handleClickSearchButton, loadMoreObserver에서 호출된다
  - renderSkeletonItems(videoCount)
    - init, resetSearchResult에서 호출된다

- Storage 폴더
  - LocalStorage 클래스
    - constructor(key)
      - key 하나만 멤버변수로 갖는다.
    - 브라우저의 localStorage 관련 load(), save() 메서드를 갖고 있다.
  - VideoStorage 클래스
    - model 역할에 가까운 것 같다.
    - LocalStorage 클래스를 상속받아서, load(), save() 메서드를 사용한다.
    - 멤버변수로 maxCount와 cache를 갖고 있고, cache는 로컬스토리지에서 load된 데이터로 초기화한다.
    - saveVideo(videoId)
    - toggleWatchStatus(videoId)
    - removeVideo(videoId)
    - clear()
- VideoItem 클래스
  - constructor(item, isWatched)만 존재한다.
    - 멤버변수로 id, publishedAt, title 등이 있고, 이 클래스는 인스턴스로 생성될 때마다 개별 영상 아이템 한 개의 프로퍼티들만 관리된다.
- validation 폴더
- constants 폴더
- utils 파일
  - requestYoutubeVideos(url, params) 함수 등

# 2. 피드백 정리

## 1. 함수

### 1-1. 함수분리

- [#100] 분리함수 분리 기준

  - 로직이 길어지는 경우(코드 10줄 초과)
  - 두 번 이상 재사용될 수 있는 경우
  - 그리고 함수가 길다고 느껴진다면 함수 분리 전에, 함수명이 잘못되었을 수도 있으니 함수명과 그 기능을 다시 잘 살펴보자. (MVC에서 controller는 model과 view가 만나는 전쟁터라서 비대해지기 쉽다 😿 )

- [#111] 메소드들을 분리하는 실익이 있나요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/111/files/23d6739edc86e14986bd35b4bf2b276833475326#r825246805)

  - 아래 예제 코드에서 생각해볼만한 의견 : "기능별로 분리한 취지는 이해하지만, 실제로는 '초기화' 프로세스 중 일부에 불과하고 달리 따로 호출될 일이 없는데 말이에요. 불필요한 prototype 메모리만 차지하는 것이 아닐까요? 가독성을 위해 희생할 가치가 있다고 보시나요?"

  - 함수를 분리한 경우

    ```js
    class MyView1 {
      constructor() {
        this.render();
        this.bindElements();
        this.addEvents();
      }

      templateList() {
        return ` <li>A</li> <li>B</li> <li>C</li> <li>D</li> `;
      }

      template() {
        return ` <button class="my-btn">BUTTON</button> <ul class="my-list"> ${templateInner()} </ul> `;
      }

      render() {
        document.querySelector(".container").innerHTML = this.template();
      }

      bindElements() {
        this.$container = document.querySelector(".container");
        this.$button = this.$container.querySelector(".my-btn");
        this.$list = this.$container.querySelector(".my-list");
      }

      addEvents() {
        this.$button.addEventListener("click", this.clickHandler);
        this.$list.addEventListener("scroll", this.scrollHandler);
      }
    }
    ```

  - 초기화 단계에서 전부 처리해주는 경우(위 코드와 비교)

    ```js
    class MyView2 {
      constructor() {
        this.$container = document.querySelector(".container");
        this.$container.innerHTML = this.template();
        this.$button = this.$container.querySelector(".my-btn");
        this.$list = this.$container.querySelector(".my-list");
        this.$button.addEventListener("click", this.clickHandler);
        this.$list.addEventListener("scroll", this.scrollHandler);
      }

      templateList() {
        return ` <li>A</li> <li>B</li> <li>C</li> <li>D</li> ... `;
      }

      template() {
        return ` <button class="my-btn">BUTTON</button> <ul class="my-list"> ${templateInner()} </ul> ... `;
      }
    }
    ```

  - 반드시 코드 길이와 가독성이 연관된다고 보긴 어렵다.
    - 필요에 따라 적절한 구획을 나누어 줄바꿈 처리만 해주어도 가독성은 월등히 높아질 수 있다.
      - 리액트를 예로 들면, 함수형 컴포넌트는 실제로는 '하나의 함수' 이다. return구문의 위쪽에 매우 많은 로직이 들어가게 되는데, 단지 이것만 가지고 가독성이 떨어진다고 하지는 않는다.

### 1-2. JSON.parse()

- [#105] JSON.parse()의 인자로 parse하지 못하는 값이 넘겨서 온다면?
  - JSON.parse() 는 문자열을 JSON으로 파싱한다. 이 문자열은 유효한 JSON 형태의 문자열이어야 하며, 유효하지 않을 경우 에러가 발생한다.
    - `\r, \n, \t, \f `가 포함되면 안된다.
    - 여분의 콤마도 허용하지 않는다.
    - 프로퍼티 이름은 반드시 쌍따옴표로 표현해야 한다.
    - 01 처럼 리딩제로를 사용할 수 없고, 십진 소수점 뒤에는 최소한 하나의 숫자는 등장해야 한다.
      > [MDN-JSON bad parse](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Errors/JSON_bad_parse)

## 2. 클래스와 메서드

### 2-1. 이벤트핸들러로 등록할 메소드는 bind보다 class fields(화살표함수)

- [#111] 이벤트핸들러로 등록할 메소드는 `bind(this)` 대신 class fields에 `화살표함수`를 사용할 것을 추천한다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/111/files/23d6739edc86e14986bd35b4bf2b276833475326#r825246994)
  - 왜냐하면 bind(this)를 사용하면 메소드 선언 방식에 의해 prototype에 원본메소드가 남아있는 채로, 실제로 호출되는건 `새로 만들어진 bound 메소드`이다. 사용하지 않을 불필요한 메소드가 남아있어 메모리가 낭비된다고 볼 수 있다.
  - 반면 class field를 이용하면 prototype에는 남아있지 않고, 인스턴스 자신이 사용할 함수만 들고 있을 수 있다.

## 3. DOM

### 3-1. element show/hide

- [#111] element를 빈번하게 show/hide해야 하는 경우, 해당 element를 삭제했다가 다시 삽입하는 방식보다는 보여줄 요소를 처음부터 맨 뒤에 만들어두고, css로 show/hide만 하는 방식이 성능상 더 좋을 수 있다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/111/files/23d6739edc86e14986bd35b4bf2b276833475326#r825247862)
- [#77] 리스트에 처음부터 skeleton item을 배치해두고 숨김처리한뒤 검색했을 때 skeleton 을 보이게 한 뒤 skeleton 맨 첫요소 앞에 검색 결과 리스트를 insert 해보세요. 그럼 굉장히 심플해져요. [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/77#discussion_r825409052), [커밋](https://github.com/woowacourse/javascript-youtube-classroom/pull/77/commits/9a2b69ff2adf55cdd3b2e1c4a55d360221077a14)

### 3-2. button의 default type은?

- [#105] 브라우저 별로 button의 default type은 다를 수 있으니 반드시 명시해줘야 한다.
  - IE에서의 default type은 'button', 다른 브라우저에서는 'submit'이다.
  - 따라서 submit 타입으로 사용할 목적이 아니라면, 다른 타입으로 명시해줘야 한다.(그냥 버튼타입을 사용하려면 `type="button"`)

## 4. 이벤트

### 4-1. 이벤트 위임

- [#111] 아이템 생성시마다 매번 listener를 등록해주는 것보다는,
  최초 한 번 상위 element에 등록해두고 event delegation을 활용하는 편이 훨씬 좋다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/111/files/23d6739edc86e14986bd35b4bf2b276833475326#r825247408)

  - 예제코드 해석
    - $videoList에 videoListTemplate을 만들어서 넣을 것이다.
    - videoListTemplate에 있는 각각의 요소들마다 이벤트리스너를 등록하는 것이 아니라, 최초 한 번만 상위 element인 $videoList에 이벤트를 등록함으로써 $videoList 하위로 들어가는 요소들에도 이벤트가 위임되도록 하였다.

  ```js
  // 예제코드
  class SearchModal {
    init() {
      this.$videoList = $(".video-list", this.$searchResult);
      this.addEvent();
    }

    // 상위 요소인 $videoList에 이벤트리스너 한 번 등록
    addEvent() {
      this.$videoList.addEventListener("click", event => {
        this.handleClickVideoList(event);
      });
    }

    // $videoList에 videoListTemplate을 만들어서 넣을 것이다.
    renderVideoItems(videos) {
      const videoListTemplate = videos
        .map(video => {
          return `<li class="video-item" data-video-id="${video.id}">
          // 생략
        </li>`;
        })
        .join("");

      this.$videoList.insertAdjacentHTML("beforeend", videoListTemplate);
    }

    // 인수로 event를 받아 조건을 분기하여 위임을 처리
    handleClickVideoList(event) {
      const { target } = event;
      if (target.classList.contains("video-item__save-button")) {
        this.handleClickSaveButton(event);
      }
    }
  }
  ```

### 4-2. bindEvent() 같은 메서드로 분리하는 경우

- [#105] bindEvent를 따로 빼게 되는 경우 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/105#discussion_r826553859)
  - (1) 클래스 인스턴스를 만들게 될때 멤버변수와 멤버함수를 가지고 메모리에 올라가게 됩니다. 최초 실행 이후 더이상 실행되지 않는 멤버함수를 계속 들고 있을 필요가 없다고 판단됩니다.
  - (2) 생성자 호출이후 bindEvent를 stack에 할당하고 호출하는데 이 또한 1번처럼 굳이 한번 더 할 필요없느 불필요한 과정이라고 생각합니다.
  - (3) bindEvent를 private으로 은닉하지 않음으로서 메소드가 노출되고 외부에서 잘못 사용할 여지가 있습니다.(1번과 2번은 지나친 성능 고려이긴 합니다)
  - 한편 여러 줄이여서 길어진다면 주석을 활용하여 구분, 줄바꿈을 통해서 구분, 새로운 메소드를 활용하여 구분할 수 있습니다. 이부분은 개발자마다 다르겠지만 이때는 bindEvent를 메소드를 따로 빼도 괜찮다고 생각합니다.

## 5. 클린코드

## 6. CSS, HTML

### 6-1. BEM

- [#100] BEM은 궁극적으로 유니크한 클래스를 보장하기 때문에, BEM을 class name으로 사용한다면 굳이 id를 설정할 필요 없이, 유니크함이 보장되는 class name을 사용하는 것이 낫다. 또한, BEM을 class name으로 제공하셨기에 id과 혼동할 여지를 주지 않는게 중요하다.

## 7. 테스트

### 7-1. 유닛테스트와 통합(E2E)테스트의 차이를 생각하자

- [#100] 보통 더미 데이터로 많이 사용하지만 실제 api를 테스트해서 하는 것만큼 통합테스트에 적합한 것이 없다. 여기서 중요한 점은 "유닛 테스트" 와 "통합 테스트"의 차이를 집중해야 한다는 점이다.

  - 비디오의 데이터를 활용해 다양한 메소드나 함수들이 정상동작하는지 여부를 파악하고 싶다면 더미데이터를 이용해서 유닛테스트를 진행하면 될 것 같다.
  - 반면, api가 정상적으로 동작되는가에 대한 통합 테스트는 jest의 mock 기능 등을 이용해 진행하는 것이 적합할 것 같다.

- [#103] `API를 통한 테스트`는 `통합테스트`을 할 때 진행하면 될 것 같고, `유닛테스트`에서는 `더미데이터`를 사용하는 것이 적절할 것 같다.

- [#112] 더미데이터를 활용하여 checkSearchResult를 테스트하는 것은 의미있는 테스트라 판단합니다. checkSearchResult는 데이터를 가공하는 일을 하고 '단위 테스트'는 그 가공하는 일이 제대로 이루어지는지 테스트하면 됩니다. 유투브같은 외부 API의 데이터 형식이 바뀌는경우는 적으며, API를 업데이트하더라도 하위호환을 고려해서 대응합니다. 한편 실제 전체 프로그램의 작동하는것까지 고려하는것은 E2E테스트를 통해서 확인하는게 맞다고 생각합니다.

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
    > 문제가 있는 경우에 대한 테스트 작성 예시 : "입력된 검색어가 없거나, 공백으로 입력된 경우 검색이 안되게한다."

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

### 9-2. prettier 설정

- [#84] `trailingComma: all` 의 장점은?
  - trailingComma를 하게되면 코드리뷰 시점에 이전 코드 부분에 `, `가 추가가 안되므로 더 편하게 수정된 부분만 확인할수도 있는 등, 다양한 이점이 있어요!
