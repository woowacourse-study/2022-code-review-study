# Level1 Youtube Step2(담당 PR 번호들) - 호프

- 분석 담당 코드 

    - 호프 [#121](https://github.com/woowacourse/javascript-youtube-classroom/pull/121)
    - 콤피  [#141](https://github.com/woowacourse/javascript-youtube-classroom/pull/141)
    - 돔하디 [#149](https://github.com/woowacourse/javascript-youtube-classroom/pull/149)
    - 블링 [#126](https://github.com/woowacourse/javascript-youtube-classroom/pull/126)
    - 밧드 [#125](https://github.com/woowacourse/javascript-youtube-classroom/pull/125)
    - 나인 [#132](https://github.com/woowacourse/javascript-youtube-classroom/pull/132)
    - 안 [#152](https://github.com/woowacourse/javascript-youtube-classroom/pull/152)

<br/>

# 피드백 정리
## 1. 구조
- [#149] storage 위에도 대략 설명을 드렸지만 해당 유튜브 앱의 영속성을 담당하는 중요한 부분입니다. 하지만 네이밍과는 다르게 도메인에 종속성이 있어서 재활용이 어려울 것 같네요
- [#126] view 들을 관장하는 view 클래스가 있는 경우-  보통 각 뷰는 독립적이어야 하는데 뷰들을 관장하는 뷰가 있다는건 뷰의 역할은 아닌 것 같다.  -  [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/126#discussion_r831918465)

<br/>


## 2. DOM & HTML 
- [#149] event.target.parentNode.parentNode.remove();
	- 할아버지까지 찾고 있군요. 만약 기획이나 디자인 수정이 생기면 해당 로직은 의미가 있을까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/149#discussion_r830591414)

-  [#149] **dataset 속성 남용** - DOM에 꽤나 많은 데이터를 때려넣는 느낌이다. 좋은 시도이지만 의존도가 점점 심해지고 있다. 브라우저 검사도구로 사용자가 무난하게 조작도 가능한 부분이다.  -[바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/149#pullrequestreview-915080811)

- [#152] 본 영상 & 볼 영상 체크 할 때, 클래스 토글이 아니라 radio checked 속성 이용 가능  - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/152#discussion_r832467573)
```javascript
  <input type="radio" id="unseen-video-button" name="video" checked />
  <label for="unseen-video-button" class="button nav__button">👁 볼 영상</label>
  <input type="radio" id="watched-video-button" name="video" />
  <label for="watched-video-button" class="button nav__button">✅ 본 영상</label>

```

<br/>


## 3. 네이밍
- [#141] 간결한 용어사용보다 훨씬 중요한 것이 ‘문맥에 따라 충분히 유추가능한 어휘 사용’이다. 단어가 길어지더라도 누구든 그 의미를 파악할 수 있도록 보다 친절하게 작성해주시길  - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/141#pullrequestreview-915072062)

- [#149] 관계에 집중하는 방법 vs 행동과 명확함에 집중하는 방법 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/149#￼discussion_r830591040)
	- 행동과 명확함에 집중하기 : `savedVideos & videoToSave`
	- 관계에 집중하기 : `savedVideos & savedVideo`

- [#132] 목데이터에는 가공 후 값이 들어야가야 할까, 가공 전 값이 들어가야 할까? -[바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/132#discussion_r831025225)
	- 가공 후 값이 들어간다면 기획자가 가공 방법을 변경 할 때마다 목데이터를 한땀 한땀 바꿔줘야 한다.
	- 따라서, 가공 전 값을 목데이터로 넣고, 테스트에서 가공해주는게 좋다.


<br/>

## 4. 테스트
- [#149] cypress viewport 수정 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/149#discussion_r830591830)
	- `cy.viewport(1536, 960);`
	- 주어진 검색 모달창의 크기가 width: 1080px; height: 727px; 인데 cypress 디폴트 뷰포트 크기가 조금 작아서 모달창의 바깥 영역을 클릭하는 것에 실패함. 그래서 cypress 테스트를 진행할 때 검색모달의 바깥부분을 클릭할 수 있도록 수정

<br/>

## 5. 함수 
- [#149]  renderNoSavedVideo (저장된 영상 없음 상태 렌더링) & renderSavedVideo(저장된 영상 있을시 상태 렌더링)로 가져가기 보다는 renderVideo & renderSavedVideo 로 가져가는게 더 효율적이지 않은가? 렌더하는 비디오의 종류가 3가지 이상이 아닌데 왜 No flag 를 써야하나 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/149#discussion_r830593341)
	- 크루: ‘저장된 비디오가 없습니다’를 보여주는 기능이 renderNoSavedVideo인데, renderSavedVideo와 정반대 의미를 가지고 있다고 생각했다. 제안해주신 renderVideo와 renderSavedVideo로 가져가나면 renderVideo가 하는 일은 무엇? 
	- 리뷰어 : 그럼 앞으로 기능이 확장되고, 비슷한 리스트가 늘어날 때 마다 모든 리스트에 no-상태를 추가할것인가? 
	- 즉, 기능 확장에 의해서 어떤 비디오 리스트가 다양해질 수 있는데 그럴 때마다 noSomeVideos 라고 플래그를 계속 만드는 것이 문제가 될 수가 있다. 
- [#132] 데이터를 forEach 로 돌면서 여러번 insertAdjacentHTML을 해주는 상황, 아래와 같이 map 으로 한번만 insertAdjacentHTML 실행하도록 리팩토링 가능 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/132#￼￼￼discussion_r831009399)
```javascript
const template = videoStorage.getVideoDataList().map(videoData => {
      return `템플릿만들기`
 })
this.$storedVideoList.insertAdjacentHTML('beforeend', template.join(''));

``` 
- [#132] forEach 내부의 return 문은 continue 같은 역할을 한다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/132#discussion_r831011837)
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


<br/>


## 6. CSS
- [#126] css 로 화면에 보이는 최대 글자수 지정하기  - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/126#discussion_r830735124)
```css
.search-result-keyword {
  width: 300px;
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
}
```

<br/>

## 7. 성능 
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

<br/>

## 8. 그 외
- [#129] localStorage 에서 배열인 json 데이터를 꺼내오는건 비용이 크다. 프로젝트에서 storage 와 같은 데이터를 관리해보는게 좋다.
실제 localStorage 데이터를 수정해야할 경우에만 localStorage 에 접근하고, 데이터 가져오는 일은 서비스 처음 시작할 때 한번만 해보도록 한다.  - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/126#discussion_r831002052)

- [#129] 가독성을 위해 선언부가 끝나고 나면 한 번 개행해주시면 어떨까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/126#discussion_r831034576)

<br/>

## 좋은 말
- [#121]  개인적으로는 ‘효율’은 ‘완성’이후에 고려해도 늦지 않다고 생각해요!