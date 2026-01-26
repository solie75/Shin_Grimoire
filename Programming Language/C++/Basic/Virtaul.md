- 가상함수란?
런타임 다형성을 구현하기 위해 사용된다. 이는 부모클래스의 포인터가 자식 객체를 가리키고 있을 때, 컴파일 타임이 아닌 실제 런타임 객체 타임에 맞춰서 재정의 된 함수를 호출하도록 보장해준다.

내부적으로는 V-Table 과 vptr 을 통해 함수 주소를 동적으로 바인딩하는 방식으로 동작한다. 다만 테이블 잠조하는 이 과정이 곧 미세한 오버헤드 이기 때문에 자주 호출되는 가벼운 연산등에는 주의하여야 한다.

V-Table 에는 virtual 로 선언된 함수만 기록한다. v-table 은 클래스마다 하나 존재하며 정적 메모리이다. 컴파일 타임에 이미 완성되어 있다. v-ptr 은 객체마다 하나씩 가진다. 자신이 속한 클래스의 v-table 을 가리킨다. 객체가 생성될 때(생성자 실행시) 설정된다.

- Upcasting
```c++
ParentClass* ptr = new ChildClass();
```

- 생성자 및 소멸자
생성자 호출 순서는 부모 $\to$ 자식 이며, 소멸자 호출 순서는 자식 $\to$ 부모 이다. 자식은 부모의 맴버 변수와 기능을 사용할 수 있어야 한다. 따라서 부모 생성자로 미리 맴버 변수와 기능을 초기화 하여야 한다. 자식이 소멸되면서 부모 멤버 변수 등을 참조할 수 있다. 즉, 부모가 먼저 소멸하면 자식은  엉뚱한 메모리를 참조할 가능성이 생긴다.

부모 소멸자가 Virtual 이 아니면 자식 소멸 단계를 무시하고 부모 소멸만 실행해 버리게 되어 메모리 누수가 터진다. 컴파일러가 부모 클래스에 접근하여 바인딩된 자식 클래스의 오버라이드 함수가 있는지 확인하는데 부모 소멸자가 Virtaul 로 선언되지 않는다면 자식의 소멸자는 부모의 소멸자를 override 하지 못하기 때문이다.

- V-Table
컴파일러는 각 클래스 마다 고유의 v-table 을 만든다.
최 상위 부모 클래스를 먼저 v-table 을 만들고 그 자식 v-table 을 만든다. 이때 부모 클래스의 V-Table 을 복사하여 자식 V-Table 을 만든다 (구조가 같다). 자식 클래스에서 override 한 함수가 있다면 v-table 에서 해당 함수 포인터가 저장된 index 를 찾는다. 복사하였기 때문에 해당 인덱스에 이미 존재하던 부모의 함수 포인터를 지우고 그 함수를 오버라이드한 자식 함수 포인터를 지정한다. 

- 생성
ParentClass* ptr = new ChildClass();

실제 메모리에 생성되는 것은 ChildClass 객체이다. 이 객체가 들고 있는 vptr(가상함수 포인터) 는 Class C 의 v-table 을 가리키도록 초기화한다.

```c++
#pragma once
#include <iostream>

class ParentClass
{
public:
    ParentClass() { std::cout << "ParentClass Constructor" << std::endl; }
    virtual ~ParentClass() { std::cout << "ParentClass Destructor" << std::endl; }

public:
    virtual void AFunction() { std::cout << "Parent AFunction" << std::endl; }
    virtual void BFunction() { std::cout << "Parent BFunction" << std::endl; }
    void CFunction() { std::cout << "Parent CFunction" << std::endl; }
};

class ChildClass : public ParentClass
{
public:
    ChildClass() { std::cout << "ChildClass Constructor" << std::endl; }
    ~ChildClass() { std::cout << "ChildClass Destructor" << std::endl; }

public:
    void AFunction() override { std::cout << "Child AFunction" << std::endl; }

    void BFunction() { std::cout << "Child BFunction" << std::endl; }
    void CFunction() { std::cout << "Child CFunction" << std::endl; }
};

int main() {
    ParentClass* ptr1 = new ChildClass();

    ptr1->AFunction();
    ptr1->BFunction();
    ptr1->CFunction();

    delete ptr1;

    ChildClass* ptr2 = new ChildClass();

    ptr2->AFunction();
    ptr2->BFunction();
    ptr2->CFunction();

    delete ptr2;

    ParentClass* ptr3 = new ParentClass();

    ptr3->AFunction();
    ptr3->BFunction();
    ptr3->CFunction();

    delete ptr3;

    return 0;
}

///// output
ParentClass Constructor
ChildClass Constructor
Child AFunction
Child BFunction
Parent CFunction
ChildClass Destructor
ParentClass Destructor
ParentClass Constructor
ChildClass Constructor
Child AFunction
Child BFunction
Child CFunction
ChildClass Destructor
ParentClass Destructor
ParentClass Constructor
Parent AFunction
Parent BFunction
Parent CFunction
ParentClass Destructor
```