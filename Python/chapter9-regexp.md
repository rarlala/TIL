## 정규표현식 (Regular Expressions)

특정한 패턴에 일치하는 복잡한 문자열을 처리할 때 사용하는 기법.

파이썬에서는 표준 모듈 `re`를 사용해서 정규표현식을 사용할 수 있다.

```python
import re
result = re.match('Lux', 'Lux, the Lady of Luminosity')
```

위에서 `match`의 첫 번째 인자에는 패턴이 들어가며, 두 번째 인자에는 문자열 소스가 들어간다. `match()`는 소스와 패턴의 일치 여부를 확인하고, 일치할 경우 `Match object`를 반환한다.

조금 복잡하거나 자주 사용되는 패턴은 미리 **컴파일**하여 속도를 향상시킬 수 있다.

```python
pattern1 = re.compile('Lux')
```

컴파일된 패턴객체를 문자열 대신 첫 번째 인자로 사용 가능하다.



### match : 시작부터 일치하는 패턴 찾기

```python
>>> import re
>>> source = 'Lux, the Lady of Luminosity'
>>> m = re.match('Lux', source)
>>> if m:
...   print(m.group())
...
Lux
```

`match()`는 시작부분부터 일치하는 패턴만 찾기 때문에, `Lady`라는 패턴으로는 찾을 수 없다.

```python
>>> m = re.match('.*Lady', source)
>>> if m:
...   print(m.group())
...
Lux, the Lady
```

위 결과는 다음을 의미한다.

- `.`은 문자 1개를 의미
- `*`는 해당 패턴이 0회 이상 올 수 있다는 의미이다
- 따라서 `.*Lady`는 앞에 아무 문자열(또는 빈) 이후 Lady로 끝나는 패턴을 의미한다



### search : 첫 번째 일치하는 패턴 찾기

`*`패턴 없이 `Lady`만 찾을 경우, 문자열 전체에서 일치하는 부분을 찾는 `search()`를 사용한다.

```python
>>> m = re.search('Lady', source)
>>> if m:
...   print(m.group())
...
Lady
```



### findall : 일치하는 모든 패턴 찾기

```python
>>> m = re.findall('y', source)
>>> m
>>> ['y', 'y']
>>> m = re.findall('y..', source)
>>> m
['y o']
```

끝자리의 `y`는 뒤에 문자가 더 없으므로 포함되지 않으므로, `?`를 추가한다.
`.`은 문자 1개를 의미하며, `?`는 0 또는 1회 반복을 사용한다. `.?`은 문자가 0 또는 1회 올 수 있음을 의미한다.

```python
>>> m = re.findall('y.?.?', source)
>>> m
['y o', 'y']
```



### split : 패턴으로 나누기

문자열의 `split()`메서드와 비슷하지만 패턴을 사용할 수 있다.

```python
>>> m = re.split('o', source)
>>> m
['Lux, the Lady ', 'f Lumin', 'sity']
```



### sub : 패턴 대체하기

문자열의 `replace()`메서드와 비슷하지만 패턴을 사용할 수 있다.

```python
>>> m = re.sub('o', '!', source)
>>> m
'Lux, the Lady !f Lumin!sity'
```



### 정규표현식의 패턴 문자

| 패턴 | 문자                       |
| ---- | -------------------------- |
| \d   | 숫자                       |
| \D   | 비숫자                     |
| \w   | 문자                       |
| \W   | 비문자                     |
| \s   | 공백 문자                  |
| \S   | 비공백 문자                |
| \b   | 단어 경계 (\w와 \W의 경계) |
| \B   | 비단어 경계                |

```python
>>> import string
>>> printable = string.printable
>>> re.findall('\w', printable)
>>> re.findall('\d', printable)
```



### 정규표현식의 패턴 지정자 (Pattern specifier)

**expr**은 정규표현식을 말한다

