# 코드리뷰스터디(코리스)

## 0. 코드리뷰스터디 결과물

|통합정리본|참여자|
|:---:|:---:|
|[자동차게임 미션](https://github.com/woowacourse-study/2022-code-review-study/blob/main/racing-car/marco.md)|마르코|
|[로또 미션 step1](https://github.com/woowacourse-study/2022-code-review-study/blob/main/lotto/step1/lotto-step1-integrated-review.md)|마르코, 호프, 록바, 무비|
|[로또 미션 step2](https://github.com/woowacourse-study/2022-code-review-study/blob/main/lotto/step2/lotto-step2-intergrated-review.md)|마르코, 호프, 록바, 무비, 꼬재|
|[유튜브 미션 step1](https://github.com/woowacourse-study/2022-code-review-study/blob/main/youtube/step1/intergrated-review.md)|마르코, 호프, 록바, 무비, 꼬재|
|[유튜브 미션 step2](https://github.com/woowacourse-study/2022-code-review-study/blob/main/youtube/step2/intergrated-review.md)|마르코, 호프, 록바, 무비, 꼬재|

## 1. 스터디 소개

- 우아한테크코스 4기 미션 코드리뷰를 '리뷰'하는 스터디입니다.
- 주요 활동은 다른 크루들이 받은 좋은 코드리뷰들을 각자 분량을 나누어 정리한 후 함께 공유하는 것입니다.
- 목표는 코드리뷰를 통하여 `메타인지` 및 `클린 코드` 역량 향상입니다.
- 스터디원

|Nickname|Github|
|:---:|:---:|
|마르코|[@wonsss](https://github.com/wonsss)|
|록바|[@lokba](https://github.com/lokba)|
|무비|[@byhhh2](https://github.com/byhhh2)|
|호프|[@moonheekim0118](https://github.com/moonheekim0118)|
|꼬재|[@kkojae91](https://github.com/kkojae91)|

## 2. 일정

- 스터디 운영 기한
  - 2.28. ~ level1 로또 미션까지 한시적으로 진행합니다.
  - 이후 참여의사 확인 및 피드백을 거쳐서 모임을 재시작합니다.
- 스터디 모임(화상회의) 주기
  - step1 리뷰 스터디는 step1 종료한 날(일요일)의 다음 주 금요일 저녁 9시
  - step2 리뷰 스터디는 step2 종료한 날(월요일) 주 금요일 저녁 9시
- 정리한 내용 PR 기한
  - 모임 전날인 목요일 밤 12시까지
- 토론 주제 상정 기한
  - 모임 당일 오후 5시까지

## 3. 주요활동

- 1인당 담당하는 코드리뷰PR은 8개이며, 자신과 페어를 포함하여 페어프로그래밍한 총 네 팀의 코드를 묶어서 맡습니다.

### 3-1. 피드백 정리 후 PR

- 스터디원들은 각자 맡은 코드리뷰PR 피드백들을 정리하고 작성합니다.
  - 크게 `아키텍처 분석`과 `피드백 정리`를 수행합니다. `아키텍처 분석`은 담당한 4개(=페어 4팀)의 소프트웨어의 아키텍처 중 느낌이 오는 코드를 선택하여 간단히 분석하고 설명합니다(부담 갖지 말고 그냥 느끼는 대로 편하게 설명해주시면 될 것 같아요). `피드백 정리`는 리뷰어들이 8개의 PR에 달은 코멘트 중 `Aha` 모멘트가 있던 피드백을 메모하는 작업입니다.
  - 양식은 아래와 같습니다.

```plain
# Level번호 미션명 Step번호(담당 PR 번호들) - 작성자닉네임
- 분석 담당 코드
    - PR 번호(PR 링크)
    - PR 번호(PR 링크)
...

## 아키텍처 분석(desc: 담당한 4개의 소프트웨어의 아키텍처를 간단히 분석하고 설명합니다)
- [해당 PR 번호] 내용
    - 상세 내용

## 피드백 정리
### 대분류(ex: 아키텍처, 함수/클래스, 컨벤션, DOM, 테스트 등)
- [해당 PR 번호] 내용
    - 상세 내용
```

- 정리한 내용을 본 저장소에 `Pull Requests`합니다(기한: 모임 전날 목요일 밤12시).
  - 다음과 같이 폴더링하여 올려주세요. 예시: `lotto/step1/{닉네임}}.md`
  - PR 제목 : `[lotto-step1-lokba] 리뷰 분석 제출합니다.`
- 아키텍처 분석 및 피드백 정리 도중 의문이 든 주제가 있을 경우, `Discussion`에 글을 올립니다(기한: 모임 당일 오후 5시).
  - `Discussion`에 올라온 토론 주제나 질문을 보고 자신의 의견을 미리 생각해보고 토론을 준비합니다.

### 3-2. 발표 및 토론

- `매주 금요일 오후 9시`에 `모임(줌)`을 가지어, 발표 및 토론을 2시간 이내로 진행합니다.
- 발표
  - 각자 정리한 내용을 10분씩 발표합니다.
- 질의응답
  - 1명씩 발표를 마친 후, 질의응답 시간을 5분씩 갖습니다.
- 토론
  - 발표 및 질의응답을 모두 마친 후, `Discussion`에 게시된 주제에 대해 토론을 진행합니다.
  - 토론 후, 각 `Discussion`의 주제에 대해 새로 알게 된 내용이나 정리된 본인의 의견을 `answer`로 남깁니다.

## 4. 운영 관련

- 매주 스터디원들이 번갈아 가며 모임 운영 역할을 맡습니다.
- 운영자 역할
  - 스터디원들에게 담당 코드리뷰PR 분배
  - 스터디원들이 올린 PR 검토 후 merge
  - PR 마감 기한 지연 시, 스터디원에게 일정 준수 요청
  - 발표 및 토론 시 모임 진행
  - 각자 올린 정리 파일을 하나로 편집하여 통합(주말까지)
- 스터디원은 총 4명으로 구성합니다(추후 검토).
- 매주 각자 분석할 코드리뷰PR의 분량은 8개입니다.
  - 나와 나의 페어(2개), 페어 3팀(6개)
  - 4*8=32... 37-32=5... 기본 배정되지 않은 5개의 PR은 스터디원들이 자율적으로 담당합니다.
