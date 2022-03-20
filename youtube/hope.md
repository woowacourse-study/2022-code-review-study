
# 구조 분석

## [#95](https://github.com/woowacourse/javascript-youtube-classroom/pull/95) & [#96](https￼://github.com/woowacourse/javascript-youtube-classroom/pull/96) 콤피 & 호프 

1. Display 에서 구독 할 Store 저장
2. Display 내 이벤트 핸들링 시 필요 시 Store 에 Dispatch 하여 상태 변경
3. Store 에서 Dispatch 호출하여 상태 변경 및 비즈니스 로직 처리
4. Store 에서 상태 변경시, 구독 중인 Display 내 함수 호출



## [#97](https://github.com/woowacourse/javascript-youtube-classroom/pull/97) &  [#78](https://github.com/woowacourse/javascript-youtube-classroom/pull/78) 블링 & 돔하디

![](https://user-images.githubusercontent.com/43166681/157829860-726c03d8-5ace-47eb-83ed-085680d18c13.png)

- 위와 같은 구조였는데 피드백 이후 변경됨 
- 중간의 검색 요청 전달, 저장 요청 전달하는 Sender class 레이어를 제거하여 UI 에서 도메인으로 직접 요청

### View
- 이벤트 바인딩 및 처리를 담당한다.
- 이벤트 처리시, 생성자 인자로 받은 search 와 saveVideos 도메인에 요청을 보내고 값을 리턴받아 현재 화면에 그려준다.


### Domain 1. Search
- 키워드와 nextPageToken 을 멤버변수로 저장한다.
- search 요청을 받으면, api 요청을 보낸 후 , 반환된 nextPageToken 와 사용자가 입력한 keyword 를 저장한다. 
- 반환된 video Items 는 한번 가공 후, 리턴해준다.

```javascript
  #getVideoObjectArray(items, savedVideos) {
    if (items.length === 0) throw new Error(ERROR_MESSAGES.NO_RESULT);
    return items.map((item) => {
      const { snippet, id } = item;
      return {
        videoId: id.videoId,
        thumbnail: snippet.thumbnails.medium.url,
        title: snippet.title,
        channelTitle: snippet.channelTitle,
        publishedAt: snippet.publishedAt,
        isSaved: !!savedVideos.includes(id.videoId),
      };
    });
  }
```

 - 위와 같이 items 를 가공한다. 
 - savedVideo 는 로컬스토리지에서 가져온다. 
 - 가공해줌으로써, 뷰에서 처리하기가 쉬워진다.

### Domain 2. Storage 
- localStorage 에 데이터를 save 하고 get 하는 연산을 한다.


## [#102](https://github.com/woowacourse/javascript-youtube-classroom/pull/102) & [#91](https://github.com/woowacourse/javascript-youtube-classroom/pull/91) 밧드 & 나인


### View
- MainView
	- callback 함수를 인자로 받아서 Modal Open 이벤트를 등록 할 수 있는 메서드가 있음  
- ModalView
	- Modal Close/ Search Button Click 이벤트를 등록 할 수 있는 메서드가 있음  
	-  Scroll 이벤트를 등록 할 수 있는 메서드가 있음  
	- 비디오 저장 이벤트를 등록 할 수 있는 메서드가 있음  
- VideoItemView
	- Modal View 내부에서 실행되어 Template 반환 


### Manager 
- 클래스로 만들지 않고 객체로만들어서 객체 메서드를 선언 
- ApiManager
	- api 요청
	- queryString 생성 
	- 범용적인 메서드
- StorageManager
	- storage 저장/ 유무 확인 
- VideoAPICaller
	- 위의 apiManager 를 사용하여 유튜브 api를 호출
	- 받아온 video Data를 파싱하여 리턴 

### EventHandler
-  위의 MainView 와 ModalView 인스턴스 생성
-  여기서 이벤트 핸들러 정의하여 위의 View 내부 메서드에 연결 
- 따라서, Manager 와 View 를 연결시켜주는 역할 

# 리뷰 정리

## 1. 테스트
- [#97] -  localStorage 는 직접 구현하신게 아닌 브라우저 기능인데요. 굳이 그거까지 테스트를 해야하나 싶습니다.
e2e 테스트였다면 비디오 저장 후 새로고침했을 때 저장된 비디오가 있는지, 그리고 2단계에서는 비디오가 잘 노출되는지를 테스트해봤을 것 같네요 
  - jest 에서 localStorage를 테스트하려면 localStorage 를 mocking 해야하는데, 사실상 개발자가 mocking 한 localStorage를 테스트 하는게 의미가 있는가? -   [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/97#discussion_r825449991)
  - (의견) 테스트 한다면 localStorage 앞 뒤에 붙어있는 데이터 가공 및 요청 함수를 테스트하는게 맞다고 생각한다.

- [#97] - api 가 올바른 형태의 응답값을 리턴하는지 확인하는 테스트에 관하여 - 저였다면 요 테스트는 넘어갔을 것 같아요! 특별한 로직이 있는게 아니라 바이패스인 수준이기 때문에..! (예를 들어 2 * 5 = 10 처럼 2, 5를 넣으면 10이 반환되는 함수인지를 검증하는 로직같은..!)
api 응답값을 잘 가공하는지 보다, 응답값 중 localstorage에 저장되어있는 비디오라면 isSaved 가 true 일테니 localstorage 에 있는 video 갯수와 isSaved 가 true 인 video 의 갯수가 같은지 정도 테스트해볼 수 있을 것 같습니다.  - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/97#discussion_r825450961)


- [#102]  테스트 파일 구조 - 테스트 파일이 분리가 되면 좋겠다는 생각을 했구
__test__ 에 모아둔다면 테스트 파일들을 모으는 것 외에는 별다른 장점이 없다고 생각합니다..!
좋은 코드 중 하나를 비슷한 역할을 하는 코드들을 모아 보기 좋게/ 유지 보수에 좋게 만드는 것이라고 생각하는데요. 테스트 코드도 마찬가지로 __test__ 에 모으는 게 아니라 validator.js 와 동일한 뎁스에 validator.test.js 를 위치시켜면 어떨까 의견을 드려봤습니다 - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/102#discussion_r825956918)

- [#91]  테스트 제목 ‘입력 없이 버튼을 눌렀다면 error를 throw한다.’,버튼을 눌렀다 가 함축한 내용이 많아 보이네요! 검색어가 무엇일 때 버튼을 눌렀고 어떤 Error 가 throw 되는지 구체적으로 적어봤으면 좋겠어요!
그리고 입력버튼을 눌렀다 는 e2e 관점 같은데요, 검색어가 비었을 때 검색하면 ~~~에러가 발생한다 와 같이 기능 관련된 테스트 명으로 수정해볼까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/91#discussion_r825455932)


- [#96] 지극히 개인적인 생각입니다만, isSameKeyword 같이 단순 문자열을 비교하는 함수를 테스트해줘야할까?라는 생각을 합니다..ㅎ
테스트코드도 비용이거든요, e2e 테스트나 통합테스트로 작성한 다음 다른 사이드이펙트 방지를 테스트하는 건 의미가 있을 순 있겠으나 === 만 수행하는 테스트를 해야하나!!!해야할까? 라는 생각으로 의견을 드렸습니다! - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#discussion_r825312522)

- [#96] 말씀주신 것처럼 저도 웹스토리지 자체를 테스트할 필요는 없다고 생각해요. 다만 웹스토리지에서 *가져와서 데이터를 조작하는 비즈니스 로직 부는 테스트해야합니당.* 만약 비즈니스 로직이 웹스토리지가 필요하다면 디펜던시를 줄여서 모킹할 수 있는 구조로 변경을 해야할 것 같습니다. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#￼issuecomment-1066917848)

- [#96] 널리 쓰이는 유틸성 함수같은 경우, 현업에서도 있는 라이브러리를 사용할 때도 있고, 재미상(?) 직접 만들어 쓰는 경우도 (사실 더 많음) 왕왕 있습니다. 그럴 때 비슷한 유틸 라이브러리를 찾아서 테스트 코드를 보면서 엣지 케이스나 고려할 점들을 참고하면서 테스트 코드를 짜곤 합니다ㅎㅎ..
그게 아니라 프로덕트 내에 특정 상황을 다루는 유틸 함수라면 기획이나 엣지 케이스를 직접 만들어서 최대한 범위를 맞춰야겠죠 : 0
테스트 코드는 어디까지나 우리 개발자가 작성하는 거기 때문에 완벽할 순 없고, 개발자 역량과 경험에 따라 그 촘촘함도 달라진다고 생각합니다. 이 부분은 다른 테스트코드를 많이 보면서 경험을 쌓는 것 밖에 없다고 답을 드릴 수 밖에.. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#￼issuecomment-1066917848)
## 2. 함수
- [#97] - 첫 비디오 리스트 요청 함수와 이 후 무한스크롤  시 비디오 요청 함수를 분리한 상황 - 요런 기능을 Pagination 이라고 하죠! 보통은 서버에서 size 와 page 값을 받고 그에 맞는 데이터를 내려주는데요, page 와 size 가 다르다고 해서 함수가 여러개라면 유지보수가 힘들겠죠?
지금 변경된 방향이 적절하다고 보고 잘 고쳐주신 것 같습니다 -[바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/97#￼￼discussion_r825633485)

- [#97]  isEndOfResult 라는 함수명이 맞나요..? 검색결과가 없다라는 것을 나타내야하지 않을까요?
그리고 그 것을 굳이 함수로 빼야하는 이유도 모르겠네요! [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/97#￼￼discussion_r825453334)
```javascript
  if (this.#isEndOfResult(searchResultArray)) return;
``` 


## 3. 로컬스토리지
- [#97] localStorage 는 기본적으로 item 이 존재하지 않을 수 있다는 가정이 있습니다. 그래서 예외처리가 필요한데요!
예를 들어 저장된 아이템이 없는 상태에서 getSavedVideos()[2].videoId 를 하면 런타임에러가 발생해 서비스 장애상황이겠죠?
예외처리를 추가해보면 좋겠네요! - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/97#discussion_r825452143)
```javascript
const savedVideos = storage.getSavedVideos() || {};
``` 


## 4. 네이밍
- [#97] - Intersection Observer 실행 함수명:lastItemOfListObserver -  꼭 lastItemOfList 가 들어가야하나 싶긴해요!
추후에 기능이 바껴서 마지막줄 전체로 observer 를 옮길 수도 있구요! 요점은 어느 누구를 observe 하는것을 나타내는 것이 아니라 intersecting 됐을 때 어떤일을 하는 옵저버인지를 나타내면 좋을 것 같아요! 현재 서비스로 말하자면 비디오를 더 불러오는 옵저버라는 것을 나타내면 좋겠다는 이야기였어요~

- [#102] 보통 이벤트핸들러는 onXXXClick/Change, handleXXXClick/Change
on/handle + {target} + {eventType} 으로 네이밍을 하곤해서 그렇게 수정해보는 건 어떨까요? - [바로가기]( https://github.com/woowacourse/javascript-youtube-classroom/pull/102#discussion_r825408409)

- [#97] Manager 라는건 어떤 데이터를 관리해주는 주체인데요, APIManager 는 데이터를 관리하지 않고 있네요! Manager 로 설정한 이유가 있을까요? - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/91#discussion_r825457653)

- [#97] is~~~()  / check~~ () 는 boolean 값을 리턴예상[바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/91#discussion_r825458109)

- [#80] handleSaveButtonClick -> handleSaveVideos 로 함수명 변경 (이벤트 위임의 의도를 드러내기 위해)  원래의 로직에서 네이밍만 교체하는 것도 좋고요.
마치 이벤트 위임을 위한 DOM 셀렉팅이 함수 네이밍까지 온 느낌인데 이게 의도와 맞으실까용?
나중에 UI가 변경되어 셀렉팅이 바뀌게되면 함수명도 변경해야할까요? [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/80#discussion_r828758902)


## 5. 클래스

- [#91]  AppClass 정의  -[바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/91#￼￼discussion_r825456612)
	1. 한번도 사용하지 않을 this.EventHandler 는 왜 저장하는건가요?
	2. 그리고 멤버변수도 사용하지않고, 멤버함수도 존재하지 않는 App class 는 어떤 이유에서 필요한가요?
	3. 멤버변수의 이름은 소문자로 시작해야 합니다.
```
index.js
HTML 템플릿 및 JavaScript의 컴포넌트를 조합하여 렌더링하고 실제 표시한다.

App.js
컴포넌트를 정의하는 프로그램이다. 실제로 화면에 표시되는 내용 등은 여기에서 정의된다.

```

- [#95] 추상클래스 인스턴스화 막기 -  [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/95#￼discussion_r825238162)
```javascript
if (this.constructor === Display) {
      throw new Error('추상 클래스는 인스턴스화 할 수 없습니다.');
    }

class Display {
  constructor() {
    if (this.constructor === Display) {
      throw new Error('추상 클래스는 인스턴스화 할 수 없습니다.');
    }
  }
}
class D2 extends Display {}
D2.prototype.constructor = Display
const d = new D2() // Error: 추상 클래스는 ...
```
	- 위에서 D2는 추상클래스가 아님에도, constructor 를 덮어 씌움으로써 같은 에러를 만나게 된다.
	- [new.target - JavaScript | MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/new.target) 속성을 이용할 수 있다.
	- new target 속성은 함수 또는 생성자가 new 연산자를 사용하여 호출되었는지 확인한다. 
	- 일반함수 호출에서 new.target 은 undefined 
```javascript
 if (new.target.name === Display.name) {
      throw new Error('추상 클래스는 인스턴스화 할 수 없습니다.');
    }
```




## 6. 그 외 
### 스크롤 이벤트 시 offset 지정 
- [#91] 보통 스크롤 값들로 무한스크롤을 구현할 때는 정확히 하단에 도착했을 때가 아닌 offset 을 주곤 합니다.
infinite scroll not working 뭐 이런 키워드로 찾아보면 offset 추가하라는 말이 많이 나올거에요! 
- `scrollTop + clientHeight >= scrollHeight - ${SCROLL_OFFSET}`
-  [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/91#discussion_r825458566)


### 비동기
- [#91]  먼저, javascript는 동기적 언어예요. 싱글 스레드로 한 번에 하나의 작업을 수행합니다. 하지만 이런 방식은 웹 페이지에 치명적일 것 같아요. 동기적인 방식으로 하면 하나의 동작이 끝날 때까지 다음 동작을 수행하지 못하게 되거든요! (엄청 버벅 거리거나 로딩이 오래 걸리겠죠?) 그래서 비동기를 통해 요청을 끝날 때까지 기다리지 않고 바로 다음 동작이 실행될 수 있도록 해요. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/91#issuecomment-1067049781)


### reduce 로 객체 생성하기  
- [#96] -  [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#discussion_r825314353)
```javascript
const output = Object.entries(origin).reduce(
    (acc, [key, value]) => ({ ...acc,
      [key]: value.substr(1),
    }),
    {},
  );
```


### 셀렉터 상수화
- [#96] 사실 현업에서는 DOM 을 Selector 로 조작할 일은 거의 없다고 봐도 되는데요…
물론 상수를 사용함으로써 변경에 용이하겠지만, HTML 에도 상수를 사용하는 게 아니라면 글쎼.. 개인적으론 가독성이 크게 떨어지는 것 같아서 사용하진 않을 듯 합니다ㅎㅎ.. - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#discussion_r826001310)

- [#95] “자바스크립트 내에서 사용 중인 선택자들을 다른 개발자에게 알려주어, 템플릿 수정 시 주의를 요하고 싶었습니다.” 이 부분은 선택자를 사용하는 코드가 도처에 널려있을 경우에는 의미가 있습니다. 당연히 선택자가 수정될 상황이 생긴다면, html, css, js 모두에 수정이 필요할 것입니다. 그런 때에 js에서 해당 값을 찾아 변경하는 작업이 필요한 것도 맞죠. 그런데 각 선택자가 오직 한군데씩만 쓰이고 있다면, 그 곳을 찾아서 변경하는 것과 상수를 찾아가서 수정하는 것 사이에 얼마나 큰 차이가 있을까요? IDE의 일괄검색/바꾸기 기능을 쓰면 constant를 변경하는 것과 아무런 차이가 없지 않나요? 저는 일반론적인 말씀을 드린 것이 아닙니다. 지금 이 프로젝트에서의 효용성을 논하고 싶었던 거예요.오버엔지니어링이 아닌지 고민해보자는 취지예요.  - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/95#￼discussion_r825233667)




### 상수 파일 분리 
- [#96] 
프로젝트 규모가 작다면 그냥 constants.js 에 모두 정의할 때도 있고, 점점 커져 역할에 나눌 필요가 있다면 분리할 때도 있습니다.
(개인적으론 작을 땐 constants.js 에 정의하고 커짐에 따라 개선하는 걸 선호합니다!  [YAGNI](https://martinfowler.com/bliki/Yagni.html)  원칙을 좋아합니다ㅋㅋ)
그리고 범용성있게 사용되지 않는 상수는 보통 그 상수를 사용하는 파일 내에 정의할 때가 많은 거 같구요! - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#discussion_r826005848)


### addEvent 함수에서 preventDefault 를 매개변수로 받아줘야할까?
```javascript
addEvent({ eventType, selector, handler, isPreventedDefault = false }) {
    const children = [...this.container.querySelectorAll(selector)];
    const isTarget = target => children.includes(target) || target.closest(selector);

    this.container.addEventListener(eventType, event => {
      if (isPreventedDefault) event.preventDefault();
      if (!isTarget(event.target)) return false;
      handler(event);
    });
  }
```

- [#96]  그리고 저는 preventDefault 는 사용하는 핸들러에서 명시적으로 사용하는 게 더 나을 것 같아요! 보통 event.preventDefault 는 핸들러 내부에서 호출하는데, 그 코드가 한 depth 더 위에 있기 때문에 코드 파악하는 데 어려움이 있을 수도 있을 것 같습니다!  - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#￼￼discussion_r826011710)

### 추상화
- [#95]  추상화에 많은 공을 들이신 것 같아요. 개발하면서 즐거우셨을 것 같습니다. 다만 이번과 같은 규모가 작은 프로젝트에서는 이런 추상화/패턴화가 오히려 가독성 / 빠른 개발을 해치는 것은 아닐지에 대해서도 한 번쯤 생각해 보시면 좋겠습니다.
- [#95] 전체 코드를 둘러보니, Display의 구현클래스들도 실상은 인스턴스로서의 가치가 없이 오직 초기화 용도로만 클래스를 활용하고 있더군요. 이런 경우에까지 클래스를 사용하는 것이 맞는지 고민해보시면 좋겠어요.  - [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/95#￼discussion_r825242959)


### Object.freeze
- Object.freeze() 메서드는 객체를 동결합니다. 동결된 객체는 더 이상 변경될 수 없습니다. 즉, 동결된 객체는 새로운 속성을 추가하거나 존재하는 속성을 제거하는 것을 방지하며 존재하는 속성의 불변성, 설정 가능성(configurability), 작성 가능성이 변경되는 것을 방지하고, 존재하는 속성의 값이 변경되는 것도 방지합니다. 또한, 동결 객체는 그 프로토타입이 변경되는것도 방지합니다.
- [#95] 상수에 Object.freeze를 쓰지 않으면 협업시 다른 사람들이 해당 변수의 내용을 바꾸려는 시도를 하게 될까요?
콤피님은 나중에 다른 코드상에서 이 변수의 내용을 바꿀 생각이 들 것 같나요?
-  팀 내 컨벤션이 확고하여 약속을 지키지 않을 가능성이 현저히 적은 경우 -> 굳이 freeze를 할 필요까지는 없을 듯. -  [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/95#discussion_r825232776)


### 예외처리
- [#95] `if (saveItemsCount === CLASS_ROOM_SETTING.MAX_SAVE_NUMBER)` 이런 코드보다  `if (saveItemsCount >= CLASS_ROOM_SETTING.MAX_SAVE_NUMBER)` 이게 더 안전하다. 
- 이유는 ? (나의 생각) 사용자가 버튼을 빠르게 눌러서 두번 저장될 수도 있는데 그러면 101 은 예외사항으로 처리하지 못하기 때문이다. [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/95#discussion_r825242511)


### 일정 시간후 fetch 요청 취소하기 - timeout
- [#96] [바로가기](https://github.com/woowacourse/javascript-youtube-classroom/pull/96#issuecomment-1068810922)
- AbortController 웹 API 를 사용하여 구현 가능
- signal 객체를 fetch option으로 넣어준다. 
- 사용자가 지정한 timeout 시간 이후 응답이 없으면 fetch 요청 취소 

```javascript
const controller = new AbortController();
const { signal } = controller;
const timeout = 8000;

const request = async (uri, options) => {
  const timer = setTimeout(() => controller.abort(), timeout);
  const response = await fetch(uri, { ...options, signal });

  if (!response.ok) throw new Error('서버 오류');
  const data = await response.json();
  clearTimeout(timer);
  return data;
};

```

