---
date: 2025-08-22
title: FutureBuilder 사용하기
layout: post
categories:
  - Flutter
tags:
  - flutter
  - dart
---
```dart
return FutureBuilder(
      future: loadAsset(widget.question),
      builder: (context, snapshot) {
        if (snapshot.hasData == false) {
          return const CircularProgressIndicator();
        } else if (snapshot.hasError) {
          return Padding(
            padding: const EdgeInsets.all(8.0),
            child: Center(
              child: Text(
                'Error: ${snapshot.error}',
                style: const TextStyle(fontSize: 15),
              ),
            ),
          );
        } else {
          Map<String, dynamic> questions = jsonDecode(snapshot.data!);
          title = questions['title'].toString();
          List<Widget> widgets;
          widgets = List<Widget>.generate(
            (questions['selects'] as List<dynamic>).length,
            (int index) => SizedBox(
              height: 100,
              child: Column(
                children: [
                  Text(questions['selects'][index]),
                  Radio(
                    value: index,
                    groupValue: selectNumber,
                    onChanged: (value) {
                      setState(() {
                        selectNumber = index;
                      });
                    },
                  ),
                ],
              ),
            ),
          );
```
##### FutureBuilder : 배송 추적기 

1. 어떤 물건을 기다릴지 알려준다. (`future: loadAsset()`)
	- `future` 속성에 json 데이터를 가져오는 `loadAsset()`연결 
	- "내가 받을 물건은 `loadAsset()`이 가져올 json파일" 이라고 알려주는 셈

2. 배송 상태에 따라 다른 화면 보여줌 (`builder: (context, snapshot) {...})`
	-  `builder`는 배송 상태가 바뀔 때마다 계속해서 호출됨. 
	- `snapshot`이라는 **배송 상태 보고서**를 받아 현재 상태를 확인

---
##### snapshot (배송 상태 보고서) 
`snapshot`객체 안에는 `connectionState`라는 현재 상태 정보가 들어있다. 이 상태에 따라 `switch`문으로 분기 처리 

- `case ConnectionState.waiting:` / `case ConnectionState.active:`
	- **의미** : 데이터를 불러오는 중 
	- **동작** : 화면 중앙에 `CircularProgressIndicator()` 렌더링

- `case ConnectionState.none:`
	- **의미** : "아직 배송이 시작되지 않음" 즉 비동기 작업이 아직 시작 안됨. 
	- **동작** : 화면 중앙에 'No data' 렌더링

- `case ConnectionState.done:`
	- **의미** : 데이터를 성공적으로 다 불러온 상태
	- **동작** : 도착한 데이터를 사용해서 화면을 꾸밈(렌더링)
---
##### `ConnectionState.done`(데이터 사용하기)

1. `Map<String, dynamic> list = jsonDecode(snapshot.data!);`
	- `snapshot.data!` : "배송 완료된 실제 물건" 즉, `loadAsset()`이 반환한 `list.json`파일의 **내용 전체가 담긴 문자열**
	- `jsonDecode()` : 받아온 데이터는 그냥 긴 String 덩어리라 바로 사용하기 어렵다. Dart가 이해할 수 있는 `Map`형태로 변환 

2. `return ListView,builder(...)` 
	- `Map`으로 변환된 데이터를 사용해 화면에 목록을 표시 `ListView.builder`는 효율적으로 목록을 만들어주는 위젯 
	- `itemCount: list['count']` : 목록에 표시할 아이템의 총 개수를 지정. `list.json`파일 안에 `count`라는 키가 있고, 그 값만큼 아이템을 만들겠다는 의미 
	- `itemBuilder: (context, value) {...}` : 각 아이템이 어떻게 생겼을지를 정의하는 부분. `itemCount`만큼 반복해서 호출된다. `value`는 0,1,2 순으로 증가하는 인덱스. 

3. **각 아이템 표시**
	- `list['questions'][value]['title'].toString()` : 
		- `list['questions']`: `Map`으로 변환된 데이터에서 'questions'라는 키에 해당하는 값을 가져온다. 
		- `[value]` : 그 질문 목록에서 현재 인덱스(`value`)에 해당하는 하나의 질문 데이터를 가져옴 
		- `['title']` : 그 질문 데이터에서 'title'이라는 키에 해당하는 값을 가져온다. 
	- 가져온 제목을 `Text`위젯으로 감싸서 화면에 보여주고 `Card` `InkWell`로 보기좋게 꾸며줌.