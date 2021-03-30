---
title: Parsley
date: 2020-12-10 07:49:29
tags:
---



# Parsley

- SKB 업무를 위한 라이브러리 공부
- Parselyjs 공식문서 참조
  - https://parsleyjs.org/doc



## Overview

### Frontend form validation

- Parsely는 자바스크립트 폼 검증 라이브러리이다.
- 사용자가 입력한 폼을 서버에 보내기 전에 검사하고 피드백을 준다. 

**장점**

- 대역폭, 서버 로드 절약
- 유저에게도 시간 절약

자바스크립트의 폼 검사는 필수적이지 않다.

사용된다고 해도 백엔드 서버의 검증을 대체하지 않는다. 

**Parsley의 사용 이유**

- 일반저인 폼 검사를 정의하고 이것을 백엔드 사이드에서 실행한다. 
- 그리고 결과를 프론트 사이드로 보낸다.
- 사용자 경험을 최대로 고려한다. 



**version**

안정적으로 지원되는 버전 2.x



### Data attributes

- Parsley는 특정한 DOM API를 사용
  - DOM에서 직접적으로 거의 모든 것을 구성할수 있게 해준다.
- Javascript를 통한 구성이나 커스텀 함수를 작성할 필요 없다.
- Parsely의 default DOM API
  - data-parsley-
  - foo 프로퍼티를 사용한다면
    - data-parsley-foo="value"



## Installation

- jQuery 의존성

**Basic**

- js file 다운로드후 스크립트로 로드
- data-parsley-validate를 form 태그에 추가



**javascript**

```js
$('#form').parsley(options);
//or
new Parsley('#form',options)
```

**localization**

```html
<script src="i18n/fr.js"></script>
<script src="i18n/it.js"></script>
```



## Usage

### Overview

- 여러 클래스들을 쓰는 분리된 라이브러리



| API                                          | Return               |
| -------------------------------------------- | -------------------- |
| $('#existingForm').parsley(options)          | parsleyFormInstance  |
| $('#existingInput').parsley(options)         | parsleyFieldInstance |
| $('#notExistingDomElement').parsley(options) | undefined            |
| $('.multipleElements').parsley(options)      | Array                |



### Configuration

```html
<input id="first" data-parsley-maxlength="42" value="hello"/>
<input id="second" value="world"/>
[...]

<script>
var instance = $('#first').parsley();
    
console.log(instance.isValid()); // maxlength is 42, so field is valid
$('#first').attr('data-parsley-maxlength', 4);
console.log(instance.isValid()); // No longer valid, as maxlength is 4
// You can access and override options in javascript too:
instance.options.maxlength++;
console.log(instance.isValid()); // Back to being valid, as maxlength is 5
// Alternatively, the options can be specified as:
var otherInstance = $('#second').parsley({
  maxlength: 10
});
console.log(otherInstance.options); // Shows that maxlength will be 10 for this field
```

- 데이터 어트리뷰트로 추가
- dom요소에 대한 인스턴스 생성
- attr 메서드로 어트리뷰트 추가
- 인스턴스 생성시 옵션 객체 전달

등등



### Form

Options

| Property                      | Default      | Description                                                |
| ----------------------------- | ------------ | ---------------------------------------------------------- |
| data-parsley-namespace        | data-parsley | Parsley DOM API에서 DOM과 연결을위해 사용하는 네임스페이스 |
| data-parsley-validate         |              | document 로드시 parsley 검사 자동 바인드                   |
| data-parsley-priority-enabled | true         |                                                            |
| data-parsley-inputs           |              |                                                            |
| data-parsley-excluded         |              |                                                            |



**Methods**



| Method                       | return          | Description |
| ---------------------------- | --------------- | ----------- |
| whenValid({group, force})    | promise         |             |
| isValid({group, force})      | boolean or null |             |
| whenValidate({group, force}) | promise         |             |
| validate({group, force})     | boolean or null |             |
| refresh()                    |                 |             |
| reset()                      |                 |             |
| destroy                      |                 |             |



### Field



**Options**

| Property                              | Description |
| ------------------------------------- | ----------- |
| data-parsley-value                    |             |
| data-parsley-group                    |             |
| data-parsley-multiple                 |             |
| data-parsley-validate-if-empty        |             |
| data-parsley-whitespace               |             |
| data-parsley-ui-enabled               |             |
| data-parsley-errors-messages-disabled |             |
| data-parsley-excluded                 |             |
| data-parsley-debounce                 |             |



