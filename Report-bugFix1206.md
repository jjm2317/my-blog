---
title: Report bugFix1206
date: 2020-12-10 07:59:26
tags:
---

## 20201206 bugfix

- SKB 20201206(일) 테스트 및 버그 수정 

**cors policy error**

- Cross-origin resource sharing
- 초기 도메인과 다른 도메인으로부터의 자원 제공 제한
- 요청 헤더에 검증 토큰전송
- 캐시때문에 에러가 뜰수도있음
  - 캐시에 대해 공부해보기
- 프록시 공부하기
- cors 가 발생안하도록 origin을 일치시키는것이 최선의 방법이라고 한다.



**RegExpression 백슬래시 삭제에러**

- 개발자도구 에서 response를 확인하니 문자열안의 백슬래시\ 삭제되는 에러
- 프론트쪽 코드를 확인해봤으나 request header에 문자열 그대로 전송
- db 쪽에서 데이터 변환과정에서 문제 발생
  - \ 대신 // 로 변환하여 문제 해결되었다고 전달
- RegExpression 및 데이터 전달과정 알아보기



남은 fix list

- 작업 페이지에서 새로고침하면 이전 페이지로 돌아가기 

- 유효성 검사

- ux 개선

  


    

- 코드 refactoring

  - es6문법 미사용되어 있음

    - 변수 선언 키워드
    - 제어문
    - if문 중첩 
    - 등등

  - 비교연산자로 == 만써서 타입 검사가 안됨



    
## 20201209 버그 픽스

- 중복 체크가 안되어있으면 완료안됨
  - 완료버튼 누를때 체크여부
- 체크 완료 후 재입력하면 중복체크가 false
- false인 상태로 완료 누르면 중복 체크 에러 메시지





```js
  $scope.dupCheckMessage=""
  $scope.checkName = name =>{
    // if(!name){
    //   $scope.dupCheckMessage = "이름을 입력하세요";
    //   $scope.dupChecked = false;
    //   return;
    // }
    $scope.dupChecked = $scope.customerNames.includes(name) ? false : true;
    
    // if(!$scope.dupChecked) $scope.parsleyOfName.addError('dupError',{message: '중복에러'});
      // $http({
      // method: 'get',
      // url: 'https://userapi-dev.myskcdn.net/tr/2020-11-25/customer/id/list', //
      // headers: { "Authorization": $rootScope.globals.currentUser.authdata }
      // })
      // .then(response => response.data.data.map(customer => customer['name']))
      // .then(customerNames => {
      //   console.log(customerNames)
      //   $scope.dupChecked = customerNames.includes(name) ? false : true;

      //   $scope.dupCheckMessage = $scope.dupChecked ? "유효한 이름입니다." : "이미 존재하는 이름입니다."
      //   console.log($scope.dupChecked);
      // })
      
    
  }

```



**answer**

```js
  window.Parsley.addValidator('duplication', {
    
    validateString: (name, nameList)  =>{
      const nameArray = nameList.split('|');
      return nameArray.includes(name) ? false : true;
    },
    messages: {
      ko: '이미 존재하는 이름입니다.'
    }
  });
  $scope.parsleyOfForm = $('.customer__info').parsley();
```



- 접기버튼 목록
  - 내정보보기
  - 고객사 관리
  - 사용자 관리
  - 사용자 그룹
  - 도메인 그룹














