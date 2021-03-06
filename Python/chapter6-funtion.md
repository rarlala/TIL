## 함수

반복적인 작업을 하는 코드를 재사용이 가능하게 정의해 놓은 것

(def = define)

```python
def 함수명(매개변수[parameters]):
    동작
```

함수도 메모리에 들어가면 객체이다.

우리가 정의한 함수는 빌트인 함수와 달리 정의된 위치가 나온다.



#### 함수의 정의, 실행

```python
# 실행 시 'call funtion!'을 print하는 함수 정의
def call_function():
    print('call function!')
     
# 함수 자체는 function객체를 참조하는 변수
call_function // <function __main__.call_function()>

# 함수를 실행시키기 위해 ()를 사용한다.
```



#### 리턴값이 있는 함수 정의

```python
def return_true():
    return True

return_true()
True
```

함수의 결과로 Bool 값을 갖는 데이터를 리턴하여 if문이나 while문의 조건으로 사용 가능하다.



#### 매개변수를 사용하는 함수 정의

```python
def print_argument(something):
    print(something)
```



#### 매개변수(parameter)와 인자(argument)의 차이

매개변수란 함수 내부에서 함수에게 전달되어 온 변수를 말한다. 함수를 호출할 때 전달하는 변수는 인자로 부른다.

```python
# 함수 정의때는 매개변수
def func(매개변수1, 매개변수2):
    
# 함수 호출 시에는 인자
func(인자1, 인자2)
```



#### 리턴값이 없는 경우

함수에서 리턴하는 값이 없을 경우 아무것도 없다는 뜻을 가진 None 객체를 반환한다.



#### 위치 인자(Positional arguments)와 키워드 인자(Keyword arguments)

위치인자와 키워드인자를 동시에 쓴다면, 위치인자가 먼저 와야 한다.

```python
def student(name, age, gender):
	return {'name': name, 'age': age, 'gender': gender}

# 위치 인자(Positional arguments)
# 매개변수의 순서대로 인자를 전달하여 사용하는 경우
student('hanyeong.lee', 31, 'male')

# 키워드 인자(Keyword arguments)
# 매개변수의 이름을 지정하여 인자로 전달하여 사용하는 경우
student(age=31, name='hanyeong.lee', gender='male')
```



#### 기본 매개변수값 지정

인자가 제공되지 않을 경우, 기본 매개변수로 사용할 값을 지정할 수 있다.

```python
def student(name,age, gender='male'):
    return('name': name, 'age':age, 'gender':gender)

student('hanyeong.lee', 31)
```



#### 기본 매개변수값의 정의 시점

기본 매개변수값은 함수가 실행될 때 마다 계산되지 않고, 함수가 정의되는 시점에 계산되어 계속해서 사용된다.

```python
def return_list(value, result=[]):

    result.append(value)
    return result

return_list(apple) // ['apple']
return_list(melon) // ['apple', 'melon']
```

함수가 실행되는 시점에 기본 매개변수값을 계산하기 위해, 아래와 같이 바꿔준다.

```python
def return_list(value, result=None):
    if result is None:
        result = []
	result.append(value)
    return result

return_list(apple) // ['apple']
return_list(melon) // ['melon']
```



#### 위치인자 묶음

함수에 위치인자로 주어진 변수들의 묶음은 매개변수명으로 사용할 수 있다.

관용적으로 ***args**를 사용한다.

출력은 튜플형태로 정의되어 출력된다.

```python
def print_args(*args):
    print(args)
    
print_args(1,2,3,4,'a') // (1,2,3,4,'a') 
```



#### 키워드인자 묶음

함수에 키워드인자로 주어진 변수들의 묶음은 매개변수명으로 사용할 수 있다. 관용적으로**<code>* *kwargs</code>**를 사용한다.

```python
def print_kwargs(**kwargs):
    print(kwargs)
    
print_kwargs(a='apple', b='banana') // {'a': 'apple', 'b': 'banana'}
```