**Methods**

| Method                   | Returns | Description |
| ------------------------ | ------- | ----------- |
| isValid({force})         |         |             |
| validate({force, group}) |         |             |
| getErrorMessages()       |         |             |
| reset()                  |         |             |
| destroy()                |         |             |



## Example

**회사 코드 분석 및 연습**

```html
 <select data-parsley-group="step-4"
data-parsley-required="{{defaultSwitch}}"
style="text-align-last: center;" form="jobform"
ng-model="data.ThumbnailSource"
class="form-control col-12 text-center">
	<option value="original">원본 소스</option>
	<option value="{{data.PresetName}}"
	ng-repeat="dat
               a in presetdata.data.data">{{data.PresetName}}
	</option>
<input type="number" name="imgwidth" ng-model="data.ThumbnailWidth"
data-parsley-group="step-4"
data-parsley-required="{{data.ThumbnailHeight!=null}}"
data-parsley-error-message=""
class="form-control form-control col-3 text-center"></input>
<label class="col-2 col-form-label text-center">x</label>
<input type="number" name="imgheight" data-parsley-group="step-4"
data-parsley-required="{{data.ThumbnailWidth!=null}}"
ng-model="data.ThumbnailHeight"
class="form-control form-control col-3 text-center"></input>
</div>
<input type="number" data-parsley-group="step-4"
data-parsley-required="{{defaultSwitch}}"
ng-model="data.ThumbnailNum" class="form-control text-center"
onKeyup="this.value=this.value.replace(/[^0-9]/g,'');" />
```



- 사용한 어트리뷰트
  - data-parsley-group
  - data-parsley-required



```js
  $('#wizard').on('leaveStep', function(e, anchorObject, stepNumber, stepDirection) {

    if (stepDirection == "forward") {
      var res = $('form[name="jobform"]').parsley().validate('step-' + (stepNumber + 1));
      if (res == true) {

      };
    }
    return res;
  });
// form[name="jobform"] 에대한 인스턴스 생성후 바로 validate 메서드 호출
// validate메서드에 대한 인수로 검사할 parsley 그룹명 전달

// job template 객체
$scope.customTemp =
        {
          "TemplateName": "",
          "CustomerName": $stateParams.customer_name,
          "CustomerID": $stateParams.customer_id,
          "CreatorID": 0,
          "CreatorName": $rootScope.globals.currentUser.username,
          "RegExp": "",

          "SKBStorageOutput": "",
          "CustomerStorageOutput": "",
          "Transcodings": [
            {
              "PresetName": "",
              "Output": "",
            }
          ],
          "DefaultThumbNail": [
            {
              "ThumbnailSource": "original",
              "ThumbnailNum": null,
              "ThumbnailWidth": null,
              "ThumbnailHeight": null,
              "StartTime": null,
              "Output": "/thb/$filepath/$filename_$prefix_$count.png"
            }
          ],
          "GridThumbNail": [
            {
              "ThumbnailSource": "original",
              "ThumbnailInterval": null,
              "ThumbnailWidth": null,
              "ThumbnailHeight": null,
              "StartTime": null,
              "Output": "/thb/$filepath/$filename_$prefix_$second.png"
            }
          ],
          "Callback": {
            "TransferJSON": "true",
            "Parameter": [
              ""
            ]
          }

        }
// 썸네일 추가 함수
$scope.addDefaultThumbnail = function() {
    var temp = {
      "ThumbnailSource": "original",
      "ThumbnailNum": null,
      "ThumbnailWidth": null,
      "ThumbnailHeight": null,
      "StartTime": null,
      "Output": "/thb/$filepath/$filename_$prefix_$count.png"
    };

    $scope.customTemp.DefaultThumbNail.push(temp);
    $scope.defaultCount = $scope.customTemp.DefaultThumbNail.length;

  }
```







```js

```

