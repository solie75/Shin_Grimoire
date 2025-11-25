# Constructor, Destructor

```c++
class parent
{
public:
	parent(int _i) { num = _i; cout << "parent constructor Num : " << GetNum() << "\n"; }
	~parent() { cout << "parent Destructor Num : " << GetNum() << "\n";
	}
	//virtual ~parent() { cout << "parent Destructor Num : " << GetNum() << "\n";
}

private:
	int num = 0;
public:
	int GetNum() {
		return num;
	}
};

class child : public parent
{
public:
	child(int _i) : parent(_i) { cout << "child constructor Num : " << GetNum() << "\n"; }
	~child() { cout << "child destructor Num : " << GetNum() << "\n"; }
};

int main()
{
	parent P(10);
	child C(1);
	parent* pP = new parent(20);
	parent* pC = new child(2);
	parent* pX = new child(1000);

	delete pP;
	delete pC;
	delete pX;

	return 0;
}

// 결과
parent constructor Num : 10
parent constructor Num : 1
child constructor Num : 1
parent constructor Num : 20
parent constructor Num : 2
child constructor Num : 2
parent constructor Num : 1000
child constructor Num : 1000
parent Destructor Num : 20
parent Destructor Num : 2
parent Destructor Num : 1000
child destructor Num : 1
parent Destructor Num : 1
parent Destructor Num : 10
```

- child destructor Num : 1000 이 없음. 이는 자식 객체를 부모 타입의 변수에 할당했을 때 소멸시 부모 클래스의 소멸자는 정상 호출되지만 자식 클래스 소멸자는 호출되지 않는 문제이다.
	-> 이는 부모 클래스의 소멸자를 virtual로 선언하면 가능하다.