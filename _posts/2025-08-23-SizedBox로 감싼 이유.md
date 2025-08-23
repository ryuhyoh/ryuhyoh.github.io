---
date: 2025-08-23
title: SizedBox로 감싸는 이유
layout: post
categories:
  - Flutter
tags:
  - flutter
---
```dart
return InkWell(
                    child: SizedBox(
                      height: 50,
                      child: Card(
                        child: Text(
                          list['questions'][index]['title'].toString(),
                        ),
                      ),
                    ),
```
위 코드를 보고 왜 `InkWell` 자식으로 바로 `Card`를 쓰지 않고 `SizedBox`로 한 번 감싸야하나 싶어 알아보았다. 

결론부터 말하면 **탭이 가능한 전체 영역의 높이를 50으로 일정하게 고정**하고 **물결효과가 나타나는 범위를 명확하게** 하기 위해서이다. 

##### `SizedBox`가 없다면?
###### 1. 높이가 제각각이 됨 : 
- `Card`는 자식 위젯의 크기에 맞춰 자신의 크기를 조절한다. 만약 텍스트가 한 줄이면 `Card`는 작아지고, 텍스트가 길어서 두 줄이 되면 `Card`는 더 커지게 된다. 이렇게 되면 리스트의 각 항목 높이가 달라져 UI가 지저분해 보이게 된다. 
###### 2. 탭 영역이 불규칙 해짐 : 
- `InkWell`의 탭 영역과 물결 효과는 자식 위젯의 크기를 따라간다. 즉 `Card`의 크기가 제각각이면 사용자가 탭할 수 있는 영역의 크기도 항목마다 달라져 일관된 사용자 경험을 주기 어렵게 된다. 