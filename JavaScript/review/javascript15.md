# 프로퍼티 정의



## 1. 프로퍼티 정의

- **프로퍼티 정의**란 **프로퍼티 어트리뷰트의 값을 정의**하여 **프로퍼티의 상태를 관리하는 것**을 말한다.
- 프로퍼티 상태란 프로퍼티의 값 (value), 값의 갱신 가능 여부 (writable), 열거 가능 여부 (enumerable), 재정의 가능 여부 (configurable)를 말함
- 자바스크립트 엔진은 프로퍼티를 생성(객체 리터럴의 평가, 프로퍼티 동적 생성)할 때 프로퍼티의 상태를 나타내는 [프로퍼티 어트리뷰트를 기본값](#<code>Object.getOwnPropertyDescriptor</code>기본값)으로 자동 정의한다. 

+ **정의된 프로퍼티 어트리뷰트**는 <code>Object.getOwnPropertyDescriptor</code> 메소드로 **확인**할 수 있다. 이 메소드는 프로퍼티 어트리뷰트 정보를 제공하는 객체인 **프로퍼티 디스크립터를 반환**한다. (단, 존재하지 않는 프로퍼티나 상속받은 프로퍼티 디스크립터를 요구하면 undefined가 반환됨)
+ <code>Object.defineProperty</code> 메소드를 사용하면 프로퍼티의 어트리뷰트를 정의할 수 있다.



> 참고하기 : getOwnPropertyDescriptor vs. getOwnPropertyDescriptors
>
> Object.getOwnPropertyDescriptor(obj, 'prop');
>
> **하나의 프로퍼티 어트리뷰트를 표현**하는 프로퍼티 디스크립터 객체 취득
>
> Object.getOwnPropertyDescriptors(obj, 'prop');
>
> **모든 프로퍼티 어트리뷰트를 표현**하는 프로퍼티 디스크립터 객체 취득



> **추상화**
>
> 여러 속성이 있지만 내가 관심이 있는 몇가지만 선택해 만드는 것이다.



## 2. 내부 슬롯(Internal slot) / 내부 메소드(Internal method)

- 내부 슬롯과 내부 메소드는 ECMAScript 스펙에서 요구하는 객체와 관련된 내부 상태와 내부 동작을 정의한 것이다. (ECMAScript 스펙에 등장하는 이중대괄호 [[...]]로 감싼 이름들이 이에 해당된다.)

- 내부 슬롯과 내부 메소드는 자바스크립트 엔진의 내부 구현 사양(자바스크립트 엔진이 코드를 실행하는 알고리즘)을 설명하기 위해 의사 프로퍼티와 의사 메소드이다. 내부 슬롯 [[Value]] , 내부 메소드 [[Get]]

- **내부 슬롯과 내부 메소드는 객체의 프로퍼티가 아니고, 자바스크립트의 프로퍼티이다.**

- 내부 슬롯과 내부 메소드는 직접적으로 접근하거나 호출할 수 있는 방법을 원칙적으로는 제공하지 않는다. 단, 일부 내부 슬롯과 내부 메소드에 간접적으로 접근할 수 있는 수단은 있다.

  

>  **알아두기!**
>
> get과 set은 fullName()으로 각각 별도로 프로퍼티를 적어줘도 하나의 프로퍼티로 판단해야한다. fullName()내 get 내부 메소드에 저장되고 set 내부 메소드에 저장되기 때문이다.



> **프로토타입과 프로토타입 체인**
>
> - 프로토 타입
>   - 어떤 객체의 상위(부모) 객체의 역할을 하는 객체이다.
>   - 프로토타입은 하위 객체에게 자신의 프로퍼티와 메소드를 상속한다.
> - 프로토타입 체인
>   - 프로토타입이 단방향 링크드 리스트 형태로 연결되어있는 상속 구조를 말한다.



> **[[Get]] 내부 메소드 동작원리**
>
> 1. 프로퍼티 키가 유효한지 확인 ▷
>
> 2. 프로토타입 체인에서 프로퍼티 검색 ▷
>
> 3. 검색된 프로퍼티가 데이터 프로퍼티라면 프로퍼티 값을 그대로 반환 // 접근자 프로퍼티라면 접근자 프로퍼티 어트리뷰트의 [[Get]]의 값, getter 함수를 호출하여 그 결과를 반환한다.



## 3. 접근자 프로퍼티

프로퍼티는 데이터 프로퍼티와 접근자 프로퍼티로 구분할 수 있다.

- 데이터 프로퍼티(Data property)
  
  - 키와 값으로 구성된 일반적인 프로퍼티
  - ex) 함수 객체의 `prototype`
  
  
