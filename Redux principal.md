# Redux principal



## useSelector

리덕스 스토어에서 상태 값을 가져올 수 있다. 

```js
const result = useSelector(selector, equalityFn);
```



**유의점**

- selector 함수는 순수함수이어야한다.
  - 여러번 호출될 수 있고 모호한 시점에 호출될수 있기 때문이다. 

**selector 특징**

- selector 는 **리덕스 스토어의 전체 상태**를 인수로 다룬다. selector 는 함수형 컴포넌트가 렌더링 될때마다 호출될 것이다. 

컴포넌트의 이전 렌더링부터 참조한 값이 변하지 않았더라도 재호출 된다. 그래서 selector 함수 코드를 재실행하지 않고 캐시된 결과값이 useSelector 훅에 의해 반환된다.

- selector 함수는 객체가 아닌 값도 반환할 수 있다. 

- 액션이 디스패치되면, useSelector