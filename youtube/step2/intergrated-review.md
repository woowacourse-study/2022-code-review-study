# 주간 코리스 [👩🏻‍💻나만의 유튜브 강의실 STEP2🧑🏻‍💻]
### 0. 코리스 설명
- [코드리뷰스터디(코리스)](https://github.com/woowacourse-study/2022-code-review-study) 는 함께 자라기 🌱를 지향해요.
- 이번 호에 정리한 코드리뷰 피드백 주제는 Level1 Youtube Step2입니다.
- 스터디원이 아니시더라도 본 자료에 대한 피드백 및  [디스커션](https://github.com/woowacourse-study/2022-code-review-study/discussions)  참여 등을 모두 환영해요.🥳
- 자료 : 우아한테크코스 프론트엔드 4기 코드리뷰
	- 각 목록의 앞에 해당 PR번호 기재
-  편집 : 코드리뷰스터디원
	- 마르코(@wonsss) ✨
	- 호프(@moonheekim0118) ✨
	- 무비(@byhhh2) ✨
	- 록바(@lokba) ✨
	- 꼬재(@kkojae91) ✨

<br/>


## 1. 아키텍쳐
### 1-1. Storage 와 도메인의 분리 
- [#149] storage 위에도 대략 설명을 드렸지만 해당 유튜브 앱의 영속성을 담당하는 중요한 부분입니다. 하지만 네이밍과는 다르게 도메인에 종속성이 있어서 재활용이 어려울 것 같네요
### 1-2.  모든 View를 관리하는 View의 의미 
- [#126] view 들을 관장하는 view 클래스가 있는 경우-  보통 각 뷰는 독립적이어야 하는데 뷰들을 관장하는 뷰가 있다는건 뷰의 역할은 아닌 것 같다.  -  [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/126#discussion_r831918465)

### 1-3. 외부와의 결합도 줄이기 
[#122](https://github.com/woowacourse/javascript-youtube-classroom/pull/122#discussion_r831122491) 외부와 강하게 결합되어있을 수록 변경에 취약해 보통 레이어 하나 정도를 두어 유연하게 작성하는게 좋다.

```js
// before
const videoStorage = {
  getVideo() {
    return JSON.parse(localStorage.getItem("saveVideoData")) || [];
  },
};
```


```js
// after 
export default class VideoStorage {
  #videos = JSON.parse(localStorage.getItem("saveVideoData")) || [];

  getVideo() {
    return this.#videos;
  }

  setVideo() {
    localStorage.setItem("saveVideoData", JSON.stringify(this.#videos));
  }
}
```

- 네이밍도 동일하다. Storage라는 이름일 경우 localStorage가 API로 변경되었을 경우를 생각한다면, Videos 라는 이름을 갖는게 좋다!

### 1-4. 단방향 데이터 바인딩과 양방향 데이터 바인딩
- [#133](https://github.com/woowacourse/javascript-youtube-classroom/pull/133#discussion_r831685384) 단방향 / 양방향 바인딩
- 단방향 데이터 바인딩
	- 데이터와 템플릿을 결합하여 화면을 생성한다.
	- UI에서 받아온 데이터를 멤버 변수 혹은 도메인에서 상태를 관리하지 않고 곧 바로 render 해준다.
	- react는 단방향 데이터 바인딩을 한다.
- 양방향 데이터 바인딩
	- 데이터의 변화를 감지해 템플릿과 결합하여 화면을 갱신하고 화면에서의 입력에 따라 데이터를 갱신한다.
	- 즉, 데이터와 화면 사이의 데이터가 계속해서 일치하게 되는 것이다. (UI와 domain의 데이터가 일치)
	- Vue는 양방향 데이터 바인딩을 한다.
	- [facebook article](https://facebook.github.io/flux/docs/in-depth-overview/)
- [#178] 데이터 바인딩 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/148#discussion_r833264831)
  - [준찌 추천 링크](https://facebook.github.io/flux/docs/in-depth-overview/)  
  - 데이터 바인딩은 데이터와 뷰를 묶는다는 뜻이다. 웹어플리케이셥의 복잡도가 증가하면 뷰와 데이터를 일치시키기 어려워진다. 그래서 데이터와 뷰가 자동으로 일치하도록 묶어 두는 것이 데이터 바인딩이다.
  - 데이터 바인딩은 다시 `단방향`과 `양방향`으로 나뉜다.
    - 단반향 데이터 바인딩은 데이터가 변경되면 템플릿과 데이터를 합쳐 뷰를 만든다.
      - 대표적으로 React가 단방향 데이터 바인딩을 한다. 단방향 데이터 바인딩은 데이터와 템플릿을 결합해 화면을 생성하는 것이다. (JS -> HTML만 가능) 사용자의 입력에 따라 데이터를 갱신하고 화면을 업데이트 해야 하므로 단방향 데이터 바인딩으로 구성하면, 데이터의 변화를 감지하고 화면을 업데이트 하는 코드를 매번 작성해주어야 한다. 리액트는 자바스크립트 기반으로, 부모 뷰->자식 뷰 바뀐 내용을 직접 전달한다.  
      - 장점 : 데이터 변화에 따른 성능 저하 없이 DOM 객체 갱신 가능, 데이터 흐름이 단방향(부모->하위 컴포넌트)이라, 코드를 이해하기 쉽고 데이터 추적과 디버깅이 쉬움
      - 단점: 변화를 감지하고 화면을 업데이트 하는 코드를 매번 작성해야 함
    - 양방향 데이터바인딩은 뷰의 변경을 감지하여 데이터에 반영해주는 동시에, 데이터의 변경이 일어나면 템플릿과 데이터를 합쳐 뷰에 반영해준다.
      - 양방향 데이터 바인딩
        - 두 데이터 혹은 정보의 소스를 일치시키는 기법으로, 화면에 보이는 데이터와 브라우저 메모리에 있는 데이터(여러개의 자바스크립트 객체)를 일치시키는 것을 말한다. 예를 들어, MVC 모델에서 model과 view를 서로 묶어 model과 view의 "자동 동기화" 시키기 라고 이해할 수 있다. 
          - 컨트롤러에서 model이 변경됨 -> view변경됨, view에서 scope model이 변경됨 -> 컨트롤러에서 model이 변경됨
        - 장점 : 코드의 사용면에서 코드량을 크게 줄여줌
        - 단점 : 변화에 따라 DOM 객체 전체를 렌더링해주거나 데이터를 바꿔주므로, 성능이 감소되는 경우가 있음     
  - [편집하면서 참고한 블로그](https://blog.hyunmin.dev/15)


### 1-5. 완벽한 구조를 만드는 방법

- [#135] [크루원 질문] 구조를 만들 때 어떻게 진행하는게 올바른 방법이라 생각하시는지 구조를 만들 때 중점 사항을 두시는 부분이 있으신지 궁금합니다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/135#pullrequestreview-915496528)
```
    세상에 완벽한 아키텍처는 존재하지 않습니다.
    또, 처음부터 좋은 아키텍처를 만들수도 없죠.
    첫 시작은 하신 것 처럼 필요로 인해 이렇게 나눠보시는 훈련을 하는거라 생각하시면 되겠습니다.

    그렇기에 "구조를 막 짜보고 부숴보라"고 권해드리는 것이고, 다른 코치님들도 동일한 답변을
    하실 것 같아요~

    그리고 가장 중요한 것은,
    아키텍처는 프로그래밍 개발 지식만으론 만들어지지 않는다는걸 이해하셔야합니다.
    오늘 만드셨던 "나만의 유튜브 강의실"의 경우 지금 만드신 아키텍처가 적합한지 다시 생각해볼 필요가 있어요.

    "나만의 유튜브 강의실"은 어떤 도메인이고, 특성을 갖고있을까요?
    그리고 함께 개발하는 인원은 몇명인가요? 개발 기간은 언제까지일까요?
    이런 것처럼 다양한 환경적 요인과 비즈니스를 성사시키기 위해 아키텍처라는 틀을 만들고
    협업/목표를 이루기 위한 수단을 만드는 거라 이해하시면 되겠습니다.
```

### 1-6. Custom Element 사용시 재사용성
- [#129] Custom Element를 활용했을 때 재사용성에 대한 생각 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/129#pullrequestreview-914990036)
  - **Q** : Custom Element를 사용하는 데 저장된 비디오 리스트, 검색된 비디오 리스트가 비슷한 element라 재사용을 하고 싶습니다. 그런데 둘의 로직이 다른 부분이 존재하기 때문에 합치는 것이 복잡할 것 같아요. 이럴때는 어떻게 하는 것이 좋을까요?
  - **A** : 분리하는게 맞다고 생각합니다.
    1. Element들이 세부적으로 이벤트 바인딩도 제각기 다른 상태
    2. (합치게 되었을 때) 한 파일에 너무 역할이 크다면 말씀하신대로 복잡도가 올라가 유지보수에 더 힘들다.
    3. 중복이 있는 코드는 상속을 통해 해결해보자.
    4. 항상 재사용하는게 정답이라고 생각하지 않는다. 재사용빈도가 낮고 재사용했을 때 **복잡도를 증가시킬 수 있다고 생각하면 있는 그대로 사용해도 좋다.**


### 1-7. 다른 구조를 생각해야 할 필요성
- [#143] 자기가 짠 코드가 자주 헷갈린다면, 단순한 다른 구조를 생각해보자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/143#discussion_r832735972)

### 1-8. 상속의 결합도  
- [#137] 상속은 강한 결합도를 갖게 하므로 여러가지를 고려해서 결정해주자. (상속은 확실한 `is - a` 관계에서 사용해보자) - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/137#discussion_r830519847)
  ```js
  export default class VideoManager extends Observer {}
  // 과연 "VideoManager is an Observer" 일까?
  ```


<br/>


## 2. HTML 
### 2-1. parentNode 를 통한 DOM 접근
- [#149] event.target.parentNode.parentNode.remove();
	- 할아버지까지 찾고 있군요. 만약 기획이나 디자인 수정이 생기면 해당 로직은 의미가 있을까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/149#discussion_r830591414)

### 2-2. Dataset 속성 남용
-  [#149] **dataset 속성 남용** - DOM에 꽤나 많은 데이터를 때려넣는 느낌이다. 좋은 시도이지만 의존도가 점점 심해지고 있다. 브라우저 검사도구로 사용자가 무난하게 조작도 가능한 부분이다.  -[바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/149#pullrequestreview-915080811)
	
### 2-3. Input Radio 
- [#152] 본 영상 & 볼 영상 체크 할 때, 클래스 토글이 아니라 radio checked 속성 이용 가능  - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/152#discussion_r832467573)
```javascript
  <input type="radio" id="unseen-video-button" name="video" checked />
  <label for="unseen-video-button" class="button nav__button">👁 볼 영상</label>
  <input type="radio" id="watched-video-button" name="video" />
  <label for="watched-video-button" class="button nav__button">✅ 본 영상</label>

```


### 2-4. classList Toggle 사용하기
- [#115] classList toggle을 적극 활용해라 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/115#discussion_r830433395)

```javascript
//수정 전 코드
  if (this.willWatchVideoList.children.length !== 0) {
    this.element.classList.add("hide-element");
  } else if (this.willWatchVideoList.children.length === 0) {
    this.element.classList.remove("hide-element");
  }

  //수정 후 코드
  this.element.classList.toggle(
    "hide-element",
    this.willWatchVideoList.children.length !== 0
  );
```


### 2-5. Input type Search 속성
 - [#127] input type="search" 속성 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/127#discussion_r830486205
  - [[MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/search)] search 속성은 검색어를 입력할 수 있는 텍스트 필드를 정의한다. 검색 필드는 텍스트 필드와 기능적으로 완전히 똑같지만, 브라우저에 의해 다르게 표현될 수 있다.
  - search 필드에서는 반드시 name 속성을 설정해야 하며, name 속성이 설정되어 있지 않으면 서버로 제출되지 않는다. 가장 자주 사용되는 대표적인 name 속성값은 'q'이다.
```html
    <form action="/examples/media/action_target.php" method="get">
      검색 <input type="search" name="q" />
      <input type="submit" />
    </form>
```


### 2-6. Attribute 는 정적인 개념
- [#146] attribute는 '고유속성'에 가까운 정적인 개념에 가깝다. 동적으로 변하는 '상태'에는 적합하지 않다.
  - https://ko.javascript.info/dom-attributes-and-properties#ref-1789

### 2-7. 유일한 Id 를 사용하기
- [#123] 같은 아이템 (id가 같은 아이템)을 저장하는 경우에는 오류를 발생시킬 수 있으므로 중복 id를 저장하는 일을 피하자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/123#discussion_r830629853)


### 2-8. <br> 태그 
- [#143] [`br`](https://developer.mozilla.org/ko/docs/Web/HTML/Element/br)태그 같은 경우 공식 문서에서도, `<br />`보다는 `<br>`을 사용하고 있다.! 이번 기회에 `/`를 제거해보자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/143#discussion_r831049297)

<br/>

## 3. 네이밍
### 3-1. 간결한 용어 vs 길지만 유추가능한 언어 
- [#141] 간결한 용어사용보다 훨씬 중요한 것이 ‘문맥에 따라 충분히 유추가능한 어휘 사용’이다. 단어가 길어지더라도 누구든 그 의미를 파악할 수 있도록 보다 친절하게 작성해주시길  - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/141#pullrequestreview-915072062)

### 3-2. 행동에 집중하기 vs 관계에 집중하기
- [#149] 관계에 집중하는 방법 vs 행동과 명확함에 집중하는 방법 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/149#￼discussion_r830591040)
	- 행동과 명확함에 집중하기 : `savedVideos & videoToSave`
	- 관계에 집중하기 : `savedVideos & savedVideo`


### 3-3. Show vs Move 접두사
- [#116] show vs move 접두사 의미 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/116#discussion_r830470580)
  - css를 통해서 view를 제어하고 있기 때문에 show란 이름이 더 적절하다.
  - move란 이름은 해당페이지로 history.push되거나 link로 이동하는 느낌이다.

### 3-4. Return 값을 예측게 하는 네이밍
- [#143] 메서드 이름을 보고 어떤 return값이 나올지 추측해보는 것이 네이밍 작성하는데 좋은 접근방법 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/143#discussion_r832737482)


<br/>

## 4. 테스트
### 4-1. 목데이터 가공
- [#132] 목데이터에는 가공 후 값이 들어야가야 할까, 가공 전 값이 들어가야 할까? -[바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/132#discussion_r831025225)
	- 가공 후 값이 들어간다면 기획자가 가공 방법을 변경 할 때마다 목데이터를 한땀 한땀 바꿔줘야 한다.
	- 따라서, 가공 전 값을 목데이터로 넣고, 테스트에서 가공해주는게 좋다.


### 4-2. cypress.viewport 속성
- [#149] cypress viewport 수정 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/149#discussion_r830591830)
	- `cy.viewport(1536, 960);`
	- 주어진 검색 모달창의 크기가 width: 1080px; height: 727px; 인데 cypress 디폴트 뷰포트 크기가 조금 작아서 모달창의 바깥 영역을 클릭하는 것에 실패함. 그래서 cypress 테스트를 진행할 때 검색모달의 바깥부분을 클릭할 수 있도록 수정

### 4-3. Cypress config 로 baseUrl 관리하기
- [#118] cypress config로 baseUrl를 관리할 수 있다. -[바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/118#discussion_r830497307)
```jsx
  //cypress.json
  {
    "supportFile": false,
    "baseUrl": "https://nan-noo.github.io/javascript-youtube-classroom"
  }
```
  - cypress config 관련 링크 - [여기](https://docs.cypress.io/guides/references/configuration#cypress-json)

### 4-4. 웹스토리지 , API Mocking 테스트의 가치
- [#147] 웹스토리지나 API 자체를 Mocking하여 테스트하는 것에 의미가 없지 않다.
  - [질문] 제가 로컬스토리지를 mocking해서 테스트하려는 것은 "저장한 영상을 로컬스토리지에서 불러올 수 있어야 한다."인데, 이처럼 유닛테스트에서 웹스토리지나 API 자체를 Mocking해서 테스트하는 것에 어떤 의미가 있는 것인지 고민이 떠올랐어요. 결국 테스트를 위한 테스트가 될 것 같다는 생각이 들기도 해서요. 해당 테스트가 유닛테스트에서 불필요할 것 같아, 지울까 하는 고민이 드는데, 어떻게 생각하시나요?
  - [답변] 의미 없지 않습니다! 실제 메소드가 동작하는지 로컬스토리지에 데이터가 있는 상황, 없는 상황을 가정해서 테스트를 할 수 있거든요! 추후엔 오히려 더 테스트 케이스를 늘려서 보강하셔야합니다!


### 4-5. 특정 아이템 삭제 여부 테스트
- [#123] (cypress) 특정 아이템이 삭제되었는지 확인하는 테스트에서는 그 아이템이 존재하는 지에 대한 테스트를 진행하자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/123#discussion_r830628283)

### 4-6 cypress api 요청 테스트
- [#129] (cypress) API 요청을 테스트할 때 `cy.wait()`을 써서 API 응답을 기다릴 수는 있다. 그런데 구체적인 시간을 지정해주는 것이기 때문에, 그 전에 API 응답이 도착했으면 비효율적일 수 있다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/129#discussion_r830480992)
  - cypress의 intercept를 통해 API 응답을 invoke해서 해당 API Fetch를 wait해줄 수 있다.
  - 그런데 해당 크루분이 `cy.wait()`를 지우고 `cy.get()`만 사용해봤더니, 정상적으로 테스트가 성공했다. 이유는 `cy.get()`은 기본적으로 기다리는 시간이 [4초](https://docs.cypress.io/guides/references/configuration#Timeouts)이기 때문이다! (`option: defaultCommandTimeout, default: 4000`) - **지정된 시간 내에 elmeent가 생성되면 이후에 체이닝된 should를 검증**
  - 그러므로 cypress가 추천하는 방법인 `cy.intercept()`를 사용해보자!
```js
    cy.intercept('GET', '/users', [{ name: 'Maggy' }, { name: 'Joan' }]);
    cy.get('#fetch').click();
    cy.wait(4000); // <--- this is unnecessary
    cy.get('table tr').should('have.length', 2);
    // 테이블의 길이가 2가 될 때까지 기다린다.
```
  - 만약 `cy.wait()`를 쓰고 싶다면 `aliased route`를 이용해서 특정한 작업을 기다리도록 해주자.
```js
    cy.intercept('GET', '/users', [{ name: 'Maggy' }, { name: 'Joan' }]).as(
      'getUsers'
    );
    cy.get('#fetch').click();
    cy.wait('@getUsers'); // <--- wait explicitly for this route to finish
    cy.get('table tr').should('have.length', 2);
```


### 4-7. Cypress Command 사용하기
- [#129] (cypress) 중복이 있는 테스트 코드를 분리해주고 싶다면 [command](https://docs.cypress.io/api/cypress-api/custom-commands#Syntax)를 사용해보자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/129#discussion_r830481260)
```js
  // command
  Cypress.Commands.add('hasCountVideoItems', ($container, count) => {
    cy.get($container).find('.video-item').should('have.length', count);
    // 영상 목록에 video-item이 몇개 있을지
  });

  // test case
  it('저장한 영상을 볼 영상 목록에서 확인할 수 있어야 한다.', () => {
    // ..add

    cy.hasCountVideoItems('.playlist-videos-container', 1); // command 이용
  });

  it('영상을 삭제할 수 있어야 한다.', () => {
    // ..delete

    cy.hasCountVideoItems('.watched-videos-container', 0);
  });
```


### 4-8. Cypress as 사용하기

- [#151] (cypress) 테스트에서 반복적으로 같은 버튼을 클릭한다면? [`as`](https://docs.cypress.io/guides/core-concepts/variables-and-aliases)를 사용해보자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/151#discussion_r830634780)
```js
  beforeEach(() => {
    cy.get('saved-list')
      .find('.video-item__remove-button')
      .first()
      .as('first-video-button'); // as
  });

  it('삭제 버튼을 클릭하면 사용자에게 삭제할 것인지 물어야 한다.', () => {
    cy.get('@first-video-button') // as
      ...
  });
```


### 4-9. Jest 특정 경로 제외 설정
- [#139, #151] (jest) package.json에 [`testPathIgnorePatterns`](https://runebook.dev/ko/docs/jest/configuration#testpathignorepatterns-arraystring)를 활용하면, 특정 경로를 테스트에서 제외시킬 수 있다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/139#discussion_r830580284)
  - jest에서 cypress테스트가 진행되지 않게 해주려면 해주자.
```json
      "jest": {
        "testPathIgnorePatterns": [
          "cypress"
        ]
     },
```


<br/>

## 5. 클린코드
### 5-1. NoFlag 의 사용
- [#149]  renderNoSavedVideo (저장된 영상 없음 상태 렌더링) & renderSavedVideo(저장된 영상 있을시 상태 렌더링)로 가져가기 보다는 renderVideo & renderSavedVideo 로 가져가는게 더 효율적이지 않은가? 렌더하는 비디오의 종류가 3가지 이상이 아닌데 왜 No flag 를 써야하나 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/149#discussion_r830593341)
	- 크루: ‘저장된 비디오가 없습니다’를 보여주는 기능이 renderNoSavedVideo인데, renderSavedVideo와 정반대 의미를 가지고 있다고 생각했다. 제안해주신 renderVideo와 renderSavedVideo로 가져가나면 renderVideo가 하는 일은 무엇? 
	- 리뷰어 : 그럼 앞으로 기능이 확장되고, 비슷한 리스트가 늘어날 때 마다 모든 리스트에 no-상태를 추가할것인가? 
	- 즉, 기능 확장에 의해서 어떤 비디오 리스트가 다양해질 수 있는데 그럴 때마다 noSomeVideos 라고 플래그를 계속 만드는 것이 문제가 될 수가 있다. 

### 5-2. Map을 사용하여 DOM 조작 최소화 하기
- [#132] 데이터를 forEach 로 돌면서 여러번 insertAdjacentHTML을 해주는 상황, 아래와 같이 map 으로 한번만 insertAdjacentHTML 실행하도록 리팩토링 가능 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/132#￼￼￼discussion_r831009399)
```javascript
const template = videoStorage.getVideoDataList().map(videoData => {
      return `템플릿만들기`
 })
this.$storedVideoList.insertAdjacentHTML('beforeend', template.join(''));
```


### 5-3. 배열 Spread 의 비용
- [#115] 배열을 spread하는 작업은 매우 비싼 작업이다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/115#discussion_r830436124)
```js
  //해당 코드
  [
    ...ControlVideo.getStorageWillWatchVideoList(),
    ...ControlVideo.getStorageWatchedVideoList(),
  ].length > MAX_SAVE_VIDEO_COUNT;
```
  - 배열을 spread하는 것은 배열의 각 요소를 한 번씩 순회하는 비싼 작업입니다.
    여기서는 두 배열의 length를 합하는 것으로 충분해 보이네요.


### 5-4. For 문을 some 메서드 사용하여 리팩토링
- [#150] for문을 some 메서드를 활용하여 리팩토링 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/150#discussion_r830994669)
    - [MDN - some](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/some)
```js
// [before] for문
for (let idx = 0; idx < this.#savedVideoItems.length; idx += 1) {
  if (this.#savedVideoItems[idx].videoId === deleteVideoId) {
    this.#savedVideoItems.splice(idx, 1);
    return;
  }
}
```

```js
// [after] some 메서드
this.#savedVideoItems.some((item, idx) => {
  if (item.videoId === deleteVideoId) {
    this.#savedVideoItems.splice(idx, 1);
  }
  return item.videoId === savedId;
});
```


### 5-5. 배열의 구조분해 할당 사용하기
- [#132] 구조분해할당 활용하기
```javascript

// before 
      videoId: clickedVideo.children[4].dataset.videoid,
      publishedAt: clickedVideo.children[3].textContent,
      title: clickedVideo.children[1].textContent,
      url: clickedVideo.children[0].src,
      channelTitle: clickedVideo.children[2].textContent,

// after 
const [thumbnailImg, title, channelTitle, publishedAt, videoId] = videoInfo.children;
    return {
      videoId: videoId.dataset.videoId,
      publishedAt: publishedAt.textContent,
      title: title.textContent,
      url: thumbnailImg.src,
      channelTitle: channelTitle.textContent,
      type: VIDEO_TYPE.WATCH_LATER,
    };
  }

```

### 5-6. deepClone 메서드 분리
- [#151] deep clone해주는 작업을 특정 메서드에 그대로 넣어준다면, 해당 메서드의 역할을 파악하는 것에 어려움이 있을 수 있다. deep clone해주는 작업은 따로 메서드 분리해주자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/151#discussion_r830630646)

### 5-7. 연속적인 Return 사용
- [#143] return에서 또 return으로 이어지는 구조는 가독성이 떨어진다. 변수에 한번 저장해주고 return해주는 구조를 생각해보자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/143#discussion_r831059726)
```diff
  - return rawVideos.map(({ id, snippet }) => {
  - return {
  -  videoId: id.videoId,
  -  thumbnailUrl: snippet.thumbnails.default.url,
  -  title: snippet.title,
  -  channelTitle: snippet.channelTitle,
  -  publishTime: snippet.publishTime,
  -  };
  - });
  + const videos = rawVideos.map(...)
  + return videos
```

<br/>

## 6. CSS
### 6-1. Text overflow 속성
- [#126] css 로 화면에 보이는 최대 글자수 지정하기  - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/126#discussion_r830735124)
```css
.search-result-keyword {
  width: 300px;
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
}
```


### 6-2. Sticky Header
- [#127] 아이템을 나열하는 리스트 UI에서 자주 사용하는 방식 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/127#discussion_r830480679)
  - 원래는 내부 스크롤 없이 리스트가 페이지에 쭉 나열되고, **스크롤로 내리면 메뉴 바가 상단에 붙는 UI를 많이 쓴다.**
  - 관련 키워드 : sticky, fixed
  - 스티키라는 것은 css 속성값이라고도 하지만, UI 적으로도 스티키 헤더라고 부른다.


### 6-3. CSS 네 방향 속성
- [#147] css 네 방향 속성 관련
  - 예를 들어, `margin: 0 0 30px 0;` 과 `margin: 0 0 30px;`은 같다.
    - "위 오른쪽 아래 왼쪽" 에서 오른쪽과 왼쪽이 같으면 "위 오른쪽=왼쪽 아래 "처럼 마지막 왼쪽을 생략해도 된다.
    - 그리고 "위와 아래끼리", "오른쪽과 왼쪽끼리" 같은 경우 "위=아래 오른쪽=왼쪽" 처럼 두 개로 축약해도 된다.

### 6-4. 의사 클래스와 의사 요소
- [#147] `CSS 의사 클래스`와 `CSS 의사요소`를 구분하자.
  - 브라우저에서 콜론 하나(:)는 `CSS 의사 클래스`에서 사용하고, 콜론 두 개(::)는 `CSS 의사 요소`에서 사용한다.
  - CSS3에 들어서면서, `CSS 의사 요소`와 `CSS 의사 클래스`를 구분하기 위해, 콜론 두 개인 `::before`과 같은 구문을 도입했다.
    - CSS3에서는 `::before`이고 CSS2에서는 원래 `:before`로 사용되었다.
  - 아직 브라우저에서는 CSS2 구문인 `:before`를 아직 허용한다고 하지만, before는 `의사 요소`이므로 `::before` 구문으로 사용하는 것이 권장된다.
  - 즉, CSS3 도입 취지에 부합하도록, `의사요소(:active, :hover 등)`와 `의사클래스(::before, ::after 등)`를 구분하여 콜론 개수를 달리 표시해야 한다.
  - [MDN- CSS 의사 클래스](https://developer.mozilla.org/ko/docs/Web/CSS/Pseudo-classes)
  - [MDN- CSS 의사 요소](https://developer.mozilla.org/ko/docs/Web/CSS/Pseudo-elements)


### 6-5. 색상 팔레트 기준
- [#129] 색상 팔레트를 나눠줄 때 100단위로 나눠주는 게 좋다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/129#discussion_r830478991)


### 6-6. Grid 
- [#151] `grid`를 사용해줬을 때 스크린 사이즈에 따라 보여지는 cell의 개수를 조정하고 싶다면, [`grid-template-columns`](https://studiomeal.com/archives/533)를 사용해보자.! - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/151#discussion_r830633346)
```css
  @media only screen and (min-width: 600px) {
    .video-list {
      grid-template-columns: repeat(2, minmax(0, 1fr));
    }
    /* repeat(반복횟수, 반복값) */
    /* minmax(최소 길이, 최대 길이) */
  }
  @media only screen and (min-width: 960px) {
    .video-list {
      grid-template-columns: repeat(3, minmax(0, 1fr));
    }
  }
  @media only screen and (min-width: 1280px) {
    .video-list {
      grid-template-columns: repeat(4, minmax(0, 1fr));
    }
  }
```

<br/>

## 7. 성능
### 7-1. N 회 렌더링과 브라우저 렌더링
- [#152] *N회 렌더링하는 문제* 백엔드 개발자는 서버의 리소스를 먹고 프론트엔드 개발자는 사용자의 리소스를 먹습니다. 그런데 유튜브 미션 요구사항을 수행하며 (스크롤 + Data Fetch + DOM Append)을 일으키고 있는데, 여기서 가장 심각한 실수가 N회 렌더링하는 것입니다.나중에 분명 Reflow & Repaint를 공부하실텐데 꼭 이 리뷰를 기억해주세요.  - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/152#pullrequestreview-916826903)

### reflow / repaint는?
![](https://developers.google.com/web/fundamentals/performance/rendering/images/intro/frame-full.jpg)
- reflow : 생성된 DOM 노드의 레이아웃 수치(너비,높이,위치) 변경 시 영향받은 모든 노드의 수치를 다시 계산하여, 렌더트리를 재생성하는 과정 (Layout)
- repaint : reflow 과정이 끝난 후 재생성된 렌더트리를 다시 그리는 과정  (Paint)

### reflow 는 언제 발생하나?
- 노드 추가 제거 
- 요소 위치 변경 
- 요소 크기 변경 (margin,padding,border,width,height 등)
- 페이지 초기 랜더링
- 윈도우 리사이징 

### repaint 만 일어나는 경우
- 화면의 구조가 변경되지 않은, 화면 변화의 경우
- css의 opacity, background-color, visibility, outline 속성들

### reflow 최적화 방법 몇가지 (DOM관련)
- 최대한 가장 하위노드만 변경하기 
    - 상위노드가 변경되면, 상위노드부터 -> 자식노드까지 모두 변경되므로, 가장 하위 노드만 변경한다면 리플로우 범위를 줄일 수 있다.
- DOM 사용 최소화 하기
    - DOM Fragment를 사용하여 DOM 을 추가 할 때, DOM 접근 최소화 하기
```javascript
const frag = document.createDocumentFragment();
const ul = frag.appendChild(document.createElement('ul'));

for (let i = 1; i <= 3; i++) {
  li = ul.appendChild(document.createElement('li'));
  li.textContent = `item ${ i }`;
}

document.body.appendChild(frag);
```


### 7-2. 로컬스토리지 json데이터 get/set 의 비용 
- [#129] localStorage 에서 배열인 json 데이터를 꺼내오는건 비용이 크다. 프로젝트에서 storage 와 같은 데이터를 관리해보는게 좋다.
실제 localStorage 데이터를 수정해야할 경우에만 localStorage 에 접근하고, 데이터 가져오는 일은 서비스 처음 시작할 때 한번만 해보도록 한다.  - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/126#discussion_r831002052)

<br/>

## 8. 클래스
### 8-1. 인스턴스 export 하기
- [#133](https://github.com/woowacourse/javascript-youtube-classroom/pull/133#discussion_r831692497) 인스턴스를 여러군데에서 사용할 경우 인스턴스를 생성해서 export 한다.
	- 클래스 맴버 변수로 가지고 있을 이유가 없을 경우 아래와 같이 인스턴스를 export 해주면 필요한 곳에서 사용해 줄 수 있다.
```js
class YoutubeAPI {}
const youtubeAPI = new YoutubeAPI();
export default youtubeAPI;
```


### 8-2. Static 멤버만 존재하는 클래스의 존재 가치

- [#115] static 멤버만 존재하는 클래스가 클래스로서의 가치가 있을까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/115#discussion_r830430735)
  - 해당 클래스를 객체로 전환하는 것으로 충분하다.


### 8-3. 클래스 관련 파일 관리법
- [#148] 클래스에 연관된 헬퍼함수, 템플릿, 상수 파일들의 분리 방법 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/148/files/1ba87826bed272037aac5446acbadcd4565ad3a3#r830620883)
  - 예들 들어, SearchModal 폴더를 만들고 하위에 SearchModal.js, SearchModal.helper.js, SearchModal.template.js, SearchModal.constants.js 이렇게 나누는것도 한 방법이다.

<br/>

## 9. 객체 지향
### 9-1. 객체지향에서 setter 사용 
- [#147] 객체지향에서는 setter는 사용하지 않는 것이 권장된다.
  - 객체지향에서 말하길, setter와 getter에서 setter는 사용하지 않는걸 권장하고 있다. 그 이유는 객체지향은 객체간 협력을 나타내는 것인데, setter는 객체간 협력보다는 "의존" 혹은 "명령"이기 때문이다.
  - 예를 들어, A와 B가 모래성을 쌓고 있는데, 이들은 멋진 모래성을 쌓는게 목표이다. 그렇게 열심히 둘이 모래성을 쌓고 있는데, B가 실수로 A에게 "파괴하라!" 라고 `명령(set)`을 하게 된다면, A는 모래성을 파괴하게 된다.
    - 만약 setter를 안쓰고 `행동 단위`로 했다면 어땠을까? A와 B는 C라는 목표를 달성하기 위해 행동하는 객체이게 된다. C라는 목표를 모래성으로 설정하고. A와 B는 메소드로 PileUp() 이라는 메소드로 쌓을 수 있다. 즉, PileUp(C)를 통해 C를 서로가 쌓을 수 있게 된다. 여기서 C가 B로 바뀌던 A로 바뀌던, 결국엔 쌓기만 한다. `쌓는 행위` 외에 다른 것들은 할 수 없으니 비교적 명령보다 `안전`하다.
  - 따라서, setter와 같은 명령 대신에 행동 단위로 하는 것이 좋다.


### 9-2. Has-a 와 is-a 의 차이점

- [#142] 객체지향적 관점에서 `has-a`와 `is-a` 차이점 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/142#discussion_r830583944)

  - is-a
    - is-a는 추상화(형식이나 클래스와 같은)들 사이의 포함 관계를 의미하며, 한 클래스 A가 다른 클래스 B의 서브클래스(파생클래스)임을 이야기합니다. 다른 말로, 타입 A는 타입 B의 명세(specification)를 암시한다는 점에서 타입 B의 서브타입이라고도 할 수 있습니다. is-a 관계는 타입 또는 클래스 간의 has-a 관계와는 대조됩니다. has-a 및 is-a 관계들 간의 혼동은 실세계 관계 모델에 대한 설계에 있어 자주 발견되는 에러입니다. is-a 관계는 또한 객체 또는 타입 간의 instance-of 관계와도 대조됩니다.
      - 예) 학생은 사람이다(o)    
      - 코드를 공통적으로 관리하기 때문에 코드의 추가 및 변경이 매우 용이하다.    
      - 조상클래스의 변경이 있으면 자손클래스는 영향을 주지만, 자손클래스가 변경되는 것은 조상클래스에 아무런 영향을 주지 않는다
      - 자손클래스의 인스턴스를 생성하면 조상클래스의 멤버와 자손 클래스의 멤버가 합쳐진 하나의 인스턴스로 생성된다
![image](https://user-images.githubusercontent.com/59413128/160842017-4e59add0-2e4f-4d03-8d1e-c4a567e8bf03.png)
  - has-a
    - has-a는 구성 관계를 의미하며 한 오브젝트(구성된 객체, 또는 부분/멤버 객체라고도 부릅니다)가 다른 오브젝트(composite type이라고 부릅니다)에 "속한다(belongs to)"를 말합니다. 단순히 말해, has-a 관계는 객체의 멤버 필드라고 불리는 객체를 말하며, Multiple has-a 관계는 소유 계층구조를 형성하기 위해 결합하는 경우를 말합니다.
      - 예) 학생은 책을 가지고있다(o) 
      - 한 클래스의 멤버변수로 다른 클래스를 선언하는 것
      - 작은 단위의 클래스를 먼저 만들고, 이 들을 조합해서 하나의 커다란 클래스를 만든다.
![image](https://user-images.githubusercontent.com/59413128/160842074-1885d659-f21b-441a-abc5-ed2e20c0dcb2.png)

<br/>

## 10. 객체
### 10-1. JSON.stringify 를 이용한 객체 비교
- [#151] `JSON.stringify()`를 사용하여 두 객체가 같은 지 비교할 때, 키의 순서가 바뀌면 제대로 된 [검증을 할 수 없다](https://betterprogramming.pub/why-you-shouldnt-use-json-stringify-to-compare-objects-in-javascript-c9a16b7331e).! - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/151#discussion_r830633031)

<br/>


## 11. 기타
### 11-1. Cache Invalidation
- 캐시 무효화(Invalidation)는 요청에 대해 캐시 데이터가 사용되어야 할 지 아닐지를 결정하고 항상 최신의 데이터를 반환하도록 하는 일련의 과정입니다. 일반적인 캐시 무효화 방법은 다음과 같습니다.
- **Expiration Time**
  캐시가 생성될 때 지정한 TTL에 의해 캐시의 수명이 결정되고 수명이 다한 캐시는 사용되지 않습니다. 가장 일반적인 캐시 무효화 전략입니다.
  TTL을 보수적으로 정했을 때, 해당 시간이 지나면 최신의 데이터가 반환되기 때문에 안전한 방법입니다. 캐시를 잘 관리할 자신이 없다면 TTL을 짧게 정하는 것이 좋습니다.

<br>

- **Freshness Caching Verification**
  별도의 검증 절차를 통해 캐시가 유효한지 확인하는 방법입니다.
  예를 들어 원본 데이터가 업데이트 된 시간과 캐시가 생성된 시간을 비교하여 캐시의 유효성을 판별합니다. 단점은 캐시가 사용될 때마다 추가적인 확인 절차를 수행하기 때문에 이에 따른 오버헤드가 발생합니다.

<br>

- **Active Application Invalidation**
  데이터가 수정되는 코드가 수행될 때마다 백엔드에서 연관된 캐시를 무력화하는 방법입니다. 캐시를 세밀하게 관리할 수 있기 때문에 이상적으로 설계되었을 때 가장 좋은 효율을 발휘합니다. 1번 방식이 수동적인 방법인데 비해 아주 능동적인 방법입니다. 별도의 코드를 통해 캐시를 컨트롤 하기 때문에 가장 에러가 발생하기 쉽고 관리가 까다롭습니다.

<br>

- 더 자세하게 알고 싶다면 [여기](https://toss.tech/article/smart-web-service-cache)를 참고하세요. - toss tech


### 11-2. 아키텍쳐 구조도 그리기
- [#131] 아키텍처 맵(구조도) - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/131#issuecomment-1073797128)
  - 아키텍처 맵은 다음과 같이 두 가지로 방법으로 그려볼 수 있다.
    - 폴더 스트럭쳐: 폴더 스트럭쳐단위로, 서로의 폴더가 어떤걸 참조하고 있는지 그리는 방법
    - SoC: 관심사 단위로 어떤 것들이 어떻게 분리되고 어떻게 참조하고 있는지 그리는 방법



### 11-3. 이벤트 위임시 early return 사용하기
- [#130] 이벤트 위임을 해줄 때 early return을 활용해보자! - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/130#discussion_r831294765)
```js
  // before
  if (e.target.classList.contains('video-item__check-button')) {
    // do something
  }

  // after
  if (!e.target.classList.contains('video-item__check-button')) {
    return;
  }
  // do something
```


### 11-4. Try-catch-finally
- [#151] 오류처리에 집중하느라 이후에 작업을 놓칠 수 있다. `try-catch`를 사용했을 때 `finally`를 한번씩 생각해주자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/151#discussion_r830629741)

### 11-5. 재미있는 console 메서드들

- [#151] log를 보기 쉽게 들여쓰기 하고 싶다면, [`console.group()`](https://developer.mozilla.org/ko/docs/Web/API/Console/group)을 사용해보자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/151#discussion_r830642019)  
  ![mdn](https://developer.mozilla.org/en-US/docs/Web/API/console/group/nesting.png)
```js
  console.log('This is the outer level');

  console.group(); // lv2
  console.log('Level 2');

  console.group(); // lv3
  console.log('Level 3');
  console.warn('More of level 3');
  console.groupEnd(); // lv3

  console.log('Back to level 2');
  console.groupEnd(); // lv2

  console.log('Back to the outer level');
```
- [#151] [log의 스타일도 커스텀](https://www.samanthaming.com/tidbits/40-colorful-console-message/)이 가능하다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/151#discussion_r830642019)

```js
  console.log('%cHello', 'color: green; background: yellow; font-size: 30px'); // [1]

  console.log('Nothing here %cHi Cat %cHey Bear', 'color: blue', 'color: red'); // [2]
```

  <img width="678" alt="스크린샷 2022-03-29 오후 9 21 47" src="https://user-images.githubusercontent.com/52737532/160610228-d5cedd6b-22af-4432-9c13-da818dc5667c.png">

- [#130] 이벤트 위임을 해줄 때 early return을 활용해보자! - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/130#discussion_r831294765)
```js
  // before
  if (e.target.classList.contains('video-item__check-button')) {
    // do something
  }

  // after
  if (!e.target.classList.contains('video-item__check-button')) {
    return;
  }
  // do something
```
