# 디바운스와 스로틀

scroll, resize, mousemove 등의 이벤트에 등록된 핸들러의 과도한 호출을 막기 위해 디바운스와 스로틀을 사용한다. 



## 디바운스

짧은 시간 간격으로 이벤트가 연속으로 발생하면 일정시간간격에서 마지막으로 호출된 이벤트 핸들러만 실행하도록 한다. 

```js
const debounce = (callback, delay) => {
    let timerId;
    return event => {
        if(timerId) clearTimeOut(timerId);
        // setTimeout 함수의 세 번째 인수는 첫 번째 인수로 전달한 콜백함수의 파라미터로 전달된다. ㄷ
        timerId = setTimeout(callback, delay, event);
    }
}
```

