---
title: report refactor20201214
date: 2020-12-14 19:54:18
tags:
---

# Report codeRefactoring

- 20201214 회사에서 만든 함수로 연습
- 데이터의 우선순위 최상단, 최하단으로 이동시키는 함수

**처음에 만든거**

```js
$scope.moveEdge = function (idx, direction) {
  if (idx === 1 && direction === "top") return;
  if (idx === $scope.ruledata.data[0].Rules.length && direction === "bottom")
    return;

  if (direction === "bottom") {
    let rules = $scope.ruledata.data[0].Rules;
    const target = rules.find((item) => item.Priority === idx + "");
    // rule 배열 인덱스
    const targetNum = rules.findIndex((item) => item.Priority === idx + "");
    rules = rules.map((item) =>
      +item.Priority > +target.Priority
        ? { ...item, Priority: +item.Priority - 1 + "" }
        : { ...item }
    );

    $scope.ruledata.data[0].Rules = rules.map((item, index) =>
      targetNum === index
        ? { ...item, Priority: rules.length + "" }
        : { ...item }
    );
  }

  if (direction === "top") {
    let rules = $scope.ruledata.data[0].Rules;
    const target = rules.find((item) => item.Priority === idx + "");
    const targetNum = rules.findIndex((item) => item.Priority === idx + "");
    rules = rules.map((item) =>
      +item.Priority < +target.Priority
        ? { ...item, Priority: +item.Priority + 1 + "" }
        : { ...item }
    );

    $scope.ruledata.data[0].Rules = rules.map((item, index) =>
      targetNum === index ? { ...item, Priority: "1" } : { ...item }
    );
  }
};
```

**리팩토링**

- 대조되는 조건 함수화, 변수화

```js
$scope.moveEdge = (idx, direction) => {
  if (idx === $scope.ruledata.data[0].Rules.length && direction === "bottom")
    return;
  if (idx === 1 && direction === "top") return;

  let rules = $scope.ruledata.data[0].Rules;
  const target = rules.find((item) => item.Priority === idx + "");
  // rule 배열 인덱스
  const targetNum = rules.findIndex((item) => item.Priority === idx + "");
  const targetPriority = direction === "bottom" ? rules.length + "" : "1";
  const changePriority = (item) =>
    direction === "bottom" ? item - 1 : item + 1;
  const comparePriority = (item, target) =>
    direction === "bottom"
      ? +item.Priority > +target.Priority
      : +item.Priority < +target.Priority;

  rules = rules.map((item) =>
    comparePriority(item, target)
      ? { ...item, Priority: changePriority(+item.Priority) + "" }
      : { ...item }
  );
  $scope.ruledata.data[0].Rules = rules.map((item, index) =>
    targetNum === index ? { ...item, Priority: targetPriority } : { ...item }
  );
};
```
