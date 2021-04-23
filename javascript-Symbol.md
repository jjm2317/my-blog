# Symbol

## Symbol 이란?

Symbol은 데이터 타입이다. 

ES6에서 **Symbol 데이터 타입**이 새로 추가되어 자바스크립트의 데이터 타입은 다음과 같은 7가지가 되었다. 

- String
- Number
- Boolean
- undefined
- null
- object
- Symbol

**Symbol의 특징**

- 원시값으로 불변 값이다. 
- 다른값과 중복되지 않는 유일한 값이다.

**사용하는 이유**

이름 충돌위험이 없는 유일한 **프로퍼티 키**를 만들기 위해 사용한다. 



## Symbol 값의 생성

Symbol 값을 생성하는 방법은 다음과 같다

- Symbol 함수
- Symbol.for 
- Symbol.keyFor

### Symbol 함수

Symbol값은 다른 원시값들과 다르게 Symbol 함수를 호출하여야만 생성할 수 있다. 

```js
//생성된 심벌 값은 외부에서 확인 할 수 없다.
const mySymbol = Symbol();

console.log(mySymbol); // Symbol()

//심벌 함수는 생성자가 아니다. 
new Symbol(); // TypeError 
```

**Symbol 함수의 인수**

Symbol 함수는 문자열을 인수로 받을 수 있다. 단 해당 값은 description의 용도로만 사용된다. 

즉, 같은 description 을 전달한 심벌 값 두개는 다른 값이다. 

```js
const firstSymbol = Symbol("first");
const secondSymbol = Symbol("second");

firstSymbol === secondSymbol // false
```

**래퍼 객체 생성**

심벌값은 원시값이지만 Symbol.prototype의 프로퍼티를 사용하여 접근하면 래퍼객체를 생성한다.

(참고) 래퍼 객체를 생성하는 원시값

- String
- Number
- Boolean

Symbol.prototype의 프로퍼티 description, toString()

```js
const mySymbol = Symbol("hello");

mySymbol.description // hello
mySymbol.toString(); //mySymbol(hello)
```

**Symbol 값의 타입변환**

- 문자열이나 숫자값으로 암묵적 타입변환이 일어나지 않는다
- 불리언 값은 암묵적 타입변환이 일어난다. 



### Symbol.for / Symbol.keyFor

**Symbol.for 과 Symbol 함수와의 차이점** 

심벌값들이 관리되는 전역 심벌 레지스트리가 있다. 

위의 Symbol함수를 통한 심벌값의 경우에는 전역 심벌 레지스트리에 저장되지 않을뿐더러, 심벌 레지스트리에서 해당 심벌값을 찾을 수 있는 키를 지정할 수 있는 방법도 없다. 

이에 반해 Symbol.for 함수는 **인수로 키를 전달**하여 생성된 **심벌 값을 전역 심벌 레지스트리에 저장하거나 조회**한다. 키에 해당하는 값이 없으면 심벌값을 생성하고, 존재하면 조회한다. 

- Symbol.for 함수의 인수로 전달한 키에 해당하는 심벌값이 심벌 레지스트리에 없는 경우

```js
const mySymbol = Symbol.for("hello"); // 심벌 레지스트리에 hello를 키로 가진 심벌값이 없으므로 심벌 값을 생성한다.
const mySymbol2 = Symbol.for("hello"); //심벌레지스트리에 hello를 키로 가진 심벌 값이 위에서 생성되어 존재하므로 해당 값을 반환한다.
mySymbol === mySymbol2 // true

```

**Symbol.keyFor**

심벌값을 저장한 변수를 인수로 전달하면 해당 심벌값의 키를 반환한다. 

```js
const mySymbol = Symbol.for("hello"); //hello를 키로하여 심볼값 생성
Symbol.keyFor(mySymbol) //hello,  mySymbol 변수에 저장된 심벌값의 키를 반환
```












