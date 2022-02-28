# [마르코 2.25] 자동차게임 미션 리뷰 분석

## 컨벤션

- 반드시 그래야 하는 것은 아니지만, 업계 전반에 class명의 첫글자는 대문자로 표기하는 관행이 있다.
- 예를 들어, `isCarNameBlank`와 같이 `is` 라는 prefix가 붙은 네이밍은 car name이 비어있는지 여부에 따라서 true나 false를 반환한다고 예상된다.

## 클린코드

- 공통 메서드는 의도가 드러난 한 가지 일만 수행하도록 하는 게 유리하다. 한 함수에서 여러 일을 동시에 하지 않도록 분리한다.

## 자료구조를 활용하여 중복 제거

- Set 자료구조를 통해 중복을 제거하고, 원본의 길이와 비교하면 해당 배열에 중복값이 있는지 쉽게 확인할 수 있다.

```jsx
const validateDuplicateCarName = names => {
  return new Set(names).size === names.length; 
```

## innerHTML 이슈

- innerHTML을 사용하면 기존에 매핑된 이벤트가 전부 소멸된다. 매핑된 이벤트를 소멸하지 않고 유지하면서 DOM에 추가하기 위해 insertAdjacentHTML 메서드를 사용해볼 수 있다.
- insertAdjacentHTML 추천

## 단축평가

- or로 이어진 조건들을 some 메서드로 리팩토링

```jsx
// before
static isInvalidInput(input) {
	if(this.isA(input)) {
		return true;
	}
	if(this.isB(input)) {
		return true;
	}
	if(this.isC(input)) {
		return true;
	}
	return false
}
```

```jsx
// refactor 1 - 단순화
return (this.isA(input) || this.isB(input) || this.isC(input));
```

```jsx
// refactor 2 - or로 이어진 조건들을 some메서드로 리팩토링
return ['isA', 'isB', 'isC'].some(method => this[method](input));
```

- and로 이어진 조건들을 every 메서드로 리팩토링

```jsx
// before
static isInvalidInput(input) {
	if(this.isA(input) && this.isB(input) && this.isC(input)) {
		return true;
	}
	return false
}
```

```jsx
// refactor 1 - 단순화
return (this.isA(input) && this.isB(input) && this.isC(input));
```

```jsx
// refactor 2 - and로 이어진 조건들을 some메서드로 리팩토링
return ['isA', 'isB', 'isC'].every(method => this[method](input));
```

## 테스트코드

- 본질적인 방어는 본진에서 해두고, 테스트는 ‘예방차원'에서 안전장치를 마련하는 것이다.
- 테스트코드에서도 자주 쓰이는 string은 상수로 관리하면 휴먼에러를 방지할 수 있다.
- 전역에서 사용하는 `custom commands` 의 경우 문서에서 `support` 폴더에 정의하길 권장한다. `support/commands.js`  파일에 정의하면 좋다.
- {enter} 하면 enter키가 눌리는 테스트코드(cypress)

```jsx
cy.get('.input-section').type('east,west,south,north{enter}');
// .input-section 클래스 엘리먼트에 'east,west,south,north'를 입력하고 enter를 누른다.
```

## 설계

