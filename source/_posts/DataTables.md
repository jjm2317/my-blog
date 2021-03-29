---
title: DataTables
date: 2020-12-04 19:10:39
tags:
---

# DataTables

- SKB 업무를 위한 라이브러리 공부
- DataTable 라이브러리 공식문서 참조
  - https://datatables.net/manual

DataTables 는 자바스크립트 라이브러리이며 다음 효과를 제공

- HTML Table에 상호작용 기능을 제공
- 단순성



## Requirements



### Dependencies

DataTables 는 하나의 라이브러리 의존성을 가진다

- jQuery

jQuery 플러그인으로, DataTables 는 jQuery가 제공하는 많은 기능들을 사용한다.

다른 모든 jQuery 플러그인들과 같은 방식으로 jQuery 플러그인 시스템의 훅들을 사용한다.  

jQuery 1.7버전 이상에서 작동한다.



### HTML

데이터 테이블이 html 테이블을 향상 시키려면 테이블이 유효해야한다.

- thead
- tbody
- tfoot은 옵션

서버사이드 프로그램을 사용하는 html 문서를 생성한다면, 그 모든 프로그램들이 해야할것은 유효한 테이블을 출력하는 것이다. 

Ajax 데이터를 사용하면 데이터 테이블의 모든 셀요소들과 함께 , thead 와 tbody도 생성가능하다 . 

하지만 단지 plain한 html에만 적용된다 .

테이블의 헤더에서만 colspan 과 rowspan 이 적용



**설치**

- cdn
- locally
- package manage ex)npm



## DataTables 시작

- html이 로드되는 이벤트 핸들러 등록
- 테이블 요소를 jQuery로 잡고 DataTable 메서드 호출

**데이터 테이블이 제공하는 기능**

정렬 / 검색 / 페이지 이동





## Data

데이터를 다루는 방식은 크게 세가지이다. 

- processing mode
- Data types
- Data sources



### Processing modes

데이터 테이블은 두가지모드의 데이터 처리방식을 가진다.

- 클라이언트 사이드 처리
  - 모든 데이터가 프론트로 오고 브라우저 상에서 처리
- 서버 사이드 처리 
  - ajax 요청이 테이블 재생성 마다 일어난다. 데이터 처리가 서버에서 일어난다.

각 방식은 장단점이 있지만 선택이 기준의 핵심이 되는 것은 **테이블 열의 개수**이다.

경험적으로 10000개 이하는 클라이언트 사이드 / 100000개 이상은 서버사이드  

중간은 자율적으로 판단한다 .

**두 방식은 상호배타적이다** 

동시에 쓰일 수 없을 뿐더러 동적으로 전환할 수도 없다.



**클라이언트 사이드 처리**

디폴트 값이다. 

사용하기 쉬우며 추가 코드를 작성하지 않아도 된다. 

클라이언트 사이드 처리모드에서는 다음동작이 브라우저와 데이터 테이블 자체에서만 진행된다 .

- 데이터 정렬
- 검색
- 페이징
- 다른 모든 데이터 처리 



**서버사이드 처리**

서버사이드 처리가 사용되는 경우는 테이블에서 보여주고 시픈 데이터가 매우 많은 경우이다.

이 레벨에서는 클라이언트에 데이터를 보내고, 자바스크립트가 데이터를 처리하게 하는 것은 

눈에 띄는 간접비를 수반하고 어플리케이션의 성능 저하를 유발할 수 있다. 

서버사이드 처리 모드에서는 모든 정렬, 검색 등이 데이터베이스를 사용하는 서버로 전달된다. 

또한 동작이 발생할때마다 고도로 조정된다.  데이터의 각 페이지는 ajax 요청을 수반한다. 

각 동작마다 시간이 부여되지만 프론트상에서의 데이터로드보다는 선호된다. 



### Data source types

항상 배열이 사용된다. 배열의 요소들은 행을 구성한다. 

데이터 테이블은 행 데이터로 3가지 데이터 타입을 사용할 수 있다.

- Array

- Objects
- Instance

```js
columns.data 
columns.render
```

기본값은 array 이다.



**Array**

행데이터 아이템의 인덱스 순으로 column을 구성한다.

특정 데이터 추출 시 필요한 인덱시를 명시

- 사용예시

```js
$('#example').DataTable({
    data: data
});
```



**Objects**

직관적인 사용에 유용하다.

주로 API 데이터를 다룰때 사용(중요!)