```html
<div class="row p-15" >
          <div class="panel-body overflow-auto w-100 " style="padding: 0 20vw">
            <form name="userForm" class="form-horizontal form-bordered">
              <div class="form-group row">
                  <label class="col-lg-4 col-form-label" style = "justify-content: center;">고객사 선택</label>
                  <div class="col-lg-8">
                      <select ng-model="userJson.companies" class="form-control form-control-lg ">
                          <option value="{{data.id}}" ng-repeat="data in customerJson">{{data.name}}
                          </option>
                      </select>
                  </div>
              </div>
              <div class="form-group row">
                  <label class="col-lg-4 col-form-label" style = "justify-content: center;">아이디</label>
                  <div class="col-lg-8">
                      <input data-parsley-group="step-1" data-parsley-required="true" type="text"
                          ng-model="userJson.user_id" class="form-control" placeholder="ID 입력">
                  </div>
              </div>
              <div class="form-group row">
                  <label class="col-lg-4 col-form-label" style = "justify-content: center;">비밀번호</label>
                  <div class="col-lg-8">
                      <div class="input-group">
                          <input data-parsley-error-message="" data-parsley-group="step-1"
                              data-parsley-required="true" ng-model="userJson.password"
                              data-placement="after" class="form-control" type="{{inputType}}"
                              placeholder="비밀번호 입력" id="pass">

                          <div class="input-group-append" style="cursor: pointer;"><button tabindex="100"
                                  ng-click="showPassword()" title="Click here to show/hide password"
                                  class="btn btn-outline-secondary" type="button">
                                  <i class="{{showHideClass}}">

                                  </i></button></div>
                      </div>
                  </div>
              </div>
              <div class="form-group row">
                  <label class="col-lg-4 col-form-label" style = "justify-content: center;">비밀번호 확인</label>
                  <div class="col-lg-8">
                      <input data-parsley-equalto="#pass" data-parsley-group="step-1"
                          data-parsley-required="true" type="{{inputType}}" class="form-control"
                          placeholder="비밀번호 입력확인">
                  </div>
              </div>
              <div class="form-group row">
                  <label class="col-lg-4 col-form-label" style = "justify-content: center;">유저 이름</label>
                  <div class="col-lg-8">
                      <input data-parsley-group="step-1" data-parsley-required="true" type="text"
                          ng-model="userJson.name" class="form-control" placeholder="유저 이름 입력">
                  </div>
              </div>
              <div class="form-group row">
                  <label class="col-lg-4 col-form-label" style = "justify-content: center;">이메일</label>
                  <div class="col-lg-8">
                      <input data-parsley-group="step-1" data-parsley-required="true" type="text"
                          ng-model="userJson.email" class="form-control" placeholder="이메일 입력">
                  </div>
              </div>
              <div class="form-group row">
                  <label class="col-lg-4 col-form-label" style = "justify-content: center;">연락처</label>
                  <div class="col-lg-8">
                      <input data-parsley-group="step-1" data-parsley-required="true" type="text"
                          ng-model="userJson.phone" class="form-control" placeholder="연락처 입력">
                  </div>
              </div>
              <div class="form-group row">
                  <label class="col-lg-4 col-form-label" style = "justify-content: center;">Storage 사용 여부</label>
                  <div class="col-lg-8">
                      <div class="form-check">
                          <input type="checkbox" ng-model="storageSwitch" class="form-check-input"
                              id="defaultCheckbox" />

                      </div>
                  </div>
              </div>


          </form>

          <div ng-show="storageSwitch" class="panel panel-inverse">
            <!-- begin panel-heading -->
            <div class="panel-heading ">
                <h4 class="panel-title">Storage 사용정보</h4>
            </div>

            <div class="panel-body">

                <form class="form-horizontal form-bordered">

                    <div class="form-group row">
                        <label class="col-lg-4 col-form-label">고객사 스토리지 Host</label>
                        <div class="col-lg-8">
                            <input type="text" class="form-control" ng-model="userJson.Host"
                                placeholder="Host 입력">
                        </div>
                    </div>
                    <div class="form-group row">
                        <label class="col-lg-4 col-form-label">ID</label>
                        <div class="col-lg-8">
                            <input type="text" ng-model="userJson.id" class="form-control" placeholder="">
                        </div>
                    </div>
                    <div class="form-group row">
                        <label class="col-lg-4 col-form-label">Passwrod</label>
                        <div class="col-lg-8">
                            <input type="text" ng-model="userJson.pass" class="form-control" placeholder="">
                        </div>
                    </div>



                </form>

            </div>

        </div>

          </div>
        </div>
 <div class="col-6" style="text-align: right;padding-right: 4vw">
          <button ng-click="Ok()" type="button" class="btn btn-primary">확인</button>
        </div>
        <div class="col-6" style="text-align: left; padding-left: 4vw">
          <button ng-click="Cancel()" type="button" class="btn btn-default ">취소</button>
        </div>
```

