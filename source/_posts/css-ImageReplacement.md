---
title: css ImageReplacement
date: 2020-11-23 18:15:13
tags:
---
# IR 기법(CSS)



## text-indent

```css
element{
    display: block;
    overflow: hidden;
    font-size: 0;
    line-height: 0;
    text-indent: -9999px
}
```



## position



- 텍스트는 span태그로 감싼다

```css
element {
	display:block;
    overflow: hidden;
    position:relative;
    z-index:-1;
    width: 100%;
    heigth: 100%;
}
```