##### 위치인자 묶음과 키워드인자 묶음을 함께 쓸 수 있다.

```python
def print_all_args(*args, **kwargs):
    print(args)
    print(kwargs)

print_all_args(2,3,4,'a', apple='사과') // (2, 3, 4, 'a') {'apple': '사과'}
```



#### docstring

함수를 정의한 문서 역할을 한다. 함수 정의 후 몸체의 시작부분에 문자열로 작성하며, 여러줄로도 작성 가능하다.

```python
def print_args(*args):
... 'Print positional arguments'
    print(args)
    
help(print_args)
```



#### 함수를 인자로 전달

파이썬에서는 함수 역시 다른 객체와 동등하게 취급되므로, 함수에서 인자로 함수를 전달, 실행, 리턴하는 형태로 프로그래밍이 가능하다.



실습) 'call func'를 출력하는 함수를 정의하고, 함수를 인자로 받아 실행하는 함수를 정의하여 첫 번째에 정의한 함수를 인자로 전달해 실행해보자. 

```python
def call_func():
    print('call func')
    
def sam(f):
    f()
    
sam(call_func) // call func
```



#### 내부 함수

함수 안에서 또 다른 함수의 정의해 사용할 수 있다.



실습) 문자열 인자를 하나 전달받는 함수를 만들고, 해당 함수 내부에 전달받은 문자열을 대문자화해서 리턴해주는 내부 함수를 구현한다. 문자열을 전달받는 함수는 내부함수를 실행한 결과를 리턴하도록 한다. 

```python
def string(str):
    def upper():
        return str.uppercase
    return upper()
```



#### 스코프(영역)

파이썬에서는 코드 작성 시, 각 함수마다 독립적인 스코프(영역)을 가진다.

