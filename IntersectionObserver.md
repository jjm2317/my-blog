# IntersectionObserver

new 연산자와 함께 생성자 함수로 호출 시 IntersectionObserver 객체를 생성한다. 

**인수(Arguments)**

1. callback: 콜백함수를 인수로 받는다. 화면의 부분 백분율이 역치 이상일때 호출된다...?

   -  콜백함수의 인수목록은 다음과 같다.

   1. entries
      - 통과한 역치를 나타내는 IntersectionObserverEntry 배열

   2. observer
      - 자신을 호출한 IntersectionObserver

2. options: observer를 조정할 수 있는 옵션 객체 

   - 옵션 객체의 프로퍼티는 다음과 같다. 

   1. root
      - 지정한 요소의 경계사각형이 뷰포트로 간주된다.
      - 미지정시 문서의 뷰포트가 root가 된다. 
   2. rootMargin
      - 루트 사각형의 크기를 줄일 때 사용된다.
   3. threshold
      - 대상요소가 '보여짐'을 결정하는 가시 비율
      - 0. 5로 지정 시, 요소의 0.5 이상이 드러나면 임계값 이상이라고 할 수 있다.



## 더 들여다보기

[MDN Intersection Observer원문](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API#the_root_element_and_root_margin) 참고

Intersection Observer API 는 **대상 요소와 조상 요소의 교차정보를 비동기적으로 탐색**할 수 있다. 

### **Intersection Observer API는 왜 사용할까**

**Intersection 탐색에 대한 요구사항**

이전에는, 하나의 대상요소의 가시성이나, 두 요소간의 상대적인 가시성을 탐지하는 것은 어려운 작업이었다. 이 작업을 해결하더라도, 해결책이 신뢰하기가 어려울 뿐더러 브라우저와 사이트의 성능을 떨어뜨리기 일수였다.

웹이 발전함에 따라 이러한 탐지에 대한 요구사항이 늘어갔다.

교차점에 대한 정보가 필요한 이유는 여러가지이다.

- 페이지가 스크롤됨에 따라 이미지 등의 정보를 lazy loading
  - lazy loading은 페이지 로드 시점에 유저에게 필요하지 않은 컨텐츠의 로딩 시점을 뒤로 미루는 것이다. 
- 무한 스크롤 웹사이트 구현
- 광고가 보여지는 횟수를 기록. 광고 수익을 계산하기 위함이다. 
- 애니메이션등의 작업을 사용자가 보는 시점에 수행



**별도의 API 없이 구현하려면**

Intersection detection을 구현하는 것은 **이벤트 핸들러**와 Element.getBoundingClientRect() 등의 **메서드의 반복적인 호출**을수반하였다.  

메인쓰레드 해당 로직이 실행되고 있기 때문에 성능 문제를 일으킬 수 있다. 

무한 스크롤을 사용하는 웹페이지의 경우, 페이지에 일정기간동안 등록되는 광고를 관리하기 위해 vendor provided library 를 사용한다. 

또한 애니메이션을 이곳저곳에서 사용하고, 박스요소들이 나타날때 별도의 효과를 구현하기 위해 커스텀 라이브러리를 사용하기도 한다.  

즉, 별도의 라이브러리나 커스텀 라이브러리를 사용한다. 

그러한 효과는 메인스레드에서 계속 실행되는 **Intersection dectection**을 통해 구현된다.  그런 라이브러리 내부적으로는 스크롤될때마다 내부 로직이 실행되고 이는 사용자경험을 떨어뜨리고 브라우저, 웹사이트, 컴퓨터에 부담을 준다. 



**Intersection Observer API 의 사용**

Intersection Observer API는 탐지하고자 하는 요소가 나타나거나 사라질때, 혹은 두 요소가 교차되는 범위가 변할때마다 호출되는 함수를 등록될 수 있게 한다. 

Intersection Observer 를 사용함으로서 이러한 **교차 탐지를 위해 별도의 다른작업을 하지 않아도 된다.** 

또한 별도의 **최적화 작업으로부터 자유롭다**는 장점이 있다. 

**유의점**

- Intersection Observer 는 요소의 교차범위에 대한 정확한 크기(px)는 알 수 없다. 
- 단, 교차범위의 임계 비율을 탐지할 수 있다. 한 요소가 몇 % 이상 교차된다면, 특정 작업을 실행할 수 있다. 



### Intersection Observer의 개념과 사용

Intersection Observer API를 사용함으로서, 우리는 **다음과 같은 상황**이 발생할 때 실행되는 **콜백함수를 등록**할 수 있다. 

- 대상요소가 **기기의 뷰포트나 특정 요소**에 교차된다. 
  - 교차되는 요소를 **root element**나 **root**라고 부른다. 
- 소스코드가 실행되면서, 맨 처음 observer가 대상요소를 관찰하도록 요청받는다. 

**root element의 바인딩**

일반적으로, 우리는 다음과 같이 교차 탐지를 하고 싶을 것이다. 

- root가 대상 요소의 가장 가까운 조상요소인지를 고려하여 대상여부와 root element의 교차여부를 탐지한다. 
- 대상 요소가 root의 후손또는 자식 요소인지 아닌지를 고려하여 교차여부를 탐지한다. 
  - 대상요소가 후손요소가 아닌 root요소의 예를들면 뷰포트가 있다. 

root 프로퍼티에 null 대신 특정 dom을 바인딩함으로서, 대상 요소와 root 요소의 교차탐지를 할 수 있다.  null인 경우 뷰포트가 바인딩 된다. 

**root element 바인딩에 따른 API의 동작**

root에 바인딩 하는 값이 다른 요소이든 뷰포트이든 관계없이, API는 같은방식으로 동작한다. 

대상요소와 root요소가 겹치는 부분이 임계점을 넘나들때마다 정의한 콜백함수가 호출된다. 

대상요소와 root 요소가 겹치는 정도를 intersection ratio라고 한다. 대상요소가 보이는 비율을 기준으로, 0.0 부터 1.0 사이의 값으로 표현된다. 



#### Intersection Observer의 생성

**생성자 함수를 호출**하고, **콜백함수를 전달**함으로서 Intersection Observer를 생성할 수 있다. 

```js
const observer = new IntersectionObserver(() => {
    /* do something, runned whenever intersection changes */
}, {
    /* options for running callback*/
})
```

참고로, 교차 비율에 대한 옵션 프로퍼티로 threshold를 사용한다. 



**Intersection Observer Options**

생성자함수의 두번째 인자로 전달되는 options 객체를 알아보자.

options 객체의 프로퍼티

- root
  - 대상 요소의 가시성을 체크할 뷰포트 역할을 하는 요소이다. 
- rootMargin
  - root 요소의 뷰포트 계산시 면적을 확장하거나 축소할 수 있는 값이다. 
  - 값은 CSS 의 margin property 와 유사하다. "top right bottom left" 순으로 값을 할당할 수 있다. 
  - 기본값은 0이다. 
- threshold
  - 콜백함수가 동작하는 대상요소의 교차 비율을 가리킨다. 
  - number 값이나, array of numbers 값이 들어간다. 
    - 단일 number 값으로 0.5 를 할당하면 root 요소와 대상요소의 교차범위 50% 기준으로 콜백함수가 호출된다. 
    - 배열 값으로 [0, 0.25, 0.5, 0.75, 1] 를 할당하면, 교차범위가 0, 25%, 50%, 75%, 100% 기준으로 콜백함수가 호출된다. 
  - 기본값은 0이다. 



#### 탐지될 대상요소 등록하기

IntersectionObserver 인스턴스를 생성완료했다면 탐지해야될 대상요소를 등록할 수 있다. 

IntersectionObserver.prototype.observe 메서드를 사용한다.

```js
const target = document.querySelector("#target");
observer.observe(target);
```

 

#### 콜백함수의 파라미터

```js
const observer = new IntersectionObserver((entires, observer) => {
    // Each entry describes an intersection change for one observed
    // target element:
    //   entry.boundingClientRect
    //   entry.intersectionRatio
    //   entry.intersectionRect
    //   entry.isIntersecting
    //   entry.rootBounds
    //   entry.target
    //   entry.time
}, {
    /* options for running callback*/
})
```

IntersectionObserver 인스턴스 생성시 첫번째 인수로 정의하는 콜백함수는, 다음과 같은 파라미터를 갖는다. 

1. IntersectionObserverEntry의 배열
2. observer 인스턴스 



**IntersectionObserverEntry**

탐지된 대상요소의 교차 정보를 나타내는 객체이며 다음과 같은 값들을 프로퍼티로 갖는다. 

- boundingClientRect
- intersectionRatio
- intersectionRect
- isIntersecting
- rootBounds
- target
- time



