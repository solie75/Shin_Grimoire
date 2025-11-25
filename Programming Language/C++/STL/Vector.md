# emplace_back

- 컨테이너의 끝에 객체를 직접 생성하여 삽입한다. -> 임시 객체를 생성하고 복사하거나 이동하는 비용을 줄일 수 있다.
- 객체를 생성할 때 복사 생성자 또는 이동 생성자를 호출하지 않고, 인자만 전달하여 컨테이너 내부에서 객체를 직접 생성한다.
- push_back 의 경우 이미 생성된 객체를 복사하거나 이동하여 컨테이너에 삽입하기 때문에 추가적인 복사/ 이동 비용이 발생할 수 있다.

```c++
class Point
{
public:
    int x, y;

    Point(int _x, int _y) : x(_x), y(_y) {
        cout << "Point(" << x << ", " << y << ") created." << "\n";
    }
};

int main()
{
    vector<string> words;

    words.emplace_back("and");
    words.emplace_back("but");

    for (const auto& word : words)
    {
        cout << word << "\n";
    }

    vector<Point> points1;

    points1.emplace_back(1, 2);
    points1.emplace_back(3, 4);

    for (const auto& point : points1)
    {
        cout << "Point(" << point.x << ", " << point.y << ")" << "\n";
    }
}
```

```c++
// 결과
and
but
Point(1, 2) created.
Point(3, 4) created.
Point(1, 2)
Point(3, 4)
```
- emplace_back 은 인자를 전달하여 직접 생산.

- push_back 과 비교하면 다음과 같다.
```c++
class Test
{
public:
    int value;

    Test(int _v) : value(_v) {
        cout << "Test(" << value << ") created." << "\n";
    }

    Test(const Test& _other)
    {
        value = _other.value;
        cout << "Test copied." << "\n";
    }
};

int main()
{
    vector<Test> tests;
    tests.reserve(3);

    Test t1(10);
    tests.push_back(t1); // 복사
    tests.push_back(Test(20)); // 임시 객체 생성 + 이동.

    tests.emplace_back(30); // 직접 생성 , 복사x 이동x
}
```

```c++
// 결과
Test(10) created.
Test copied.
Test(20) created.
Test copied.
Test(30) created.
```


# emplace

- vector 외에 map 등 c++ 표준 컨테이너에서 특정 위치에 객체를 직접 생성할 때 사용.
- 컨테이너의 특정 위치에 객체를 생성하여 삽입.

```c++
class Test
{
public:
    int value;

    Test(int _v) : value(_v) {
        cout << "Test(" << value << ") created." << "\n";
    }

    Test(const Test& _other)
    {
        value = _other.value;
        cout << "Test copied." << "This value is " << value <<  "\n";
    }
};

int main()
{
    vector<Test> tests;
    tests.reserve(4);

    Test t1(10);
    tests.push_back(t1); // 복사
    tests.push_back(Test(20)); // 임시 객체 생성 + 이동.

    tests.emplace_back(30); // 직접 생성 , 복사x 이동x
    tests.emplace(tests.begin() + 1, Test(100));
}
```

```c++
Test(10) created.
Test copied.This value is 10
Test(20) created.
Test copied.This value is 20
Test(30) created.
Test(100) created.
Test copied.This value is 100
Test copied.This value is 30
```