메인 프로그램(현재 동작하는 프로그램의 최상위 위치)의 영역은 전역 영역(Global Scope라고 하며, 전역 스코프 내부에서 독립적인 영역을 갖고 있는 경우에는 지역 영역(Local scope)라고 부른다.

```python
# 자신의 스코프에서 찾지 못한 변수는, 자신보다 상위 스코프에서 찾는다
champion = 'Lux'

def show_global_champion():
    print(f'show_global_champion: {champion}')
    
show_global_champion() // show_global_champion: Lux
print(champion) // Lux
```

각 영역에 해당하는 데이터들은 `locals()`함수를 사용해 확인 할 수 있으며, 전역 영역의 데이터들은 `globals()`함수를 사용한다. 



#### 스코핑 롤

스코프는 지역(Local), 전역(Global)외에도 내장(Built-in)영역이 존재한다.

내장영역이 가장 바깥, 그 내부에 전역, 그 내부에 지역 순으로 정의된다.

분리된 영역에서, 외부 영역에서는 내부 영역의 데이터를 사용할 수 없지만 내부 영역에서는 자신의 외부 영역에 있는 데이터를 참조할 수 있다. (반대의 경우에는 함수의 인자로 데이터를 전달한다) 



#### 내장 함수와 내장 영역

`print`, `dict`등 지정하지 않고 사용했던 내장 함수들은 위 스코핑 룰의 내장 스코프에 존재하는 함수들이다.

전역스코프의 `__builtin__`변수에 할당되어 있으며, 전역 스코프에서는 해당 변수의 내부를 참조할 수 있도록 파이썬이 시작될 때 자동으로 처리된다.

확인시 `dir`함수를 사용하며, `dir`함수는 해당 객체가 사용 가능한 속성 및 함수들을 리스트 형태로 나타내준다.



#### 로컬 스코프에서 글로벌 스코프의 변수를 사용

```python
champion = 'Lux'

def change_global_champion():
    champion = 'Ahri'
    print('after change_global_champion : {}'.format(champion))

change_global_champion()
print('print global champion : {}'.format(champion))
```

이 경우, 위의 `show_global_champion`함수와는 다르게 `change_global_champion`함수는 `champion`변수에 새로운 값을 대입한다.

만약 로컬 스코프에서 글로벌 스코프의 변수를 변경해야 한다면, 해당 변수가 로컬 스코프에 생성되는 것이 아닌 글로벌 영역에 이미 존재하는 값을 사용함을 명시해주어야 한다.

```python
champion = 'Lux'

def change_global_champion():
    global champion
    champion = 'Ahri'
    print('after change_global_champion : {}'.format(champion))

change_global_champion()
print('print global champion : {}'.format(champion))
```

파이썬에서는 한 스코프에서 동일한 이름을 가진 두 스코프의 변수를 사용할 수 없음을 기억해야 한다.



#### 내부함수에서의 로컬 스코프 (nonlocal)

```python
champion = 'Lux'

def local1():
    champion = 'Ahri'
    print('local1 locals() : {}'.format(locals()))

    def local2():
        champion = 'Ezreal'
        print('local2 locals() : {}'.format(locals()))
    local2()

print('global locals() : {}'.format(locals()))
local1()
```

로컬 스코프 내부에는 또 다른 로컬 스코프가 존재할 수 있다.

전역 스코프가 아닌, 자신의 바로 바깥 영역의 로컬 스코프(자신보다 한 단계 위의 로컬 스코프)의 데이터를 참조하고자 한다면, `nonlocal`키워드를 사용한다.

```python
champion = 'Lux'

def local1():
    champion = 'Ahri'
    print('local1 locals() : {}'.format(locals()))

    def local2():
        nonlocal champion
        champion = 'Ezreal'
        print('local2 locals() : {}'.format(locals()))
    local2()
    print('local1 locals() : {}'.format(locals()))

print('global locals() : {}'.format(locals()))
local1()
```



#### global키워드와 인자(argument)전달의 차이

##### 인자로 전달한 경우

```python
global_level = 100
def level_add(value):
    value += 30
    print(value)

level_add(global_level)
print(global_level)
```



##### global키워드를 사용한 경우

```python
global_level = 100
def level_add():
    global global_level
    global_level += 30
    print(global_level)

level_add()
print(global_level)
```

인자로 전달한 경우, 같은 객체를 가리키는 글로벌 변수 `global_level`과 매개변수 `value`가 존재한다.
이 때, 매개변수인 `value`의 값을 변경하는 것은 `global_level`에는 영향을 주지 않는다.

global키워드의 경우 둘은 같은 변수이다.

하지만 리스트 변수가 전달된다면? global keyword를 사용하지 않아도 바뀐다. 

```python
global_list = []

def add_item(target_list)
	target_list.append('ASDF')
    
print(global_list)
add_item(global_list)
print(global_list)
```



#### 람다함수

한 줄 짜리 표현식으로 이루어지며, 반복문이나 조건문 등의 제어문은 포함될 수 없다.

또한, 함수이지만 정의/호출이 나누어져 있지 않으며 표현식 자체가 바로 호출된다.

람다함수는 이름이 없다.

```python
lambda <매개변수> : <표현식>
```

```python
# 함수의 정의
>>> def multi(x):
...   return x*x
... 

# 함수의 호출
>>> multi(5)
25

# 람다함수의 사용
>>> (lambda x : x*x)(5)
25

# 람다함수를 사용해 함수 정의
>>> f = lambda x : x*x
>>> f(5)
25
```



#### 람다함수의 사용

```python
import string
>>> for char in string.ascii_lowercase:
...   if char > 'i':
...     print(char.upper())
...   else:
...     print(char)
>>> for char in string.ascii_lowercase:
...   print((lambda x : x.upper() if x > 'i' else x)(char))
```

```python
>>> for char in string.ascii_lowercase:
...   print((lambda x : x.upper() if x > 'i' else x)(char))
```



#### 클로져 (Closure)

함수가 정의된 환경을 말하며, 파이썬 파일이 여러개일 경우 각 파일은 하나의 `모듈`역할을 하고, 각 `모듈`은 독립적인 환경을 가진다.

독립된 환경은 각자의 영역을 전역 영역으로 사용한다.

**closure/module_a.py**

```
level = 100
def print_level():
    print(level)
```

**closure/module_b.py**

```
import module_a
level = 50
def print_level():
    print(level)
    
module_a.print_level()
print_level()
```

`python module_b.py`로 `module_b`를 실행한다.

함수의 전역 영역은 해당 함수가 정의된 모듈의 전역 영역으로, 전역변수는 모듈의 영역에 영향을 받는다.



#### 내부함수의 클로져

```
>>> level = 0
>>> def outer():
...   level = 50
...   def inner():
...     nonlocal level
...     level += 3
...     print(level)
...   return inner
...
>>> f = outer()
```

위의 경우, `outer`함수는 `inner`함수를 반환하여 결과를 `f`전역변수에 할당한다.
`inner`함수의 호출 결과가 아닌 함수 자체를 반환하기 때문에, `f`변수는 실행할 수 있는 함수객체이다.
이 때 `inner`함수가 사용하는 `level`변수는 `nonlocal`키워드를 사용했기 때문에 `outer`에 새로 정의된 지역변수 `level`을 사용한다.

반복적으로 `f`함수를 실행하면 `inner`의 외부(`outer`)에 존재하는 `level`변수의 값을 증가시키고 `print`시키기 때문에, 값은 계속해서 증가한다.



#### 데코레이터 (decorator)

함수를 받아 다른 함수를 반환하는 함수. 예를 들면, 기존에 존재하던 함수를 바꾸지 않고 전달된 인자를 보기위한 디버깅 `print`함수를 추가한다던가 하는 기능을 할 수 있다.

1. 기능을 추가할 함수를 인자로 받음
2. 데코레이터 자체에 추가할 기능을 함수로 정의
3. 인자로 받은 함수를 데코레이터 내부에서 적절히 호출
4. 위 2가지를 행하는 내부 함수를 반환



아래와 같이 함수 2개를 만들고, 새 구문을 추가해본다.

- 인자로 문자열 변수를 받아 출력해주는 함수 `print_string`구현
- 인자로 정수형 변수를 받아 출력해주는 함수 `print_int`구현
- 각 함수에 대해 자신이 전달받은 인자에 대한 `type`을 출력하는 구문을 추가

```python
def print_string(value):
    print('print_string call')
    print(value)
    
def print_int(value):
    print('print_int call')
    print(value)
```



여러 함수에 대해 같은 기능을 추가할 경우, 데코레이터를 사용한다.

```python
def print_debug(f):
    def inner_function(*args, **kwargs):
        print('args :', args)
        print('kwargs :', kwargs)
        result = f(*args, **kwargs)
        return result
    return inner_function
```



데코레이터함수(인자로 전달할 함수) 형태로도 사용이 가능하지만, 데코레이터를 사용할 경우에는 함수 위에 데코레이터를 추가해서 사용가능하다.

```python
@print_debug
def any_function():
    pass
```

또한 데코레이터는 여러개를 가질 수 있으며, 함수에서 가장 가까운 것 부터 실행한다.

- print_debug이후 결과를 제곱하고 print해주는 데코레이터를 실행, 반대로 실행



#### 제네레이터 (generator)

제네레이터는 함수는 파이썬의 시퀀스 데이터를 생성하는데 사용된다.

실제 시퀀스 데이터와 다른 점은, 시퀀스 전체를 가지고 있는 것이 아니라 <u>시퀀스 데이터를 생성하기 위한 어떠한 루틴만을 가지고 있는 것</u>이다.



**장점**: 전체 크기만큼의 메모리를 가지고 있는 시퀀스 데이터와는 달리 <u>메모리를 적게 사용</u>할 수 있다.



**특징**

- 제네레이터는 마지막으로 호출한 위치(항목)를 기억하고 있으며, 한 번 순회할 때 마다 그 다음 값을 반환한다.

- 제네레이터는 함수를 통해서 만들어지며, 함수 내부의 반복문에서 `yield`키워드를 사용하면 제네레이터가 된다.

```python
>>> def range_gen(num):
...   i = 0
...   while i < num:
...     yield i
...     i += 1
...
>>> gen = range_gen(10)
>>> gen
<generator object range_gen at 0x10b682168>
>>> type(gen)
<class 'generator'>
>>> gen.__next__()
0
>>> gen.__next__()
1
>>> gen.__next__()
2
>>> gen.__next__()
3
>>> gen.__next__()
4
>>> gen.__next__()
5
>>> gen.__next__()
6
>>> gen.__next__()
7
>>> gen.__next__()
8
>>> gen.__next__()
9
>>> gen.__next__()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

함수 내부에 `yield`키워드가 사용되어 제네레이터 함수가 되었으며, 함수를 실행하면 제네레이터 객체를 반환한다.
`yield`부분에서 멈춘 제네레이터 객체를 순회하기 위해서는 `__next__()` 함수를 실행, 또는 next(gen) 해준다.



<code>for i in gen</code>과 같이 for ... in ... 문을 사용하는 경우는, 실행되면서 `__next__(gen)` 함수를 실행, 또는 `next(gen)`



## 실습

매개변수로 문자열을 받고, 해당 문자열이 `red`면 `apple`을, `yellow`면 `banana`를, `green`이면 `melon`을, 어떤 경우도 아닐 경우 `I don't know`를 리턴하는 함수를 정의하고, 사용하여 `result`변수에 결과를 할당하고 `print`해본다.

```python
def fruits(string):
    if(string == 'red'):
        return 'apple'
    elif(string == 'yellow'):
        return 'banana'
    elif(string == 'green'):
        return 'melon'
    else:
        return "I don't know"

result = fruits('red')
print(result)
```



다른 방법

```python
# 딕셔너리를 활용해 더 깔끔하게 짜는 방법
fruit = { 'red': 'apple', 'yellow': 'banana' }

def what_fruits(color: str) -> str:
    return fruit.get(color,"I don't know")

what_fruits('red')
```



위에서 작성한 함수에 `docstring`을 작성하여 함수에 대한 설명을 달아보고, `help(함수명)`으로 해당 설명을 출력해본다.

```python
def fruits(string):
    """
        입력한 문자열에 따라서 해당 색이 해당되는 과일 이름이 출력되는 함수입니다.
    """
    if(string == 'red'):
        return 'apple'
    elif(string == 'yellow'):
        return 'banana'
    elif(string == 'green'):
        return 'melon'
    else:
        return "I don't know"
    
help(fruits)
```



한 개 또는 두 개의 숫자 인자를 전달받아, 하나가 오면 제곱, 두개를 받으면 두 수의 곱을 반환해주는 함수를 정의하고 사용해본다.

```python
def math_arg(one, two = None):
   
    if (type(two) != 'int'):
        return f'{one * two}'
    
    elif (type(two) == 'None'):
        return f'{one * one}'
```



다른 코드

```python
def square_or_multi1(a, b=None):
    if b is None:
        return a ** 2
    else:
        return a * b
    
def square_or_multi2(a, b=None):
    if len(args2) > 2:
        raise TypeError('args length > 2')
        return
    elif len(arg2) == 2:
        return args[0] * args[1]
    elif len(arg2) == 1:
        return args[0] * args[1]
    else:
        print('args length == 0')
        return
```





두 개의 숫자를 인자로 받아 합과 차를 튜플을 이용해 동시에 반환하는 함수를 정의하고 사용해본다.

```python
def add_minus (a, b):
    result = (f'{a + b}'), (f'{a - b}')
    return result

add_minus(1,2)
```



다른코드

```python
def sum_sub(a, b):
    return (a + b), abs(a - b)

sum_sub(5,3)
```



또 다른 코드

```python
def sum_sub(a, b):
    large, small = (a, b) if a > b else (b, a)
    return (large + small), (large - small)

sum_sub(5,3)
```





위치인자 묶음을 매개변수로 가지며, 위치인자가 몇 개 전달되었는지를 print하고 개수를 리턴해주는 함수를 정의하고 사용해본다.

```python
def haveargs (*args):
    result = args
    print (result)
    print(len(result))
    
haveargs(1,2,3,4)
```



다른 코드

```python
def print_and_return_args_length(*args):
    len_args = len(args)
    print(f'주어진 위치 인자의 개수는 {}개 입니다.')
    return len(args)
```



람다함수와 리스트 컴프리헨션을 사용해 한 줄로 구구단의 결과를 갖는 리스트를 생성해본다.

```python
# 리스트 컴프리헨션 [표현식 for 항목 in iterable객체]
# 람다함수 lambda <매개변수> : <표현식>

results = []
for i in range(2,10):
    for j in range(1,10):
        cur_result = (lambda x, y: f'{x} x {y} = {x * y}')(i, j)
        result.append = cur_result
        #result.append(f'{x} x {y} = {x * y}')(i, j)')
        
results
```



다른 코드

```python
results = [(lambda x,y: f'{x} x {y} = {x * y}')(i, j) for i in range(2, 10) for j in range(1,10)]
```





## 알고리즘

- 순차검색(Sequential Search)
  1. 문자열과 키 문자 1개를 받는 함수 구현
  2. while문을 이용, 문자열에서 키 문자가 존재하는 index위치를 검사 후 해당 index를 리턴
  3. 찾지 못했을 경우 -1을 리턴

```python
def sequential_search(string, key):
    """
    주어진 string에서 key에 해당하는 문자(1개)가 몇 번째 index에 존재하는지를 리턴
    존재하지 않는다면 -1을 리턴한다
    """
    print('string:', string)
    print('key:', key)
    
    for index, char in enumerate(string):
        print(index, char, char == key)
        if char == key:
            return index
    return -1
```

```python
sequential_search('asd','s')
#return 2
```



- 선택정렬(Selection sort)
  - [9, 1, 6, 8, 4, 3, 2, 0, 5, 7] 를 정렬한다.
  - 정렬과정
    1. 리스트 중 최소값을 검색
    2. 그 값을 맨 앞의 값과 교체
    3. 나머지 리스트에서 위의 과정을 반복
  - 해결방법
    1. 알고리즘 진행과정 그려보기
    2. 의사코드(Psuedo code) 작성
    3. 실제 코드 작성



```python
sample = [9, 1, 6, 8, 4, 3, 2, 0, 5, 7]
sample_length = len(sample)
print('sample length:', sample_length)

for i in range(sample_length - 1):
    # 현재 리스트의 상태를 보여줌
    print(i, sample)
    
    # i번째 정렬 loop에서 가장 작은 값을
    # i번째 Index의 원소와 교환해준다
    min_value = sample[i]
    min_value_index = i
    print('초기 min_value:', min_value)
    for j in range(1 + i, 10):
        if min_value > sample[j]:
            min_value = sample[j]
            min_value_index = j
	print('이 loop에서 min_value:', min_value)
    print('이 loop에서 min_value_index:', min_value_index)
    
    # 가장 작은 값과, 그 작은 값의 index를 찾음 (min_value_index)
    # i번째 원소와 min_value_index의 원소를 치환
    sample[i], sample[min_value_index] = sample[min_value_index], sample[i]
    
    print()
    
print('결과: ', sample)
```



다른 코드

![image-20191203084733733](C:\Users\정수지\AppData\Roaming\Typora\typora-user-images\image-20191203084733733.png)

