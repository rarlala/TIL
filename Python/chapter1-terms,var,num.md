## 용어, 변수, 숫자

### 1. 용어

#### **1) 리터럴**

변하지 않는 고정된 데이터 자체의 표현을 말한다.

#### **2) 표현식(expression)**

값을 의미하는 표현 또는 값을 반환하는 표현을 말한다.

#### **3) 구문(statement)**

값의 의미를 지니지 않으며, 어떠한 목적을 수행하는 코드를 말한다.





### 2. 변수

파이썬은 인터프리터 언어이다.

파이썬은 모든 것이 객체로 이루어져있다. 객체는 데이터의 형태를 결정해주는 타입으로, 파이썬에서는 객체의 타입을 바꿀 수 없다.

파이썬도 가비지 컬렉터가 있어서 아무것도 참조하고 있지 않으면 삭제되게 된다. reference count가 0인 애를 찾아서 지워버린다.



#### 변수

프로그래머는 변수를 선언하고 사용하는 형태로 컴퓨터 메모리에 값을 할당하고 참조할 수 있다.



// 실습 코드

```python
var1 = 100
print(var1) // 100
```



변수는 단지 이름일 뿐이며, 그 자체가 어떠한 값을 갖는 것이 아니다. 변수는 데이터를 직접 가지는 것이 아니며, 100이라는 정수형 객체가 있고 a는 단순히 해당 객체를 참조하는 역할을 한다.



// 실습코드

```python
var2 = var1
id(var1) // 4520513888
id(var2) // 4520513888

id(var1)==id(var2) // True

var1 =101
id(Var1) // 4520513920
id(var2) // 4520513888

id(var1)==id(var2) // False
```



id 내장함수는 변수가 참조하고 있는 객체가 메모리 상에서 가지고 있는 고유의 주소를 출력한다. (여기서 보여주는 id 값은 실제 메모리주소는 아니고 실제 메모리주소와 매핑되는 값이긴 하다.)

id 함수를 통해 같은 값을 할당해준 변수는 같은 객체를 가리키는 것을 확인할 수 있다.

단, 변수는 언제든 다른 객체를 가리킬 수 있다.



#### 변수의 타입확인

내장함수 type을 사용한다.

```python
type(var1) // int or <class 'int'>
```

- int : 정수형 변수
- float : 실수형 변수
- str : 문자열

> 출력에서 class는 객체의 타입을 나타낸다.



#### 변수의 이름 제한

사용가능한 문자 : 대·소문자, 숫자, 언더스코어(_)

<u>숫자로 시작 불가능, 예약어 사용 불가</u>



#### 변수의 입력과 출력

입력은 내장함수 input/ 출력은 내장함수 print를 사용함

```python
var = input()
var = input('숫자를 입력해주세요 : ')
print(var)
```



### 3. 숫자

#### 수학 연산자

+, -, *, /, **//**, %, **

> 나누기와 정수나누기, 정수나누기는 소수점 부분을 버림



#### 산술 연산자 결합

예시) <code>a -= 3</code>



#### 우선 순위

우선순위 규칙이 적용된다. 가독성에 문제가 있을 경우 괄호로 묶어준다.



#### 진수(base)

- 2진수(binary) :  0b, 0B로 시작
- 8진수(octal) : 0o, 0O로 시작
- 16진수(hex) : 0x, 0X로 시작



#### 형변환

내장함수 int, float를 사용한다.



---

기타 추가 정보

- PEP8을 통해 파이썬 컨벤션을 볼 수 있다.