# 디바운스와 스로틀

scroll, resize, mousemove 등의 이벤트에 등록된 핸들러의 과도한 호출을 막기 위해 디바운스와 스로틀을 사용한다. 



## 디바운스

짧은 시간 간격으로 이벤트가 연속으로 발생하면 일정시간간격에서 마지막으로 호출된 이벤트 핸들러만 실행하도록 한다. 

```js
const debounce = (callback, delay) => {
    let timerId;
    return event => {
        // clearTimeout이 실행되면 타이머가 취소되며, 콜백 함수가 태스크 큐에 올라가지 않도록 한다. 
        if(timerId) clearTimeOut(timerId);
        // setTimeout 함수의 세 번째 인수는 첫 번째 인수로 전달한 콜백함수의 파라미터로 전달된다. 
        timerId = setTimeout(callback, delay, event);
    }
}

window.onresize = debounce(e => /*some code*/, 300)
```

**setTimeout**

```js
setTimeout(func|code[, delay, param1, param2, ...]);
```

인수 

1. func(콜백 함수)
   - 타이머가 만료된 뒤 호출 될 콜백함수이다. 
   - 디바운스를 사용할 때, 이벤트 핸들러 등록시 실행할 함수이다.
2. delay
   - 타이머 만료시간이며 기본 값은 0 이며, 실제 동작시 최소 지연시간은 4ms이다. 
3. param
   - 콜백함수에 전달할 인수를 3번째 이후 인수로 전달할 수 있다.
   - 디바운스를 사용할 때 보통 이벤트 객체를 전달한다. 



**디바운스의 특징**

delay 의 값으로 전달한 시간이 지나기 전에 같은 이벤트가 다시 발생하면 기존 타이머는 취소된다.

기존 타이머가 취소되면  콜백함수가 태스크 큐에 올라가지 않는다. 

즉, 이벤트 발생 후 일정 시간이 지나야 콜백 함수가 실행된다. 

**언제 쓰이는지**

- 검색창에서  input 값에 따라 자동 검색을 할 때 AJAX 요청을 최소로 줄일 때
-  버튼 중복 클릭 방지 시



## 스로틀

디바운스가 짧은 시간 내 여러번 발생한 이벤트에 대해서, 일정시간 간격에서 마지막으로 호출된 이벤트만 실행하도록 한다면, 스로틀은 연속된 이벤트를 그룹화해서 일정시간 간격으로 호출 주기를 만든다. 



```js
const throttle = (callback, delay) => {
    let timerId;
    
    return event => {
        if(timerId) return;
        timerId = setTimeout((event) => {
            callback(event);
            timerId = nulll;
        }, delay, event)
    }
}
```

**스로틀의 특징**

타이머 함수가 호출된 이후 delay 동안은 timeId 가 null이 아닌 유효값으로 존재한다. 

timerId가 유효값이면 throttle의 클로저가 return 되어 타이머함수가 호출되지않는다. 

즉 타이머함수가 최초로 호출된 후 delay동안은 타이머함수의 콜백함수가 한번 밖에 실행되지않는다.



**언제 쓰이는지**

-  스크롤 이벤트 처리
- 무한 스크롤 ui