- 프로퍼티 이름을 통해 필요한 데이터를 추출하여 보여줄 수 있다. 
- 데이터 테이블로 보여지는 것보다 더많은 정보를 포함할 수 있다. 
  - 데이터 베이스의 primary key
  - 해당 정보는 사용자에게 보여지지 않는다.

- 사용예시

```js
$('#example').DataTable({
    data: data,
    columns: [
        {data: 'name'},
        {data: 'position'},
        {data: 'salary'},
        {data: 'office'}
    ]
});
```



**Instance**

자바스크립트 객체 인스턴스로부터의 데이터를 보여줄때 유용

- 당장은 몰라도 될거같다



### Data Sources

처리모드와 데이터 행데이터 타입이 정해졌다면, 데이터 소스를 정해야한다.

- DOM
- Javascript
- Ajax sourced data

**DOM**

**HTML5**

- data- 어트리뷰트를 사용할 수 있다.
  - ordering : data-order / data-sort
  - search: data-search / data-filter



**Javascript**

초기화 옵션으로 행 데이터 타입을 선택할 수 있다.

자바스크립트가 접근할 수 잇는 데이터라면, ajax나 websocket 등의 데이터도 데이터 테이블에 보낼 수 있다.

DataTables API를 극도로 사용하는 방식이다. 



**Ajax**

자바스크립트 소스데이터와 유사하지만 데이터 테이블이 ajax 통신을 생성한다.

 ajax옵션에의해 관리된다(프로퍼티와 url). 객체와 배열데이터만 쓸수있다.

서버사이드 처리는 ajax소스데이터를 위한 특별한 형식이다.  해당 페이지가 보여져야 할때만 ajax 요청이 응답된다. 이는 데이터 베이스 엔진이 큰 데이터 셋을 쓸수있게 해준다. 





## Ajax

- http api 피드데이터 
- html에서 테이블 데이터 로직 분리



로드가 끝난 ajax 데이터를 추가하는것은 데이터 테이블로 하여금 deferRender 옵션을 통해서  필요할때만 돔요소가 생성되게 할 수 있다. cpu 초기로드를 줄인다



### Loading data

ajax 데이터는 요청이 만들어져야하는 url을 입력함으로서 로드할 수 있다.

```js
$('#myTable').DataTable({
    ajax: '/api/myData'
});
```



### JSON data source

ajax 데이터를 다룰때 거의 항상 json 페이로드를 사용한다.

서버에서 받아오는 형식이 json 형식이며 json 형식이 호환이 잘되기 때문이다.

json 데이터를 사용할때 두가지 key 정보가 필요

- object안에 행데이터를 표현하는 데이터의 배열의 위치
- 행 객체안에 열의 데이터포인트 위치



### Data array location

데이터 테이블은 행데이터를 담은 아이템들의 배열이 필요하다.

일반적으로 객체나 배열 형태이다.

첫번째로 할것은 데이터 테이블에 데이터 소스에서 배열의 위치를 알리는 것이다. 

데이터 배열의 위치에따라서 초기화를 다르게 해준다.

**example**

- 일반적 배열 데이터

```js
$('#myTable').DataTable({
    ajax: {
        url: '/api/myData',
        dataSrc: ''
    },
    columns:[{...},{...},...]
});
```

- data프로퍼티로 저장 
  - data 프로퍼티는 디폴트 값이어서 생략해도 된다. 

```js
$('#myTable').DataTable({
    ajax: '/api/myData',
    columns: [...]
});
//or
$('#myTable').DataTable({
    ajax: {
    	url: '/api/myData',
        dataSrc: 'data'
    },
    columns: [...]
    
});
```

- 'data'가 아닌 프로퍼티에 저장

```js
$('#myTable').DataTable({
    ajax: {
    	url: '/api/myData',
        dataSrc: 'propertyName'
    },
    columns: [...]
    
});
```



### Column data points

데이터를 로드했다면 행의 어느셀에 데이터를 넣을지 정한다. columns.data 옵션으로 수행





## Options

여러 옵션으로 컨텐츠를 커스터마이즈할 수 있다.

초기화할때 추가한다



### Setting options

데이터 테이블 생성자 함수에 원하는 옵션을 추가하여 사용한다.

**페이징의 유무 결정**

```js
$('#example').DataTable({
    paging: false
})
```



**페이지 내 스크롤 기능**

```js
$('#example').DataTable({
    scrollY: 400
});
```



**한페이지 & 스크롤**

```js
$('#example').DataTable({
    scrollY: 400,
    paging: false
});
```

