
## 분석 담당 코드 
- [시지프](https://github.com/woowacourse/javascript-lotto/pull/151)
- [호프](https://github.com/woowacourse/javascript-lotto/pull/154)
- [병민](https://github.com/woowacourse/javascript-lotto/pull/133)
- [티거](https://github.com/woowacourse/javascript-lotto/pull/150)
- [우연](https://github.com/woowacourse/javascript-lotto/pull/149)
- [소피아](https://github.com/woowacourse/javascript-lotto/pull/139)
- [해리](https://github.com/woowacourse/javascript-lotto/pull/130)

### 함수
- [#130] Math.floor는 소수점 내림, parseInt는 소수점을 버림 / 따라서 양수에는 동일하게 작용하나 음수에는 다르게 작용 
    - parseInt는 정확하게 string 형을 number로 만들어준다. 
    - string -> number 변환시 박싱 / 언박싱이 일어난다.
    - 즉, 메모리 계층에서 기존 메모리 타입인 string을 갖고 있다가, number 형태의 메모리 블록을 만들고 그 후 string을 number로 옮기며 메모리의 비효율이 일어난다. 
    - Math.floor는 타입캐스팅하지 않고 오롯이 계산만 하기 때문에 박싱 / 언박싱에서 자유로움

### 네이밍
```javascript
intersection(arrayA, arrayB)
```
- [#150] 집합 개념을 사용하고 있을 때에는 1,2 보다 A, B가 들어간 이름이 더 적합하다.


### 이벤트 관련
- [#151] input은 값이 들어온 직후 발생되는 이벤트 / keyup은 keyboard에서 떨어질때 발생하는 이벤트


### 반복문
- [#110] 정해진 횟수만큼 반복할 때는 for문, 그러지 않을때에는 while문 사용


### 테스트
- [#110] 변경으로 인해 사이드 이펙트가 없고 해당 로직이 변경되지 않는다는 확신이 있다면 테스트 할 필요성이 떨어진다.
- [#149] 테스트를 위한 분리는 지양하기. 하지만 함수가 하나의 행동만 해야된다는 원칙에 위배된다면 분리해는게 좋은 방향성이라고 생각하는데 단일 행동을 하게 작성하다보면 테스트하기 좋은 구조가 나온다.
```javascript
import * as Utils from '../utils/util';

    jest
      .spyOn(Utils, 'generateRandomInRange')
// ReturnValue chaining하여 정의

```
- [#149] jest의 spyOn 메서드를 통해 특정 object의 key에 해당하는 함수를 mocking하기
- [#150] [단위테스트vs인수테스트vs통합테스트](https://tecoble.techcourse.co.kr/post/2021-05-25-unit-test-vs-integration-test-vs-acceptance-test/), 하나의 테스트를 위해 여러가지 함수를 불러와서 실행시킨 후, 결과를 테스트한다면 단위테스트가 아니다.



### DOM 관련

#### 자식노드 초기화 방법 [#110]
- 부모에서 firstChild가 없을 때 까지 자식노드를 삭제한다 (자식 엘리먼트에서 remove를 사용하거나 부모 엘리먼트에서 removeChild를 활용) => 반복적으로 호출이 필요한 부분이지만 브라우저와 하위 노드 수에 따라 차이가 있겠지만 하위 노드의 수가 1000개를 기준으로 벤치마크에서 innerHTML보다 좋은 성능
- textContent나 innerHTML을 “”로 변경
추가적으로 엘리먼트를 createElement로 생성했다면 replaceChildren으로 생성한 노드로 교체할 수 있다.
- [#130] aria label은 해당 section이 어떤 것을 의미하는지 알려주고 웹에 더 정확한 정보를 전달하기 위해 사용한다.

### CSS
- [#130] position 속성이 지정된 엘리먼트보다 position 속성이 지정되지 않은 엘리먼트가 먼저 렌더링 되어서 따로 z-index 설정을 할 필요가 없다.
    - 따라서 position이 absolute, fixed, sticky 등은 position이 지정되지 않은 엘리먼트보다 후에 렌더링한다.
    - 쌓임맥락으로 인해 z index 설정이 필요 없고 쌓임맥락 기준으로 그래픽 레이어가 렌더링되기 때문
    - 기본적인 position 설정을 안해주면 static이 되는데, static은 쌓임 맥락이 없으므로 가장 바닥에 쌓이고, 쌓임 맥락은 그 위로 쌓이기 때문에 그래픽 레이어 생성시 앞서 보이기 때문이다. [관련 링크](https://developer.mozilla.org/ko/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context)
