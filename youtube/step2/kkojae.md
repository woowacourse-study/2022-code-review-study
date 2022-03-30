# STEP2. 나만의 유튜브 강의실 정리

---

## 담당 크루

---

- 준찌 [#117](https://github.com/woowacourse/javascript-youtube-classroom/pull/83)
- 빅터 [#128](https://github.com/woowacourse/javascript-youtube-classroom/pull/90)
- 꼬재 [#122](https://github.com/woowacourse/javascript-youtube-classroom/pull/98)
- 우연 [#133](https://github.com/woowacourse/javascript-youtube-classroom/pull/109)
- 유세지 [#140](https://github.com/woowacourse/javascript-youtube-classroom/pull/99)
- 온스타 [#120](https://github.com/woowacourse/javascript-youtube-classroom/pull/87)
- 아놀드 [#154](https://github.com/woowacourse/javascript-youtube-classroom/pull/106)

## 피드백 정리

---

### JS

#### [#122](https://github.com/woowacourse/javascript-youtube-classroom/pull/122#discussion_r831122491) 외부와 강하게 결합되어있을 수록 변경에 취약해 보통 레이어 하나 정도를 두어 유연하게 작성하는게 좋다.

변경 전

```js
const videoStorage = {
  getVideo() {
    return JSON.parse(localStorage.getItem("saveVideoData")) || [];
  },
};
```

좋은 예가 아니다...!!
변경 후

```js
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

네이밍도 동일하다.
Storage라는 이름일 경우 localStorage가 API로 변경되었을 경우를 생각한다면, Videos 라는 이름을 갖는게 좋다!

#### [#133](https://github.com/woowacourse/javascript-youtube-classroom/pull/133#discussion_r831685384) 단방향 / 양방향 바인딩

단방향 데이터 바인딩

- 데이터와 템플릿을 결합하여 화면을 생성한다.
- UI에서 받아온 데이터를 멤버 변수 혹은 도메인에서 상태를 관리하지 않고 곧 바로 render 해준다.
- react는 단방향 데이터 바인딩을 한다.

양방향 데이터 바인딩

- 데이터의 변화를 감지해 템플릿과 결합하여 화면을 갱신하고 화면에서의 입력에 따라 데이터를 갱신한다.
- 즉, 데이터와 화면 사이의 데이터가 계속해서 일치하게 되는 것이다. (UI와 domain의 데이터가 일치)
- Vue는 양방향 데이터 바인딩을 한다.

> [facebook article](https://facebook.github.io/flux/docs/in-depth-overview/)

#### [#133](https://github.com/woowacourse/javascript-youtube-classroom/pull/133#discussion_r831692497) 인스턴스를 여러군데에서 사용할 경우 인스턴스를 생성해서 export 한다.

클래스 맴버 변수로 가지고 있을 이유가 없을 경우 아래와 같이 인스턴스를 export 해주면 필요한 곳에서 사용해 줄 수 있다.

```js
class YoutubeAPI {}
const youtubeAPI = new YoutubeAPI();
export default youtubeAPI;
```

#### [#120](https://github.com/woowacourse/javascript-youtube-classroom/pull/120#issuecomment-1073419431) localStorage 접근 또한 비용이 많이 든다.

localStorage에 불필요하게 접근하고 있는건 아닌지?! 확인해 볼 필요가 있다.

#### [#128](https://github.com/woowacourse/javascript-youtube-classroom/pull/128#discussion_r830633687) 저장된 video들이 많아 질 경우 배열로 관리하는 것이 좋을까?

하나의 video를 고치기 위해 모든 배열을 순회할 필요가 있을까?!

- 배열이 아닌 객체로 변경

#### [#117](https://github.com/woowacourse/javascript-youtube-classroom/pull/117#discussion_r830470065) Promise.all 사용

localStorage에 id값만 저장하고 이후 id 값들로 API 호출을 다시 한다.

```js
const savedVideoIdList =
  localStorageUtil.getArrayData(LOCAL_STORAGE_UTIL_KEYS.SAVED_VIDEO_LIST_KEY) ??
  [];
setState(STATE_STORE_KEY.IS_SAVED_VIDEO_WAITING, true);

const savedVideoList = await Promise.all(
  savedVideoIdList.map((savedVideoId) => this.requestVideoById(savedVideoId))
);

async requestVideoById(id) {
  const videoResult = await youtubeAPIFetcher({
    path: API_PATHS.GET_VIDEO,
    params: {
      id,
      part: 'snippet',
    },
  });

  const {
    items: [videoInfos],
  } = parserVideos(videoResult);

  return Video.create({ ...videoInfos, videoId: id });
}
```