| 패턴            | 의미                                           |
| --------------- | ---------------------------------------------- |
| abc             | 리터럴 `abc`                                   |
| (expr)          | expr                                           |
| expr1 \| expr2  | expr1 또는 expr2                               |
| `.`             | `\n`을 제외한 모든 문자                        |
| `^`             | 소스문자열의 시작                              |
| `$`             | 소스문자열의 끝                                |
| expr`?`         | 0 또는 1회의 expr                              |
| expr`*`         | 0회 이상의 최대 expr                           |
| expr`*?`        | 0회 이상의 최소 expr                           |
| expr`+`         | 1회 이상의 최대 expr                           |
| expr`+?`        | 1회 이상의 최소 expr                           |
| expr`{m}`       | m회의 expr                                     |
| expr`{m,n}`     | m에서 n회의 최대 expr                          |
| expr`{m,n}?`    | m에서 n회의 최소 expr                          |
| [abc]           | a or b or c                                    |
| [^abc]          | not (a or b or c)                              |
| expr1(?=expr2)  | 뒤에 expr2가 오면 expr1에 해당하는 부분        |
| expr1(?!expr2)  | 뒤에 expr2가 오지 않으면 expr1에 해당하는 부분 |
| (?<=expr1)expr2 | 앞에 expr1이 오면 expr2에 해당하는 부분        |
| (?<!expr1)expr2 | 앞에 expr1이 오지 않으면 expr2에 해당하는 부분 |

