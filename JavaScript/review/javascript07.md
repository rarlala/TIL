# **제어문**

- 제어문은 주어진 조건에 따라 코드 블록을 실행하거나 반복 실행할 때 사용한다. 일반적으로 코드는 위에서 아래 방향으로 순차적으로 실행된다. 제어문을 사용하면 코드의 실행 흐름을 인위적으로 제어할 수 있다.

(단, 코드의 실행 순서가 변경된다는 것은 단순하게 위에서 아래로 순차적으로 진행하는 직관적은 코드의 흐름을 혼란스럽게 만든다.)



## 1. **블록문 (=코드 블록, 블록)**
- 블록문은 0개 이상의 문을 중괄호로 묶은 것이다.
- 자바스크립트는 블록문을 **하나의 실행 단위로 취급**한다.
- 블록문은 단독으로 사용할 수 있으나 일반적으로 제어문이나 함수 선언문 등에서 사용하는 것이 일반적이다.
- 블록문의 끝에는 세미콜론을 붙이지 않는다.

> 자바스크립트는 var로 변수를 선언하면 블록문이 스코프 역할을 하지 않음  단, let과 const는 스코프 역할을 함



## 2. **조건문**

- 정의: 불리언 값으로 평가될 수 있는 표현식으로 주어진 조건식의 평가 결과에 따라 코드 블럭의 실행을 결정한다.



### if...else 문 // if...else if...else 문

- 불리언 값으로 평가될 수 있는 표현식의 평과 결과로 실행할 코드 블럭을 결정한다.
- 평가 결과가 불리언 값이 아니면 불리언 값으로 **강제 변환**되어 논리적 참, 거짓을 구별한다.
- else문과 else if 문은 옵션으로 사용할 수도 사용하지 않을 수도 있다. if 문과 else문은 2번 이상 사용할 수 없지만 else if는 여러번 사용가능

```javascript
if(조건식1){  
  // 조건식1이 참일 때 실행  
} else if(조건식2){
  // 조건식2이 참이면 이 코드 블록이 실행
} else{  
  // 조건식이 거짓일 때 실행  
}
```



> **삼항 조건 연산자와 if...else문의 동일한 결과값을 도출하는 코드 비교**  
>
> 아래 코드를 살펴보면 간단한 if...else 문의 경우 삼항 조건 연산자를 활용해 코드를 작성하는 것이 더 간단함을 확인할 수 있다. 
>
> **둘의 차이는 삼항 연산자는 값으로 평가되는 표현식을 만든다. 따라서 변수에 할당할 수 있다. 하지만 if...else문은 표현식이 아닌 문이다. 따라서 변수에 할당할 수 없다.**

```javascript  
var x = 2;

// if문
var result;

if(x % 2){
  result = '홀수';
}else{
  result = '짝수';
}

// 삼항 조건 연산자
var result = x % 2 ? '홀수' : '짝수';
````



### switch 문

- 주어진 표현식을 평가하여 그 값과 일치하는 표현식을 갖는 case 문으로 실행 순서를 이동시킨다.
- 표현식과 일치하는 표현식을 갖는 case 문이 없다면 실행 순서는 default 문으로 이동하는데, 이것은 옵션이라 사용할 수도 사용하지 않을 수도 있다.

```javascipt
switch (표현식) {
  case 표현식1:
    표현식과 표현식1이 일치하면 실행;
    break;
  default:
    표현식과 일치하는 표현식을 갖는 case 문이 없을 때 실행;
}
```

> **폴스루(fall through)란?**  
>
> switch 문에서 표현식의 평가 결과가 일치하는 case 문으로 이동하여 실행되었지만, 문을 실행 후 탈출하지 않고 switch 문을 탈출하지 않고 switch이 끝날때까지 이후에 모든 case문과 default문을 실행한 것



## 3.**반복문 (loop)**

- 정의 : 주어진 조건식의 평가 결과가 참인 경우 코드 블럭을 실행한다. 그 후 조건식을 다시 검사하여 다시 실행하는데 이는 조건식이 거짓일 때까지 반복된다.

### for문

```javascript
for (변수 선언문 또는 할당문; 조건식; 증감식){
  조건식이 참인 경우 반복 실행
}
```
![for문실행순서](https://poiemaweb.com/assets/fs-images/7-1.png)
출처: https://poiemaweb.com/assets



### while문

- 조건식의 평가 결과가 참이면 코드블록을 계속해서 반복 실행하는데, 조건문의 평가 결과가 거짓이 되면 실행을 종료한다.
- 조건식의 평가 결과가 불리언 값이 아니면 불리언 값으로 **강제 변환**되어 논리적 참, 거짓을 구별한다.
- 무한루프를 탈출하기 위해 코드 블럭 내에 if문으로 탈출 조건을 만들고 break 문으로 코드 블럭을 탈출한다.

```javascipt
  while(조건식){
    조건식이 참이면 실행할 문
  }
```



### do...while문

- 코드 블록을 먼저 실행하고 조건식을 평가한다. 따라서 코드 블록은 무조건 한번 이상 실행된다.
```javascript
var count =  0;
do {
  console.log(count);
  count ++;
} while (count < 3);
```



## 4. **break 문**

- break문은 레이블 문, 반복문(for, for…in, for…of, while, do…while) 또는 switch 문의 코드 블록을 탈출한다.
- 중첩된 for 문의 내부 for 문에서 break 문을 실행하면 내부 for문을 탈출하여 외부 for문으로 진입한다. 이때 외부 for문을 탈출하려면 레이블 문을 사용한다.



> **레이블문**  
>
> 식별자가 붙은 문을 말하며, 프로그램의 실행 순서를 제어하기 위해 사용된다. 하지만 레이블문을 사용하면 프로그램의 흐름이 복잡해져 가독성이 나빠지고 오류를 발생시킬 가능성이 높아지기 때문에 사용을 권장하지 않는다.



## **5. continue 문**

- continue문은 반복문의 코드 블록 실행을 현 시점에서 중단하고 반복문의 증감식으로 이동한다.
- 팁: if문 내에서 실행해야 할 코드가 한 줄이라면 continue 문을 사용했을 때보다 간편하며 가독성도 좋지만, if문 내에서 실행해야 할 코드가 길다면 들여쓰기가 한 단계 더 깊어지므로 continue문을 사용하는 것이 가독성이 더 좋다.


> 마지막 단락 이해가 어려움  
위와 같이 if 문 내에서 실행해야 할 코드가 한 줄이라면 continue 문을 사용했을 때보다 간편하며 가독성도 좋다. 하지만 if 문 내에서 실행해야 할 코드가 길다면 들여쓰기가 한 단계 더 깊어지므로 continue 문을 사용하는 것이 가독성이 더 좋다.
