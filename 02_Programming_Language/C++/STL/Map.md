키 - 값 쌍을 저장. 삽입하면 키 기준으로 자동 정렬 된다.

```c++
map<string, int> TestMap;

TestMap["Second"] = 2;
TestMap["First"];
TestMap.insert(make_pair("Third", 3));
// 순서대로 ("First", 0) ("Secoind", 2) ("Third", 3)

int TempInt1 = TestMap.size(); // TempInt1 은 3;

TestMap.erase("Second");
// 순서대로 ("First", 0) ("Third", 3)

map<string, int>::iterator iter = TestMap.begin();

iter = TestMap.find("Second"); // iter 은 TestMap.end()
iter = TestMap.find("First"); // iter 은 Testmap.begin() 을 가리킨다.

TestMap["First"] = 1; // 키 "First" 의 값은 1로 수정.

int TempInt2 = TestMap.at("Third"); // TempInt2 는 3;
```

- \[ ]
- insert
- erase
- size
- find
- at

# Multimap

같은 키를 중복해서 저장할 수 있다.
하나의 키에 여러 개의 value 가 들어갈 수 있다.

```c++
multimap<string, int> MultiMap;

MultiMap.insert({ "First", 1 });
MultiMap.insert({ "First", 11 });  // 순서대로 ("First", 1) ("First", 11)
// [ ] 로 추가 불가능.
MultiMap.insert({ "Second", 2 });
MultiMap.insert({ "Third", 3 });

MultiMap.erase("Second");
multimap<string, int>::iterator iter = MultiMap.find("Second");  // iter 는  MulriMap.end()를 가리킨다.
iter = MultiMap.find("Third");
int temp = iter->second;  // temp 는 3이다.

pair< multimap<string, int>::iterator, multimap<string, int>::iterator> range = MultiMap.equal_range("First");
// range.first 는 Key 가 First 인 것 중 첫번째 iterator (즉, ("First", 1)),  range.second 는 Key 가 First 인 것 중 마지막 iterator 의 다음을 가리킨다 (즉, "Third", 3).
```

- insert
- erase
- find
- equal_range