- model과 controller를 분리했음에도  controller가 model에 직접 개입하는 것은 바람직하지 않다. model에 값을 던져주고, model이 해당 동작을 수행하게끔 하는 편이 좋다. 그렇지 않으면, 역할 개입에 의해 예상치 못한 문제가 발생하기도 하기 때문이다. 이 때문에 이런 사례가 발생하지 않게 하기 위해 미리부터 model을 캡슐화하는 것도 좋은 전략이다.
- model의 상태를 다른 곳에서 변경하기 보다는 해당 model에게 메세지를 보내서(model의 메서드 호출) model이 직접 스스로 자신의 상태를 변경하는 방식(model의 메서드 내부에 상태 변경 코드)을 추천한다. 상태를 외부에서 직접 변경하는 방식보다는 객체가 스스로 상태를 책임지는 방식이 캡슐화가 더 잘됐다고 할 수 있고, 코드의 의도를 파악하기도 더 좋다.
- 헐리우드 원칙 [https://johngrib.github.io/wiki/hollywood-principle/](https://johngrib.github.io/wiki/hollywood-principle/)
    - DOM 요소가 모두 View에 있도록 하기 위해  DOM에서 이벤트 부착을 하였고, 이벤트 발동 시 실행할 콜백함수를 인자로 받는다. 해당 인자는 Controller에서 전달한 것이므로, 이벤트의 콜백함수는 Controller에서 실행된다. 이처럼 함수를 인자로 넘기는 것은 자바스크립트는 함수가 일급객체이기 때문에 가능한 것이며, 이는 할리우드 원칙 패턴과 유사하다.
    
    헐리우드 원칙을 활용하면 "의존성 부패(dependency rot)"를 방지할 수 있습니다. 어떤 고수준 구성요소가 저수준 구성요소에 의존하고, 그 저수준 구성요소는 다시 고수준 구성요소에 의존하고, 그 고수준 구성요소는 다시 또 다른 구성요소에 의존하고, 그 다른 구성요소는 또 저수준 구성요소에 의존하는 것과 같은 식으로 의존성이 복잡하게 꼬여있는 것을 의존성 부패라고 부릅니다. 이렇게 의존성이 부패되면 시스템이 어떤 식으로 디자인된 것인지 거의 아무도 알아볼 수 없게 되죠.
    
    헐리우드 원칙을 사용하면, 저수준 구성요소에서 시스템에 접속을 할 수는 있지만, 언제 어떤 식으로 그 구성요소들을 사용할지는 고수준 구성요소에서 결정하게 됩니다. 즉, 고수준 구성요소에서 저수준 구성요소에게 "먼저 연락하지 마세요. 제가 먼저 연락 드리겠습니다"라고 얘기를 하는 것과 같죠.
    
- controller가 다른 객체인 view의 멤버변수(element)에 직접 접근해서 eventListener를 달으는 것이 올바를까?
    - 객체 지향 프로그래밍에서 객체의 데이터는 객체의 외부에서 직접적으로 접근하는 것을 막는다. 객체의 데이터를 외부에서 마음대로 읽고 변경할 경우 객체의 무결성이 깨질 수 있기 때문이다 . 객체의 프로퍼티를 객체 바깥에서 직접 조작하는 행위는 데이터의 유지 보수성을 해치는 주요 원인이다.
    - 따라서 view의 멤버변수에 접근하여 eventListener를 다는 메서드는 view 내부에서 해주는 것이 적절하다고 보인다. 또한, 다른 객체의 멤버변수에 접근해야할 경우, getter나 setter 등을 사용하는 것이 적절하다.

## 바로 return하는 함수 관련

- 바로 return을 하는 함수라면 () 안에 return을 내포하고 있어 다음과 같이 리팩토링이 가능하다.

```jsx
// before
const func = input => {
	return input.split(',').map(input => input.trim())
};
```

```jsx
// refactor
const func = input => (input.split(',').map(input => input.trim()))
```

## DOM

- selector 탐색을 root에서부터 시작하는 경우가 많은데, 이럴 경우 탐색하면서 매칭되는 첫 번째 element를 반환한다. 이런 방식으로는 프로젝트 덩치가 커질수록 다음과 같은 두 가지 이슈가 예상된다.
    1. 모든 element를 root에서부터 탐색하기 때무에 element 깊이가 깊어질수록 탐색 속도 저하
    2. 유일한 className이 아니면 의도하지 않은 element 반환
    
    이러한 이슈를 방지하기 위해, document root부터 탐색이 아니라 `특정 element` 부터 탐색하는 방법을 고민해보자. 
    
    ```jsx
    element = baseElement.querySelector(selector);
    ```
    

## forEach를 map으로 리팩토링

- DOM에 렌더링을 하기 위한 HTML String을 준비할 때, 데이터 배열을 돌면서 HTML string을 만드는 경우 forEach를 쓰기도 하는데, forEach 대신 map 메서드를 쓸 수 있다.

```jsx
// before : forEach메서드 사용
renderSomething(things) {
    let template = '';

    things.forEach((thing) => {
      template += somethingTemplate(thing.data);
    });

    this.$targetElement.insertAdjacentHTML('beforeend', template);
}
```

```jsx
// after: map으로 리팩토링
renderSomething(things) {
    const template = things
      .map((thing) => somethingTemplate(thing.data))
      .join('');
    this.$targetElement.insertAdjacentHTML('beforeend', template);
}
```

## class

- 객체를 생성할 때, class와 생성자 함수 중 어떤 것을 사용하는 것이 더 좋을까? class는 결국 syntatic sugar이긴 하다. 팀 내에서는 섞어서 쓰고 있다. 요즘에는 class가 `private` 이나 `상속` 등 사용 측면에서 유리한(편한) 면이 더 있다고 생각해서, 팀내에서 class를 더 선호하기도 하다.

## 파일 분리

- 파일을 분리하되 응집력있게 모아두면 괜찮다. 함수가 쪼개지고 파일까지 쪼개지다 보면 오히려 역할이나 배경, 의도를 파악하기가 어려울 때가 종종 있다. (파일도, 함수도, 모두 마찬가지이다.)

## 유효성 검사

- 데이터에 대한 유효성 검증방법은 데이터 관련 `Model` 에서 제공하고, 검증은 `Controller` 에서 하는 방법도 있을 것 같다.

## 고민

- **개발이란 게 정답이 없다.** 교육기간에 너무 베스트 프랙티스나 정답을 따라서 하기보다는, 이것저것 삽질하면서 베스트 프랙티스로 향하거나 본인만의 답을 찾는 게 가장 중요하다.  고민하는 근력이야말로 현업에서 가장 중요하다.

## sort 메서드와 immutable

- 복사본 없이 sort 메서드로 모델의 데이터를 직접적으로 정렬시키는 것은 주의해야 한다. immutable하게 모델을 유지하는 것을 추천한다. 프로그램의 복잡도가 올라갈수록 `직접적으로 객체를 변경하는 것`은  의도치 않은 사이드이펙트를 가져올 수 있기 때문이다.

```jsx
// before
function sortCars(cars) {
	return cars.sort((a,b) => b.position - a.position);
}
```

```jsx
// after - 복사본 만들어서 sort
function sortCars(cars) {
	const carsCopy = [...cars];
	return carsCopy.sort((a,b) => b.position - a.position);
}
```

## 올바른 변수 이름 짓는 법

[https://youtu.be/ZtkIwGZZAq8](https://youtu.be/ZtkIwGZZAq8)

- 간결하면서도 의미가 전달되는 것이 중요하다.
- 변수 이름에는 동사를 넣지 않는다.
- 단수형, 복수형을 구분한다. 변수의 단수형에는 관사(a, the)를 넣지 않는다. 복수형은 ‘s’를 뒤에 붙인다(또는 list로 하는 경우도 있다).
- 전치사는 최대한 생략한다. `nubmerOfUsers` 같은 변수명보다는 더 간결히 `userCount`가 더 낫다. 또는 `number`를 앞에 쓰고 싶다 한다면, 약간 문법 파괴를 하더라도 `numUsers` 같이 짧게 쓰는 경우도 있다. 아무튼 `numberOfUsers` 처럼 전치사를 넣는 네이밍은 피하는 것이 좋다.
- 좋은 오픈소스의 코드들을 보면서 변수명이 어떻게 지어졌는지 보고 익혀보자.

## 명령형과 선언형

- 명령형(imperative, HOW) : “어떻게 구현하는가"를 설명한다. 어떤 방법으로 해야 하는지를 나타낸다. 어떤 동작을 할 것인지, 수행할 명령을 순서대로 써놓은 것.
- 선언형(declartive, WHAT) : “무엇"이 일어나는지, 무엇과 같은지를 설명한다.

## 상수화

- 어디까지 상수화하는 것이 좋은지에 대해 여러 사람들의 생각을 묻고 고민해보자. 상수화하면 어떤 이점이 있을까?
- 상수화 시, ID의 `#`, class의 `.` 를 이런 함수를 만들어서 편하게 사용할 수도 있다(전역 프로토타입 객체를 건드리는 것은 권장하는 방법까진 아니나, 이 정도 미션에서는 가능할 수도 있 다.).

```jsx
const DOM = {
	CAR_NAME_INPUT = 'car_name_input',
}

String.prototype.toID = function() {
	return `#${this}`
}

console.log(DOM.CAR_NAME_INPUT.todID()); // #car_name_input
```

  

## prettier 환경 설정

- trailingComma의 값에 ‘all’ 설정을 하게 되면, 코드리뷰 시 실제로 코드가 변경된 부분만 표시가 될 수 있으므로, 코드리뷰할 때 편하다는 장점이 있다.

## 은닉화

- class의 속성이나 메서드에 해쉬 # prefix를 추가하여 private class fileds를 선언할 수 있다. 다른 클래스에게 노출하지 않아야 하는 것임을 알 수 있게 한다.

## jsDoc

- **JSDoc을 사용하여 자바스크립트에 타입 힌트 제공하기**
- [https://velog.io/@yijaee/JSDoc을-사용해-JavaScript-파일-문서화하기](https://velog.io/@yijaee/JSDoc%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%B4-JavaScript-%ED%8C%8C%EC%9D%BC-%EB%AC%B8%EC%84%9C%ED%99%94%ED%95%98%EA%B8%B0)
- [https://jsdoc.app/](https://jsdoc.app/)

## class

- static 메서드만 존재하는 class가 class로서 의미가 있을까? 객체로 만들어도 충분할 수 있다. class는 어디까지나 “인스턴스를 생성하기 위한 틀 또는 추상적 객체"라는 원래의 목적에 맞게 쓰는 것이 좋다. 왜냐하면, static 멤버로만 구성된 클래스는 실제로 new 연산자와 함께 호출하여 인스턴스를 생성할 일이 없으니, ‘공통 속성을 뽑아낸 추상적 범주'라기보다는 그 자체로 특정 역할을 부여한 “구체적 객체"와 다름없다고 볼 수 있다.

## 유틸 함수

- 범용성이 있는 함수는 유틸 폴더로 옮기는 것이 낫다. 유틸에는 보통 도메인에 대한 정보가 없다.

## 드모르간 법칙으로 if문 없애기

- 조건 충족시 false, 불충족시 true를 반환하는 함수라면 이런 식으로 if문을 없앨 수 있다.

```jsx
// (1)
if(!conditionA || conditionB) return false
return true

// (2)
if (conditionA && !conditionB) return true
return false

// (3) 
return (conditionA && !conditionB)
```

## 입력된 값들에 빈 값이 있는지 체크

```jsx
hasSpaceInName = (names) => {
 names.some((name) => Array.from(anme).some((ch) => ch.match(/ /)));
}
```

## 시맨틱 태그

- 웹접근성을 위해 div 대신에 section이나 article을 사용하여 시맨틱하게 구현도 고려하기
    - section 안에 타이틀 태그를 넣어서 스크린리더기가 해당 섹션의 설명을 읽을 수 있도록 도와줄 수 있다. 타이틀 태그를 화면에 표시하고 싶지 않다면 hidden 어트리뷰트를 사용한다.
    

## 기타

- js 파일을 마지막에 배치한 이유
    - html을 읽고 DOM 트리가 모두 생성된 후에 js가 실행되어야 오류가 발생하지 않는다.
    

## null, “”(empty), undefined

- null, “”(empty), undefined는 구분된다.
- `null`
    - object타입
    - 변수의 선언과 데이터가 없는 상태로서 메모리의 변수의 위치가 정해지고 공간은 텅 빈 상태
    - null은 원시값(Primitive Type) 중 하나로, 어떤 값이 의도적으로 비어있음을 표현한다. undefined는 값이 지정되지 않은 경우를 의미하지만, null의 경우에는 해당 변수가 어떤 객체도 가리키고 있지 않다는 것을 의미
- `""` (empty)
    - string 타입
    - 메모리의 변수의 위치가 정해지고 공간에 빈 문자열 하나만 있는 상태
- `undefined`
    - undefined 타입
    - 선언한 후에 값을 할당하지 않은 변수나 값이 주어지지 않은 인수에 자동으로 할당됨