[점프 투 파이썬 - 정규표현식 (https://wikidocs.net/4309)](https://wikidocs.net/4309)



> 스토리**
>
> Born to the prestigious Crownguards, the paragon family of Demacian service, Luxanna was destined for greatness. She grew up as the family's only daughter, and she immediately took to the advanced education and lavish parties required of families as high profile as the Crownguards. As Lux matured, it became clear that she was extraordinarily gifted. She could play tricks that made people believe they had seen things that did not actually exist. She could also hide in plain sight. Somehow, she was able to reverse engineer arcane magical spells after seeing them cast only once. She was hailed as a prodigy, drawing the affections of the Demacian government, military, and citizens alike.
>
> As one of the youngest women to be tested by the College of Magic, she was discovered to possess a unique command over the powers of light. The young Lux viewed this as a great gift, something for her to embrace and use in the name of good. Realizing her unique skills, the Demacian military recruited and trained her in covert operations. She quickly became renowned for her daring achievements; the most dangerous of which found her deep in the chambers of the Noxian High Command. She extracted valuable inside information about the Noxus-Ionian conflict, earning her great favor with Demacians and Ionians alike. However, reconnaissance and surveillance was not for her. A light of her people, Lux's true calling was the League of Legends, where she could follow in her brother's footsteps and unleash her gifts as an inspiration for all of Demacia.



```python
story = '''Born to the prestigious Crownguards, the paragon family of Demacian service, Luxanna was destined for greatness. She grew up as the family's only daughter, and she immediately took to the advanced education and lavish parties required of families as high profile as the Crownguards. As Lux matured, it became clear that she was extraordinarily gifted. She could play tricks that made people believe they had seen things that did not actually exist. She could also hide in plain sight. Somehow, she was able to reverse engineer arcane magical spells after seeing them cast only once. She was hailed as a prodigy, drawing the affections of the Demacian government, military, and citizens alike.

As one of the youngest women to be tested by the College of Magic, she was discovered to possess a unique command over the powers of light. The young Lux viewed this as a great gift, something for her to embrace and use in the name of good. Realizing her unique skills, the Demacian military recruited and trained her in covert operations. She quickly became renowned for her daring achievements; the most dangerous of which found her deep in the chambers of the Noxian High Command. She extracted valuable inside information about the Noxus-Ionian conflict, earning her great favor with Demacians and Ionians alike. However, reconnaissance and surveillance was not for her. A light of her people, Lux's true calling was the League of Legends, where she could follow in her brother's footsteps and unleash her gifts as an inspiration for all of Demacia.'''
```

위와같이 `story`변수에 스토리 문자열을 할당한다.

```python
re.findall('Lux', story)
re.findall('Lux|her|she', story)
re.findall('[Ll]ux|[Hh]er|[Ss]he', story)
re.findall('^Born', story)
re.findall('Demacia$', story)
re.findall('was', story)
re.findall('(?<=she) was', story)
re.findall('\w+(?<!she) was', story)
re.findall('\bwas\b', story)
re.findall(r'\bwas\b', story)
```

`\`로 시작하는 패턴 문자나, 정규표현식에서 `\`를 직접 사용해야 하는 경우 문자열의 **이스케이프**문을 사용하지 않고, 정규식 내에서 `\`로 해석됨을 나타내기 위해 앞에 `r`을 붙인다 (raw string으로 취급된다)

정규표현식의 패턴에는 항상 앞에 `r`을 붙인다고 생각하는 것이 좋다. (만약 정규표현식 내부에서 `\`를 쓰지 않을 경우, `r`을 붙임과 붙이지 않음은 같은 결과를 가져온다)



### 매칭 결과 그룹화

정규표현식 패턴중 괄호로 둘러싸인 부분이 있을 경우, 결과는 해당 괄호만의 그룹으로 저장된다.

Match객체의 `group()`함수는 매치된 전체 문자열을 리턴하며, `groups()`함수는 지정된 그룹 리스트를 리턴해준다.
`group(0)`은 `group()`과 같은 동작을 하며, `group(숫자)`는 매치된 `숫자`번째의 그룹요소를 리턴해준다.

```python
s = re.search(r'\w+\w(was)', story)
s.groups()
s.group(0)
s.group(1)
```

`(?Pexpr)` 패턴을 사용하면 매칭된 표현식 그룹에 이름을 붙여 사용할 수 있다.

```python
m = re.search(r'(?P<before>\w+)\s+(?P<was>was)\s+(?P<after>\w+)', story)
m.groups()
m.group('before')
m.group('was')
m.group('after')
```



### 최소일치와 최대일치

```python
<html><body><h1>HTML</h1></body></html>
```

위 항목을

```python
m = re.match(r'<.*>', html)
```

로 검색하면, `.*`표현식이 첫 번째 `>`에서 멈추는것이 아니라 맨 마지막 `>`까지 검색을 진행한다.
`*`이나 `+`에 **최소일치인 `?`를 붙여주면**, 표현식 다음부분에 해당하는 문자열이 **처음 나왔을 때 그 부분까지만 일치**시키고 검색을 마친다.



### 실습

아래 실습들은 위의 `story`변수 문자열을 소스로 사용한다.



`{m}`패턴지정자를 사용해서 `a`로 시작하는 4글자 단어를 전부 찾는다.

```python
re.findall(r'\ba\w{3}\b', story)
```



r로 끝나는 모든 단어를 찾는다.

```python
re.findall(r'\w+r\b', story)
```



a,b,c,d,e중 아무 문자나 3번 연속으로 들어간 단어를 찾는다.

> ex) b[eca]me

```python
re.findall('\b\w*[abcde]{3}\w*\b', story)
```



`re.sub`를 사용해서 `,`로 구분된 앞/뒤 단어에 대해 앞단어는 대문자화 시키고, 뒷단어는 대괄호로 감싼다. 이 과정에서, 각각의 앞/뒤에 `before`, `after`그룹 이름을 사용한다.

```python
p = re.compile(
    r'''(?P<before>\w+)      # 쉼표 이전의 단어
        (?P<center>\s*,\s*)  # 쉼표 이전 단어와 쉼표 이후 단어 사이
        (?P<after>\w+)       # 쉼표 다음 단어
    ''', re.VERBOSE)
# re.X

re.sub(p, r'\g<before>\g<center>[\g<after>]', story)

def replace_f(m):
    before = m.group('before').upper()
    center = m.group('center')
    after = f'[{m.group("after")}]'
    # after = '[{}]'.format(m.group('after'))
    # after = '[{after}]'.format(after=m.group('after'))

    result = f'{before}{center}{after}'
    return result

# 람다함수로 만들기
def replace_f(m):
    result = f'{m.group("before").upper()}{m.group("center")}[{m.group("after")}]'
    return result

replace_f = lambda m: f'{m.group("before").upper()}{m.group("center")}[{m.group("after")}]'

re.sub(
    p, 
    lambda m: f'{m.group("before").upper()}{m.group("center")}[{m.group("after")}]', 
    story,
)
```



### HTML과 정규표현식

> 기준이 되는 html
>
> ```html
> <li>
> 	<div class="thumb">
> 		<a href="/webtoon/list.nhn?titleId=119874&weekday=tue">
>         <img src="https://shared-comic.pstatic.net/thumb/webtoon/119874/thumbnail/title_thumbnail_20150706185233_t83x90.jpg" title="덴마" alt="덴마">
>         <span class="mask"></span>
> 		</a>
> 	</div>
> 
>     <a href="/webtoon/list.nhn?titleId=119874&weekday=tue" onclick="nclk_v2(event,'thm*t.tit','','4')" class="title" title="덴마">덴마
>     </a>
> </li>
> ```



```python
html = ''
with open('webtoon_list.html', 'rt') as f:
    html = f.read()
```

```python
import re

# 정규표현식을 사용해서 
# a여는태그 / 닫는태그 사이에 있는 텍스트를 가져오기
# ex) <a href="">ASDF</a> 에서 ASDF를 가져오기
sample = '''
<a href="asdf" class="title" title="유미의 세포들">유미의 세포들</a>
<a href="asdf" class="title" title="덴마">덴마</a>
'''

# a태그가 "title"이라는 class를 가진 경우의 정규표현식 사용

# 방법 1) findall을 사용해 제목 출력
result_list = re.findall(r'<a.*?class="title".*?>(?P<name>[\w\s]+)</a>', html)

# 방법 2) firniter를 사용해 제목 출력
m_list = re.finditer(r'<a.*?class="title".*?>(?P<name>[\w\s]+)</a>', html)
for m in m_list:
    print(m.group('name'))

# 1. 위 a태그가 가진 "href"속성의 값을 가져와보기
result_list = re.findall(r'<a.*?href="(.*?)".*?class="title".*?>(?P<name>[\w\s]+)</a>', html)

# 2. 위 a태그가 가진 "href"속성 내의 "titleId" GET parameter값 
#   (유미의 세포들의 경우, 651673)을 가져와보기

# 방법1) findall을 사용해 titleId 출력
result_list = re.findall(r'<a.*?href="(.*?titleId=(\d+).*?)".*?class="title".*?>(?P<name>[\w\s]+)</a>', html)

# 방법2) finditer를 사용해 titleId 출력
m_list = re.finditer(r'<a.*?href="(.*?titleId=(\d+).*?)".*?class="title".*?>(?P<name>[\w\s]+)</a>', html)

for m in m_list
	print m.group(1) # 링크 출력
	titleId_match = re.search(r'\?titleId=(\d+)', m.group(1))
    title_id = titleId_match.group(1)
    print(title_id) # titleId 출력
    print(m.group(3))

# 3. 전체 웹툰 목록에 중복내역이 표시되는데, 이를 없앤 리스트를 만들어보기
webtoon_list = re.findall(r'<a.*?class="title".*?>(?P<name>[\w\s]+)</a>', html)

print(len(webtoon_list))

# set으로 중복을 없애고 다시 list로 변환 (중복삭제)
webtoon_list = list(set(webtoon_list))
print(len(webtoon_list))

# 4. 각 웹툰이 1주에 총 몇 회 연재되는지 표시해보기 (덴마의 경우 주3회)

# 방법 1) 직접 구현
webtoon_dict = {}
for title in webtoon_list:
    # webtoon_dict에서 title key에 해당하는 값을 가져옴
    cur_webtoon_count = webtoon_dict.get(title)
	if not cur_webtoon_count:
        webtoon_dict[title] = 1
    else:
        webtoon_dict[title] += 1
        
# 방법 2) Counter를 사용
from collections import Counter
webtoon_dict = Counter(webtoon_list)

# 이 webtoon_dict를 주간 연재 횟수 순으로 정렬
# count를 기준으로 새 dict를 생성하기
webtoon_count_dict = {}

for title,count in webtoon_dict.items():
    # count를 key로 사용, value가 없는 경우 빈 리스트를 value로 할당
    webtoon_count_dict.setdefault(count, [])
    webtoon_count_dict[count].appent(title)

from collections import OrderedDict
s = OrderedDict(sorted(webtoon_count_dict.items(), key=lambda item: item[0], reverse=True))

# 이 webtoon_dict를 주간 연재 횟수 순으로 정렬
#  operator.itemgetter사용
import operator
from collection import OrderDict
s = OrderedDict(sorted(webtoon_dict.items()), key=operator.itemgetter(1), reverse=True)


# 이 webtoon_dict를 주간 연재 횟수 순으로 정렬
#  key에 lambda함수
from collections import OrderedDict
s = OrderedDict(sorted(webtoon_dict.items(), key=lambda item: item[1], reverse=True))

# 이 webtoon_dict를
#  1. 주간 연재 횟수 순 
#  2. 같은 경우 제목순
#    으로 정렬
from collections import OrderedDict

def webtoon_sort(item):
    return (-item[1], item[0])
    
s = OrderedDict(sorted(webtoon_dict.items(), key=webtoon_sort))
```