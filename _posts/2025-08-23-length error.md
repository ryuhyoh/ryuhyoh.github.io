---
date: 2025-08-23
title: length error
layout: post
categories:
  - debug
  - Dart
tags:
  - debug
  - dart
---
심리 앱 질문 목록페이지를 구현하는 중에 3개의 질문이 있어야하는데 자꾸 2개의 목록만 렌더링 되는 현상을 확인했다. 

문제의 코드는 다음과 같았다. 
```dart 
case ConnectionState.done:
              Map<String, dynamic> list = jsonDecode(snapshot.data!);
              return ListView.builder(
                itemCount: list.length
                itemBuilder: (context, value) {...}
```
언뜻 보기엔 아무 이상이 없어보이지만, 
문제는 `list.length`에 있었다. 

`list`는 리스트가 아닌 **Map**이기 때문에 질무의 개수가 아닌 **맵의 최상위 키** 개수를 반환하게 된다. 

json 파일을 보게 되면 
```json
{

  "questions": [
    {
      "title": "MBTI",
      "file":"mbti"
    },
    {
      "title": "당신은 어떤 사랑을 하고 싶나요",
      "file":"test2"
    },
    {
      "title": "당신이 좋아하는 애완동물은",
      "file":"test1"
    }
  ],
  "count": 3
}
```
- 이런 형식으로 되어있기 때문에 `list.length`는 최상위 키인 'questions'와 'count' 개수인 2를 반환하게 된다. 

수정된 코드는 다음과 같다. 
```dart
case ConnectionState.done:
              Map<String, dynamic> list = jsonDecode(snapshot.data!);
              return ListView.builder(
                itemCount: list['count'],
                itemBuilder: (context, value) {
```