- 접근자 프로퍼티(Accessor property)
  - **자체적으로는 값(value)을 갖지 않고** 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 **접근자 함수(getter/setter 함수)로 구성된 프로퍼티**
  - get, set, enumerable, configurable 프로퍼티 어트리뷰트를 갖는다
- ex) 일반 객체의 `__proto__`
  
  
  

## 4. 프로퍼티 어트리뷰트

- 모든 프로퍼티, 즉 데이터 프로퍼티와 접근자 프로퍼티는 자신의 상태와 동작을 정의한 내부 슬롯/메소드를 가지고 있다. 이들을 프로퍼티 어트리뷰트라 한다.
- 접근자 프로퍼티와 데이터 프로퍼티의 프로퍼티 디스크립터 객체의 프로퍼티가 다르다. value 프로퍼티가 있으면 데이터 프로퍼티다.



**[데이터 프로퍼티가 갖는 프로퍼티 어트리뷰트]**

| 프로퍼티 어트리뷰트 | 설명                                                    | false 인 경우([[Value]] 미해당)                              | 프로퍼티 디스크립터 객체의 프로퍼티 |
| :-----------------: | :------------------------------------------------------ | :----------------------------------------------------------- | :---------------------------------: |
|      [[Value]]      | 값에 접근, 저장                                         | -                                                            |                value                |
|    [[Writeable]]    | 값의 변경 가능 여부를 나타내며 불리언 값을 가짐         | 해당 프로퍼티의 [[Value]] 값 변경 불가                       |              writeable              |
|   [[Enumerable]]    | 프로퍼티 열거 가능 여부를 나타내며 불리언 값을 가짐     | 해당 프로퍼티는 for...in 문이나 Object.keys 메소드 등으로 열거 불가하다. |             enumerable              |
|  [[Configurable]]   | 프로퍼티의 재정의 가능 여부를 나타내며 불리언 값을 가짐 | false인 경우, 해당 프로퍼티 삭제, 프로퍼티 어트리뷰트의 값의 변경이 금지된다. |            configurable             |

[[Value]] 추가설명

- 프로퍼티 키로 프로퍼티 값에 접근하면 내부 메소드 [[Get]]에 의해 반환되는 값, 프로퍼티 키로 프로퍼티 값을 저장하면 [[Value]]에 값을 저장한다. 이때 프로퍼티가 없으면 프로퍼티를 생성 후 생성된 프로퍼티의 [[Value]]에 값을 저장한다.



**[접근자 프로퍼티가 갖는 프로퍼티 어트리뷰트]**

| 프로퍼티 어트리뷰트 | 설명                                                         | 프로퍼티 디스크립터 객체의 프로퍼티 |
| ------------------- | ------------------------------------------------------------ | ----------------------------------- |
| [[Get]]             | 접근자 프로퍼티를 통해 데이터 프로퍼티의 값을 읽을 때 호출되는 접근자 함수이다. getter 함수가 호출되고 그 결과가 프로퍼티 값으로 반환된다. | get                                 |
| [[Set]]             | 접근자 프로퍼티를 통해 데이터 프로퍼티의 **값을 저장할 때 호출되는 접근자 함수**이다. setter 함수가 호출되고 그 결과가 프로퍼티 값으로 저장된다. | set                                 |
| [[Enumerable]]      | 데이터 프로퍼티의 [[Enumerable]]와 같다.                     | enumerable                          |
| [[Configurable]]    | 데이터 프로퍼티의 [[Configurable]]와 같다.                   | configurable                        |

- 다른 데이터 프로퍼티의 값을 조작하기 때문에 접근자 프로퍼티는 함수이다.



###### <code>Object.getOwnPropertyDescriptor</code>기본값

| 프로퍼티 디스크립터 객체의 프로퍼티 | 대응하는 프로퍼티 어트리뷰트 | 디스크립터 객체의 프로퍼티 누락 시 기본값 |
| ----------------------------------- | ---------------------------- | ----------------------------------------- |
| value                               | [[Value]]                    | undefined                                 |
| get                                 | [[Get]]                      | undefined                                 |
| set                                 | [[Set]]                      | undefined                                 |
| writable                            | [[Writable]]                 | false                                     |
| enumerable                          | [[Enumerable]]               | false                                     |
| configurable                        | [[Configurable]]             | false                                     |

-  Object.defineProperty 메소드로 프로퍼티 정의할 때 프로퍼티 디스크립터 객체의 프로퍼티를 일부 누락할 수 있다 
- Object.defineProperty 메소드는 한번에 하나의 프로퍼티만 정의가능
- Object.defineProperties 메소드를 사용하면 여러 개의 프로퍼티 한번에 정의가능

---

[기타]

- 의사 프로퍼티, 의사 메소드 > sudo code로 설명하고 있음을 말함



[문의]

- 추후 예시들을 다시 읽어보고 설명 및 예시코드 정리하기