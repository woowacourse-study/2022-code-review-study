# LEVEL 1 나만의 유튜브 강의실 STEP 2 - 무비😎

## 분석 담당 코드

- 무비 [PR 123](https://github.com/woowacourse/javascript-youtube-classroom/pull/123) X 티거 [PR 129](https://github.com/woowacourse/javascript-youtube-classroom/pull/129)
- 우디 [PR 151](https://github.com/woowacourse/javascript-youtube-classroom/pull/151) X 시지프 [PR 143](https://github.com/woowacourse/javascript-youtube-classroom/pull/143)
- 하리 [PR 119](https://github.com/woowacourse/javascript-youtube-classroom/pull/119) X 민초 [PR 130](https://github.com/woowacourse/javascript-youtube-classroom/pull/130)
- 비녀 [PR 139](https://github.com/woowacourse/javascript-youtube-classroom/pull/139) X 코이 [PR 137](https://github.com/woowacourse/javascript-youtube-classroom/pull/137)

<br />
<br />

## 피드백 정리

😛 링크를 누르면 해당 discussion으로 이동! 😛

<br />

### 구조

- [#129] Custom Element를 활용했을 때 재사용성에 대한 생각 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/129#pullrequestreview-914990036)
  - **Q** : Custom Element를 사용하는 데 저장된 비디오 리스트, 검색된 비디오 리스트가 비슷한 element라 재사용을 하고 싶습니다. 그런데 둘의 로직이 다른 부분이 존재하기 때문에 합치는 것이 복잡할 것 같아요. 이럴때는 어떻게 하는 것이 좋을까요?
  - **A** : 분리하는게 맞다고 생각합니다.
    1. Element들이 세부적으로 이벤트 바인딩도 제각기 다른 상태
    2. (합치게 되었을 때) 한 파일에 너무 역할이 크다면 말씀하신대로 복잡도가 올라가 유지보수에 더 힘들다.
    3. 중복이 있는 코드는 상속을 통해 해결해보자.
    4. 항상 재사용하는게 정답이라고 생각하지 않는다. 재사용빈도가 낮고 재사용했을 때 **복잡도를 증가시킬 수 있다고 생각하면 있는 그대로 사용해도 좋다.**
- [#143] 자기가 짠 코드가 자주 헷갈린다면, 단순한 다른 구조를 생각해보자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/143#discussion_r832735972)
- [#137] 상속은 강한 결합도를 갖게 하므로 여러가지를 고려해서 결정해주자. (상속은 확실한 `is - a` 관계에서 사용해보자) - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/137#discussion_r830519847)
  ```js
  export default class VideoManager extends Observer {}
  // 과연 "VideoManager is an Observer" 일까?
  ```

<br />

### 테스트

- [#123] (cypress) 특정 아이템이 삭제되었는지 확인하는 테스트에서는 그 아이템이 존재하는 지에 대한 테스트를 진행하자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/123#discussion_r830628283)
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

- [#139, #151] (jest) package.json에 [`testPathIgnorePatterns`](https://runebook.dev/ko/docs/jest/configuration#testpathignorepatterns-arraystring)를 활용하면, 특정 경로를 테스트에서 제외시킬 수 있다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/139#discussion_r830580284)
  - jest에서 cypress테스트가 진행되지 않게 해주려면 해주자.
    ```json
      "jest": {
        "testPathIgnorePatterns": [
          "cypress"
        ]
     },
    ```
- [#137] (cypress) 반복적으로 `localhost:----`에 cy.visit()해줘야 한다면, cypress config에서 [`baseUrl`설정](https://docs.cypress.io/guides/references/configuration#Options)을 해주자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/137#discussion_r830517643)

<br />

### 데이터

- [#123] 같은 아이템 (id가 같은 아이템)을 저장하는 경우에는 오류를 발생시킬 수 있으므로 중복 id를 저장하는 일을 피하자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/123#discussion_r830629853)

<br />

### HTML

- [#143] [`br`](https://developer.mozilla.org/ko/docs/Web/HTML/Element/br)태그 같은 경우 공식 문서에서도, `<br />`보다는 `<br>`을 사용하고 있다.! 이번 기회에 `/`를 제거해보자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/143#discussion_r831049297)

<br />

### CSS

- [#129] 색상 팔레트를 나눠줄 때 100단위로 나눠주는 게 좋다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/129#discussion_r830478991)
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

<br />

### 함수

- [#151] deep clone해주는 작업을 특정 메서드에 그대로 넣어준다면, 해당 메서드의 역할을 파악하는 것에 어려움이 있을 수 있다. deep clone해주는 작업은 따로 메서드 분리해주자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/151#discussion_r830630646)
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

<br />

### 객체

- [#151] `JSON.stringify()`를 사용하여 두 객체가 같은 지 비교할 때, 키의 순서가 바뀌면 제대로 된 [검증을 할 수 없다](https://betterprogramming.pub/why-you-shouldnt-use-json-stringify-to-compare-objects-in-javascript-c9a16b7331e).! - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/151#discussion_r830633031)

  ```js
  JSON.stringify({a: 'a', b: 'b'}) === JSON.stringify({{a: 'a', b: 'b'}} //true

  JSON.stringify({b: 'b', a: 'a'}) === JSON.stringify({{a: 'a', b: 'b'}} //false
  ```

<br />

### 유용한 구문

- [#151] 오류처리에 집중하느라 이후에 작업을 놓칠 수 있다. `try-catch`를 사용했을 때 `finally`를 한번씩 생각해주자. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/151#discussion_r830629741)
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

<br>

### 네이밍

- [#143] 메서드 이름을 보고 어떤 return값이 나올지 추측해보는 것이 네이밍 작성하는데 좋은 접근방법 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/143#discussion_r832737482)
