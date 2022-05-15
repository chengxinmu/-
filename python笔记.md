# 1、python保留字

```python
>>> import keyword
>>> print(keyword.kwlist)
['False', 'None', 'True', 'and', 'as', 'assert', 'async', 'await', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```

常量全大写的默认为常量

例 MAX=100

## 1.1、虚拟环境

```shell
# 创建虚拟环境
python -m venv python_ven_demo
# 进入虚拟环境
source python_ven_demo/bin/activate
# 退出虚拟环境
deactivate
```

# 2、变量类型

str、int、float、bool、byte    （单独存值）

list、tuple、set、dict		（容器类）

```python
a = 0.5 + 0.8j
print(a.real)
print(a.imag)
```



# 3、运算符

## 3.1、算数运算符

以下假设变量a为10，变量b为21：

| 运算符 | 描述                                                         |               实例               |
| :----- | :----------------------------------------------------------- | :------------------------------: |
| +      | 加 - 两个对象相加   <!--字符串表示拼接-->                    |        a + b 输出结果 31         |
| -      | 减 - 得到负数或是一个数减去另一个数                          |        a - b 输出结果 -11        |
| *      | 乘 - 两个数相乘 <!--返回乘积--> 字符串则返回一个被重复若干次的字符串 |        a * b 输出结果 210        |
| /      | 除 - x 除以 y                                                |        b / a 输出结果 2.1        |
| %      | 取模 - 返回除法的余数                                        |         b % a 输出结果 1         |
| **     | 幂 - 返回x的y次幂                                            |        a**b 为10的21次方         |
| //     | 取整除 - 向下取接近商的整数                                  | `>>> 9//2 4`      `>>> -9//2 -5` |

## 3.2、赋值运算符

以下假设变量a为10，变量b为20：

| 运算符 | 描述                                                         |                             实例                             |
| :----- | :----------------------------------------------------------- | :----------------------------------------------------------: |
| =      | 简单的赋值运算符                                             |            c = a + b 将 a + b 的运算结果赋值为 c             |
| +=     | 加法赋值运算符                                               |                   c += a 等效于 c = c + a                    |
| -=     | 减法赋值运算符                                               |                   c -= a 等效于 c = c - a                    |
| *=     | 乘法赋值运算符                                               |                   c *= a 等效于 c = c * a                    |
| /=     | 除法赋值运算符                                               |                   c /= a 等效于 c = c / a                    |
| %=     | 取模赋值运算符                                               |                   c %= a 等效于 c = c % a                    |
| **=    | 幂赋值运算符                                                 |                  c **= a 等效于 c = c ** a                   |
| //=    | 取整除赋值运算符                                             |                  c //= a 等效于 c = c // a                   |
| :=     | 海象运算符，可在表达式内部为变量赋值。**Python3.8 版本新增运算符**。 | 在这个示例中，赋值表达式可以避免调用 len() 两次:`if (n := len(a)) > 10:    print(f"List is too long ({n} elements, expected <= 10)")` |

赋值时小整数对象池。

## 3.3、关系运算符（返回结果为bool值）

以下假设变量a为10，变量b为20：

| 运算符 | 描述                                                         |         实例          |
| :----- | :----------------------------------------------------------- | :-------------------: |
| ==     | 等于 - 比较对象是否相等（比较的是内容）                      | (a == b) 返回 False。 |
| !=     | 不等于 - 比较两个对象是否不相等                              | (a != b) 返回 True。  |
| >      | 大于 - 返回x是否大于y                                        | (a > b) 返回 False。  |
| <      | 小于 - 返回x是否小于y。所有比较运算符返回1表示真，返回0表示假。这分别与特殊的变量True和False等价。注意，这些变量名的大写。 |  (a < b) 返回 True。  |
| >=     | 大于等于 - 返回x是否大于等于y。                              | (a >= b) 返回 False。 |
| <=     | 小于等于 - 返回x是否小于等于y。                              | (a <= b) 返回 True。  |

```python
>>> b1 = 1000
>>> b2 = 1000
>>> print(b1 is b2)			#此命令在pycharm中结果和终端中不同。
False
>>> print(b1 == b2)
True
```

## 3.4、逻辑运算符

Python语言支持逻辑运算符，以下假设变量 a 为 10, b为 20:

| 运算符 | 逻辑表达式 | 描述                                                         |          实例           |
| :----- | :--------- | :----------------------------------------------------------- | :---------------------: |
| and    | x and y    | 布尔"与" - 如果 x 为 False，x and y 返回 False，否则它返回 y 的计算值。  <!--False即为0--> |   (a and b) 返回 20。   |
| or     | x or y     | 布尔"或" - 如果 x 是 True，它返回 x 的值，否则它返回 y 的计算值。   <!--True即为1--> |   (a or b) 返回 10。    |
| not    | not x      | 布尔"非" - 如果 x 为 True，返回 False 。如果 x 为 False，它返回 True。 | not(a and b) 返回 False |

## 3.5、三目运算符

结果1 if 条件  else 结果2

```python
>>> result = 5 + 6 if 5 > 6 else 5 - 6
>>> print(result)
-1
```



## 3.6、进制转换

负数的二进制：正数的二进制反码加一。

```
3  0000 0011
-3 1111 1101
```

```python
a = 0b01001011   #二进制
b = int(a)
print(b)
>>>75

c = oct(a)   #八进制
print(c)
>>>0o113

d = hex(a)   #十六进制
print(d)
>>>0x4b

result = int('0o65',base=8)
print(result)
>>>53
```

## 3.7、位运算

下表中变量 a 为 60，b 为 13二进制格式如下：

```shell
a = 0011 1100

b = 0000 1101

-----------------

a&b = 0000 1100

a|b = 0011 1101

a^b = 0011 0001

~a  = 1100 0011
```

| 运算符 | 描述                                                         |                             实例                             |
| :----- | :----------------------------------------------------------- | :----------------------------------------------------------: |
| &      | 按位与运算符：参与运算的两个值,如果两个相应位都为1,则该位的结果为1,否则为0 |         (a & b) 输出结果 12 ，二进制解释： 0000 1100         |
| \|     | 按位或运算符：只要对应的二个二进位有一个为1时，结果位就为1。 |        (a \| b) 输出结果 61 ，二进制解释： 0011 1101         |
| ^      | 按位异或运算符：当两对应的二进位相异时，结果为1              |         (a ^ b) 输出结果 49 ，二进制解释： 0011 0001         |
| ~      | 按位取反运算符：对数据的每个二进制位取反,即把1变为0,把0变为1。**~x** 类似于 **-x-1** | (~a ) 输出结果 -61 ，二进制解释： 1100 0011， 在一个有符号二进制数的补码形式。 |
| <<     | 左移动运算符：运算数的各二进位全部左移若干位，由"<<"右边的数指定移动的位数，高位丢弃，低位补0。 | a << 2 输出结果 240 ，二进制解释： 1111 0000。2<<3=16(2*2**3) |
| >>     | 右移动运算符：把">>"左边的运算数的各二进位全部右移若干位，">>"右边的数指定移动的位数 | a >> 2 输出结果 15 ，二进制解释： 0000 1111。  60//2*2=15，***原值整除2的右移位数次方。*** |

## 3.8、成员运算符

| 运算符 | 描述                                                    | 实例                                              |
| :----- | :------------------------------------------------------ | :------------------------------------------------ |
| in     | 如果在指定的序列中找到值返回 True，否则返回 False。     | x 在 y 序列中 , 如果 x 在 y 序列中返回 True。     |
| not in | 如果在指定的序列中没有找到值返回 True，否则返回 False。 | x 不在 y 序列中 , 如果 x 不在 y 序列中返回 True。 |

## 3.9、身份运算符

| 运算符 | 描述                                        | 实例                                                         |
| :----- | :------------------------------------------ | :----------------------------------------------------------- |
| is     | is 是判断两个标识符是不是引用自一个对象     | **x is y**, 类似 **id(x) == id(y)** , 如果引用的是同一个对象则返回 True，否则返回 False |
| is not | is not 是判断两个标识符是不是引用自不同对象 | **x is not y** ， 类似 **id(a) != id(b)**。如果引用的不是同一个对象则返回结果 True，否则返回 False。 |

小整数对象池的体现

## 3.10、运算符优先级

| 运算符                   | 描述                                                   |
| :----------------------- | :----------------------------------------------------- |
| **                       | 指数 (最高优先级)                                      |
| ~ + -                    | 按位翻转, 一元加号和减号 (最后两个的方法名为 +@ 和 -@) |
| * / % //                 | 乘，除，求余数和取整除                                 |
| + -                      | 加法减法                                               |
| >> <<                    | 右移，左移运算符                                       |
| &                        | 位 'AND'                                               |
| ^ \|                     | 位运算符                                               |
| <= < > >=                | 比较运算符                                             |
| == !=                    | 等于运算符                                             |
| = %= /= //= -= += *= **= | 赋值运算符                                             |
| is ，is not              | 身份运算符                                             |
| in ，not in              | 成员运算符                                             |
| not and or               | 逻辑运算符                                             |

# 4、语句

## 4.1、条件语句

```python
if condition_1:
    statement_block_1
elif condition_2:
    statement_block_2
else:
    statement_block_3
```

```shell
if 条件：
	动作
if 条件：
	动作
if 条件：
	动作
```

```python
if 表达式1:
    语句
    if 表达式2:
        语句
    elif 表达式3:
        语句
    else:
        语句
elif 表达式4:
    语句
else:
    语句
```

<!--占位符-->

%s(字符串占位),%d(整形占位),%.nf(保留小数点后几位)

{}    .format()  所有类型均支持

```python
choice = int(input('请选择功能：1、进场 2、出场 3、查询 4、统计 5、退出: '))
car_location = []
if choice == 1:
    car_in = input('输入车牌号：')
    print(car_in + '进场了！')
    car_location.append(car_in)
elif choice == 2:
    car_out = input('输入车牌号：')
    print(car_out + '出场了')
    car_location.remove(car_out)
elif choice == 3:
    car_inquire = input('输入查询的车牌号：')
    if car_inquire in car_location:
        print('车辆在场！')
    else:
        print('车辆不在！')
elif choice == 4:
    print('停车场使用数量：', len(car_location))
elif choice == 5:
    print('退出系统!')
    exit(0)
else:
    print('选择错误!')
```

## 4.2、循环语句

```python
while <expr>:
    <statement(s)>
else:
    <additional_statement(s)>
```

### 4.2.1、验证账户密码，3次锁定

```python
count = 1
while count <= 3:

    username = input('用户名：')
    password = input('密码：')
    if username == 'admin' and password == '123456':
        print('登陆成功！')
        break
    else:
        print('输入有误！')
    count += 1
if count == 4:
    print('账户锁定!')
```

```python
count = 1
while count <= 3:

    username = input('用户名：')
    password = input('密码：')
    if username == 'admin' and password == '123456':
        print('登陆成功！')
        break
    else:
        print('输入有误！')
    count += 1
else:		# else的使用
    print('账户锁定!')
```

### 4.2.2、1-100奇数累加

```python
count = 1
sum = 0
while count <= 100:
    if count %2 != 0:
        sum += count
    count += 1
print('累加和为：',sum)

# 利用range完成奇数累加
r = range(1,100,2)
sum = 0
for i in r:
    sum += i
print(sum)
```

### 4.2.3、生成随机银行卡号

```python
import random

card = '62'
i = 0
while i < 14:
    r = random.randint(0, 9)
    card += str(r)
    i += 1
print(card)
```

### 4.2.4、打印1-50之间的偶数

```python
count = 0
for i in range(1, 51):
    if i % 2 == 0:
        print(str(i) + '\t', end='')
        count += 1
        if count % 5 == 0:
            print()
```

### 4.2.5、猜猜乐游戏

```python
import random

print('猜猜乐'.center(30,'*'))
sorce = 0
while True:
    number =3
    guess_number = random.randint(1,20)
    for i in range(3):
        guess = int(input('请猜数字(1-20)：'))
        if guess > guess_number:
            print('猜大了！')
            number-=1
        elif guess < guess_number:
            print('猜小了！')
            number -= 1
        else:
            print('猜对了！')
            sorce+=number
            break
    else:
        print('这一局次数用完，下次再来吧！')
    answer = input('是否继续游戏？(yes/no):')
    if answer != 'yes':
        print('欢迎下次再来！')
        break
print('得分为',sorce)
```

### 4.2.6、跳一跳游戏

```python
import random
import time

step = 0
num = 0
while True:
    i = random.randint(0,10)
    time.sleep(random.random())
    if i == 0:
        print('game over')
        break
    elif i %5 ==0 or i %3 ==0:
        step+=(i*2)
        num+=1
        print('步数翻倍，本次跳了%d步，总步数：%d'%(i*2,step))
    else:
        step+=i
        num+=1
        print('本次跳了%d步，总步数：%d'%(i,step))
print('本次跳了%d步，跳了%d次！'%(step,num))
```

### 4.2.7、99乘法表

```python
for i in range(1, 10):
    for j in range(i, 10):
        print('{}*{}={}\t'.format(i, j, i * j), end='')
    print()

for i in range(1, 10):
    for j in range(1, i + 1):
        print('{}*{}={}\t'.format(i, j, i * j), end='')
    print()
```

## 4.3、 跳转语句

break 和continue

```python
for i in range(0,31):
    if i %3==0 and i %4 ==0:
        continue  #跳过本次循环
        #break	# 跳出全部循环
    print(i)
```

# 5、数据类型

## 5.1、字符串

+ ###### **<u>运算符</u>**

| 操作符 | 描述                                                         | 实例                            |
| :----- | :----------------------------------------------------------- | :------------------------------ |
| +      | 字符串连接                                                   | a + b 输出结果： HelloPython    |
| *      | 重复输出字符串                                               | a*2 输出结果：HelloHello        |
| []     | 通过索引获取字符串中字符                                     | a[1] 输出结果 **e**             |
| [ : ]  | 截取字符串中的一部分，遵循**左闭右开**原则，str[0:2] 是不包含第 3 个字符的。 | a[1:4] 输出结果 **ell**         |
| in     | 成员运算符 - 如果字符串中包含给定的字符返回 True             | **'H' in a** 输出结果 True      |
| not in | 成员运算符 - 如果字符串中不包含给定的字符返回 True           | **'M' not in a** 输出结果 True  |
| r/R    | 原始字符串 - 原始字符串：所有的字符串都是直接按照字面的意思来使用，没有转义特殊或不能打印的字符。 原始字符串除在字符串的第一个引号前加上字母 **r**（可以大小写）以外，与普通字符串有着几乎完全相同的语法。 | `print( r'\n' ) print( R'\n' )` |
| %      | 格式字符串(**.format**)                                      | 请看下一节内容。                |

<!--字符串[start:stop:step]-->  步长可以设置为负值，表示反方向，逆序输出。

```python
s = 'abcdefg'
print(s[::-1])
```

+ ###### **<u>find()函数</u>**     **<u>index()函数</u>**

find返回-1 ，index则报ValueError

```python
s = 'abcdefgwqeyuiqwhdanxsahdkfv,qpqwod1237813712jd'
print(s.find('e'))
```

+ ###### **<u>rfind()从右侧开始搜索 rindex()</u>**

+ replace()    敏感词替换

  ```python
  s = 'hello 帅哥 帅哥'
  print(s.replace('帅哥','美女',1))  #(old,new,count)
  ```

+ ```python
  s = 'hello 帅哥 帅哥'
  print(s.split(' ',4))
  ```

```python
s = 'hello'
r = s.isupper()
print(r)

r = s.islower()
print(r)

r = s.isalpha()
print(r)

r = s.isdigit()
print(r)

r = s.startswith('h')
print(r)

r = s.endswith('o')
print(r)

s = 'AA'
s1 = s.join(['BB','CC','DD','EE'])  #join方法只能处理字符串参数
print(s1)
```

+ 对齐相关方法

  ```python
  print('hello'.center(30,'*'))
  
  print('hello'.ljust(30,'*'))
  
  print('hello'.rjust(30,'*'))
  ```

+ 去除空格

  ```python
  s = '   abc   '
  print(s.strip())
  ```

Python 的字符串常用内建函数如下：

| 序号 | 方法及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | [capitalize()](https://www.runoob.com/python3/python3-string-capitalize.html) 将字符串的第一个字符转换为大写 |
| 2    | [center(width, fillchar)](https://www.runoob.com/python3/python3-string-center.html) 返回一个指定的宽度 width 居中的字符串，fillchar 为填充的字符，默认为空格。 |
| 3    | [count(str, beg= 0,end=len(string))](https://www.runoob.com/python3/python3-string-count.html) 返回 str 在 string 里面出现的次数，如果 beg 或者 end 指定则返回指定范围内 str 出现的次数 |
| 4    | [bytes.decode(encoding="utf-8", errors="strict")](https://www.runoob.com/python3/python3-string-decode.html) Python3 中没有 decode 方法，但我们可以使用 bytes 对象的 decode() 方法来解码给定的 bytes 对象，这个 bytes 对象可以由 str.encode() 来编码返回。 |
| 5    | [encode(encoding='UTF-8',errors='strict')](https://www.runoob.com/python3/python3-string-encode.html) 以 encoding 指定的编码格式编码字符串，如果出错默认报一个ValueError 的异常，除非 errors 指定的是'ignore'或者'replace' |
| 6    | [endswith(suffix, beg=0, end=len(string))](https://www.runoob.com/python3/python3-string-endswith.html) 检查字符串是否以 obj 结束，如果beg 或者 end 指定则检查指定的范围内是否以 obj 结束，如果是，返回 True,否则返回 False. |
| 7    | [expandtabs(tabsize=8)](https://www.runoob.com/python3/python3-string-expandtabs.html) 把字符串 string 中的 tab 符号转为空格，tab 符号默认的空格数是 8 。 |
| 8    | [find(str, beg=0, end=len(string))](https://www.runoob.com/python3/python3-string-find.html) 检测 str 是否包含在字符串中，如果指定范围 beg 和 end ，则检查是否包含在指定范围内，如果包含返回开始的索引值，否则返回-1 |
| 9    | [index(str, beg=0, end=len(string))](https://www.runoob.com/python3/python3-string-index.html) 跟find()方法一样，只不过如果str不在字符串中会报一个异常. |
| 10   | [isalnum()](https://www.runoob.com/python3/python3-string-isalnum.html) 如果字符串至少有一个字符并且所有字符都是字母或数字则返 回 True,否则返回 False |
| 11   | [isalpha()](https://www.runoob.com/python3/python3-string-isalpha.html) 如果字符串至少有一个字符并且所有字符都是字母则返回 True, 否则返回 False |
| 12   | [isdigit()](https://www.runoob.com/python3/python3-string-isdigit.html) 如果字符串只包含数字则返回 True 否则返回 False.. |
| 13   | [islower()](https://www.runoob.com/python3/python3-string-islower.html) 如果字符串中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是小写，则返回 True，否则返回 False |
| 14   | [isnumeric()](https://www.runoob.com/python3/python3-string-isnumeric.html) 如果字符串中只包含数字字符，则返回 True，否则返回 False |
| 15   | [isspace()](https://www.runoob.com/python3/python3-string-isspace.html) 如果字符串中只包含空白，则返回 True，否则返回 False. |
| 16   | [istitle()](https://www.runoob.com/python3/python3-string-istitle.html) 如果字符串是标题化的(见 title())则返回 True，否则返回 False |
| 17   | [isupper()](https://www.runoob.com/python3/python3-string-isupper.html) 如果字符串中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是大写，则返回 True，否则返回 False |
| 18   | [join(seq)](https://www.runoob.com/python3/python3-string-join.html) 以指定字符串作为分隔符，将 seq 中所有的元素(的字符串表示)合并为一个新的字符串 |
| 19   | [len(string)](https://www.runoob.com/python3/python3-string-len.html) 返回字符串长度 |
| 20   | [ljust(width[, fillchar\])](https://www.runoob.com/python3/python3-string-ljust.html) 返回一个原字符串左对齐,并使用 fillchar 填充至长度 width 的新字符串，fillchar 默认为空格。 |
| 21   | [lower()](https://www.runoob.com/python3/python3-string-lower.html) 转换字符串中所有大写字符为小写. |
| 22   | [lstrip()](https://www.runoob.com/python3/python3-string-lstrip.html) 截掉字符串左边的空格或指定字符。 |
| 23   | [maketrans()](https://www.runoob.com/python3/python3-string-maketrans.html) 创建字符映射的转换表，对于接受两个参数的最简单的调用方式，第一个参数是字符串，表示需要转换的字符，第二个参数也是字符串表示转换的目标。 |
| 24   | [max(str)](https://www.runoob.com/python3/python3-string-max.html) 返回字符串 str 中最大的字母。 |
| 25   | [min(str)](https://www.runoob.com/python3/python3-string-min.html) 返回字符串 str 中最小的字母。 |
| 26   | [replace(old, new [, max\])](https://www.runoob.com/python3/python3-string-replace.html) 把 将字符串中的 str1 替换成 str2,如果 max 指定，则替换不超过 max 次。 |
| 27   | [rfind(str, beg=0,end=len(string))](https://www.runoob.com/python3/python3-string-rfind.html) 类似于 find()函数，不过是从右边开始查找. |
| 28   | [rindex( str, beg=0, end=len(string))](https://www.runoob.com/python3/python3-string-rindex.html) 类似于 index()，不过是从右边开始. |
| 29   | [rjust(width,[, fillchar\])](https://www.runoob.com/python3/python3-string-rjust.html) 返回一个原字符串右对齐,并使用fillchar(默认空格）填充至长度 width 的新字符串 |
| 30   | [rstrip()](https://www.runoob.com/python3/python3-string-rstrip.html) 删除字符串末尾的空格. |
| 31   | [split(str="", num=string.count(str))](https://www.runoob.com/python3/python3-string-split.html) num=string.count(str)) 以 str 为分隔符截取字符串，如果 num 有指定值，则仅截取 num+1 个子字符串 |
| 32   | [splitlines([keepends\])](https://www.runoob.com/python3/python3-string-splitlines.html) 按照行('\r', '\r\n', \n')分隔，返回一个包含各行作为元素的列表，如果参数 keepends 为 False，不包含换行符，如果为 True，则保留换行符。 |
| 33   | [startswith(substr, beg=0,end=len(string))](https://www.runoob.com/python3/python3-string-startswith.html) 检查字符串是否是以指定子字符串 substr 开头，是则返回 True，否则返回 False。如果beg 和 end 指定值，则在指定范围内检查。 |
| 34   | [strip([chars\])](https://www.runoob.com/python3/python3-string-strip.html) 在字符串上执行 lstrip()和 rstrip() |
| 35   | [swapcase()](https://www.runoob.com/python3/python3-string-swapcase.html) 将字符串中大写转换为小写，小写转换为大写 |
| 36   | [title()](https://www.runoob.com/python3/python3-string-title.html) 返回"标题化"的字符串,就是说所有单词都是以大写开始，其余字母均为小写(见 istitle()) |
| 37   | [translate(table, deletechars="")](https://www.runoob.com/python3/python3-string-translate.html) 根据 str 给出的表(包含 256 个字符)转换 string 的字符, 要过滤掉的字符放到 deletechars 参数中 |
| 38   | [upper()](https://www.runoob.com/python3/python3-string-upper.html) 转换字符串中的小写字母为大写 |
| 39   | [zfill (width)](https://www.runoob.com/python3/python3-string-zfill.html) 返回长度为 width 的字符串，原字符串右对齐，前面填充0 |
| 40   | [isdecimal()](https://www.runoob.com/python3/python3-string-isdecimal.html) 检查字符串是否只包含十进制字符，如果是返回 true，否则返回 false。 |

## 5.2、列表

### 5.2.1、列表操作符

| Python 表达式                         | 结果                         | 描述                 |
| :------------------------------------ | :--------------------------- | :------------------- |
| len([1, 2, 3])                        | 3                            | 长度                 |
| [1, 2, 3] + [4, 5, 6]                 | [1, 2, 3, 4, 5, 6]           | 组合                 |
| ['Hi!'] * 4                           | ['Hi!', 'Hi!', 'Hi!', 'Hi!'] | 重复                 |
| 3 in [1, 2, 3]                        | True                         | 元素是否存在于列表中 |
| for x in [1, 2, 3]: print(x, end=" ") | 1 2 3                        | 迭代                 |

### 5.2.2、索引、切片

### 5.2.3、嵌套

```python
>>>a = ['a', 'b', 'c']
>>> n = [1, 2, 3]
>>> x = [a, n]
>>> x
[['a', 'b', 'c'], [1, 2, 3]]
>>> x[0]
['a', 'b', 'c']
>>> x[0][1]
'b'
```

### 5.2.4、列表函数&方法

Python包含以下函数:

| 序号 | 函数                                                         |
| :--- | :----------------------------------------------------------- |
| 1    | [len(list)](https://www.runoob.com/python3/python3-att-list-len.html) 列表元素个数 |
| 2    | [max(list)](https://www.runoob.com/python3/python3-att-list-max.html) 返回列表元素最大值 |
| 3    | [min(list)](https://www.runoob.com/python3/python3-att-list-min.html) 返回列表元素最小值 |
| 4    | [list(seq)](https://www.runoob.com/python3/python3-att-list-list.html) 将元组转换为列表 |
| 5    | sum()                                                        |

Python包含以下方法:

| 序号 | 方法                                                         |
| :--- | :----------------------------------------------------------- |
| 1    | [list.append(obj)](https://www.runoob.com/python3/python3-att-list-append.html) 在列表末尾添加新的对象 |
| 2    | [list.count(obj)](https://www.runoob.com/python3/python3-att-list-count.html) 统计某个元素在列表中出现的次数 |
| 3    | [list.extend(seq)](https://www.runoob.com/python3/python3-att-list-extend.html) 在列表末尾一次性追加另一个序列中的多个值（用新列表扩展原来的列表） |
| 4    | [list.index(obj)](https://www.runoob.com/python3/python3-att-list-index.html) 从列表中找出某个值第一个匹配项的索引位置 |
| 5    | [list.insert(index, obj)](https://www.runoob.com/python3/python3-att-list-insert.html) 将对象插入列表 |
| 6    | [list.pop([index=-1\])](https://www.runoob.com/python3/python3-att-list-pop.html) 移除列表中的一个元素（默认最后一个元素），并且返回该元素的值。<!--参数为索引值--> |
| 7    | [list.remove(obj)](https://www.runoob.com/python3/python3-att-list-remove.html) 移除列表中某个值的第一个匹配项。 <!--参数为对象--> |
| 8    | [list.reverse()](https://www.runoob.com/python3/python3-att-list-reverse.html) 反向列表中元素 |
| 9    | [list.sort( key=None, reverse=False)](https://www.runoob.com/python3/python3-att-list-sort.html) 对原列表进行排序 |
| 10   | [list.clear()](https://www.runoob.com/python3/python3-att-list-clear.html) 清空列表 |
| 11   | [list.copy()](https://www.runoob.com/python3/python3-att-list-copy.html) 复制列表 |

```python
list_1 = [1, 3, 2, 4, 5, 6]
r = list_1.pop()

print(r)
r = list_1.pop()

print(r)
print(list_1)

list_1.insert(1,10)
print(list_1)

list_2 = [2,1,3,4,5,6,7]
list_1.extend(list_2)
print(list_1)

list_1.reverse()
print(list_1)

del list_1[3]
print(list_1)

del list_1  #清空内容和地址
print(list_1)

list_1[2] = 9  #更改

-----------------------------------------------------------------------
# 比较几种操作的区别
list_1 = ['张三','李四',['王五','赵六','田七']]
list_2 = list_1
list_3 = list_1[:]
print(list_1,list_2,list_3)    #1和2为赋值操作完全一致
print(id(list_1),id(list_2),id(list_3))   # 1和3为切片操作，类似复制，仅内容一致。

# 列表的排序方法
list = [100, 200, 150, 175, 160, 180, 130, 155]
list.sort(reverse=True)
print(list)

list1 = sorted(list, reverse=True)
print(list1)

list.sort()
list.reverse()
print(list)
```

### 5.2.5、浅拷贝和深拷贝

```python
# 改变列表中的可变类型会影响到其他复制版本，若是不可变类型改变值只会影响当前版本
# 浅拷贝。(共用一个可变类型地址)
list_1 = ['a','b']
list_copy = list_1.copy()
print(list_copy)
print(list_1)
print(list_1 is list_copy)

list_1.pop()
list_1.append('c')

print(list_1)
print(list_copy)

list_1 = ['张三','李四',['王五','赵六','田七']]
list_2 = list_1.copy()
print(list_1)
print(list_2)

list_1[2].pop()
list_1[2].append('秦始皇')
print(list_1)
print(list_2)

import copy
list_1 = ['张三','李四',['王五','赵六','田七']]

list_2 = list_1.copy()

list_3 = copy.copy(list_1)

print(list_1,list_2,list_3)
print(id(list_1),id(list_2),id(list_3))

list_2[2].append('秦始皇')
print(list_1,list_2,list_3)
print(id(list_1),id(list_2),id(list_3))
```

```python
# 深拷贝（重新生成可变类型地址，共用不可变类型地址）
# 
import copy
list_1 = ['张三','李四',['王五','赵六','田七']]
list_2 = copy.deepcopy(list_1)

print(list_1,list_2)
print(id(list_1),id(list_2))

print('list_1'.center(30,'*'))
for  i in list_1:
    print(i,id(i))
print('list_2'.center(30,'*'))
for i in list_2:
    print(i,id(i))
print('*'*40)
list_2[2].append('秦始皇')
print(list_1,list_2,sep='\n')
print('list_1的id为:',id(list_1),'list_2的id为:',id(list_2),sep='\n')
```



## 5.3、元组

类似列表，但是不可修改。**元组只有一个元素时，必须以','结尾**。

### 5.3.1、运算符

| Python 表达式                  | 结果                         | 描述         |
| :----------------------------- | :--------------------------- | :----------- |
| len((1, 2, 3))                 | 3                            | 计算元素个数 |
| (1, 2, 3) + (4, 5, 6)          | (1, 2, 3, 4, 5, 6)           | 连接         |
| ('Hi!',) * 4                   | ('Hi!', 'Hi!', 'Hi!', 'Hi!') | 复制         |
| 3 in (1, 2, 3)                 | True                         | 元素是否存在 |
| for x in (1, 2, 3): print (x,) | 1 2 3                        | 迭代         |

### 5.3.2、元组函数&方法

index()   count()   tuple()

| 序号 | 方法及描述                               | 实例                                                         |
| :--- | :--------------------------------------- | :----------------------------------------------------------- |
| 1    | len(tuple) 计算元组元素个数。            | `>>> tuple1 = ('Google', 'Runoob', 'Taobao') >>> len(tuple1) 3 >>> ` |
| 2    | max(tuple) 返回元组中元素最大值。        | `>>> tuple2 = ('5', '4', '8') >>> max(tuple2) '8' >>> `      |
| 3    | min(tuple) 返回元组中元素最小值。        | `>>> tuple2 = ('5', '4', '8') >>> min(tuple2) '4' >>> `      |
| 4    | tuple(iterable) 将可迭代系列转换为元组。 | `>>> list1= ['Google', 'Taobao', 'Runoob', 'Baidu'] >>> tuple1=tuple(list1) >>> tuple1 ('Google', 'Taobao', 'Runoob', 'Baidu')` |

## 5.4、集合

集合（set）是一个无序的**不重复**元素序列。

可以使用大括号 **{ }** 或者 **set()** 函数创建集合，注意：创建一个空集合必须用 **set()** 而不是 **{ }**，因为 **{ }** 是用来创建一个空字典

**集合不支持下表、切片、+、***。

### 5.4.1、集合常见方法

| 方法                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [add()](https://www.runoob.com/python3/ref-set-add.html)     | 为集合添加元素                                               |
| [clear()](https://www.runoob.com/python3/ref-set-clear.html) | 移除集合中的所有元素                                         |
| [copy()](https://www.runoob.com/python3/ref-set-copy.html)   | 拷贝一个集合                                                 |
| [difference()](https://www.runoob.com/python3/ref-set-difference.html) | 返回多个集合的差集                                           |
| [difference_update()](https://www.runoob.com/python3/ref-set-difference_update.html) | 移除集合中的元素，该元素在指定的集合也存在。                 |
| [discard()](https://www.runoob.com/python3/ref-set-discard.html) | 删除集合中指定的元素                                         |
| [intersection()](https://www.runoob.com/python3/ref-set-intersection.html) | 返回集合的交集                                               |
| [intersection_update()](https://www.runoob.com/python3/ref-set-intersection_update.html) | 返回集合的交集。                                             |
| [isdisjoint()](https://www.runoob.com/python3/ref-set-isdisjoint.html) | 判断两个集合是否包含相同的元素，如果没有返回 True，否则返回 False。 |
| [issubset()](https://www.runoob.com/python3/ref-set-issubset.html) | 判断指定集合是否为该方法参数集合的子集。                     |
| [issuperset()](https://www.runoob.com/python3/ref-set-issuperset.html) | 判断该方法的参数集合是否为指定集合的子集                     |
| [pop()](https://www.runoob.com/python3/ref-set-pop.html)     | 随机移除元素                                                 |
| [remove()](https://www.runoob.com/python3/ref-set-remove.html) | 移除指定元素                                                 |
| [symmetric_difference()](https://www.runoob.com/python3/ref-set-symmetric_difference.html) | 返回两个集合中不重复的元素集合。                             |
| [symmetric_difference_update()](https://www.runoob.com/python3/ref-set-symmetric_difference_update.html) | 移除当前集合中在另外一个指定集合相同的元素，并将另外一个指定集合中不同的元素插入到当前集合中。 |
| [union()](https://www.runoob.com/python3/ref-set-union.html) | 返回两个集合的并集                                           |
| [update()](https://www.runoob.com/python3/ref-set-update.html) | 给集合添加元素                                               |

```python
import random

list_1 = []
for i in range(10):
    ran = random.randint(0, 20)
    list_1.append(ran)
print(list_1)
set_2 = set(list_1)
print(set_2)

set_2.add('hello')
print(set_2)

tuple_1 = ('林志玲', '言承旭')
set_2.update(tuple_1)

print(set_2)

set_2.remove('言承旭')            ######元素不存在则报错
print(set_2)

set_2.pop()
print(set_2)                    ##### 随机删除，常用默认删除第一个元素
print('欢迎'.center(30, '*'))
set_2.discard('小黑')  #####删除的元素不存在时不报错
print(set_2)
```

```python
# 随机生成10位集合,并排序
import random

set_1 = set()
while True:
    ran = random.randint(1, 20)
    set_1.add(ran)
    if len(set_1) == 10:
        break
print(set_1)
print(min(set_1))
print(max(set_1))
print(sorted(set_1,reverse=True))
```

### 5.4.2、集合运算

```python
set_1 = {2, 3, 4, 5, 6, 7}
set_2 = {3, 4, 8, 7, 9, 7}
print(set_1 == set_2)

# set_3 = set_1 + set_1
# print(set_3)                      不支持加

print(set_2 - set_1)

set_3 = set_2.difference(set_1)		#差集
print(set_3)

set_4 = set_1 & set_2
print(set_4)

set_5 = set_1.intersection(set_2)	#交集
print(set_5)

set_6 = set_1 | set_2
print(set_6)
set_7 = set_1.union(set_2)		# 并集
print(set_7)

# 对称差集,A有B没有和B有A没有

resurlt = set_1 ^ set_2
print(resurlt)

resurlt = (set_1 | set_2) - (set_1 & set_2)
print(resurlt)

resurlt = set_1.symmetric_difference(set_2)
print(resurlt)

set_1.difference_update(set_2)
print(set_1)
```

<!--enumerate-->

**枚举函数**

```python
set1 = {'小黄','旺财','大黄'}
for index,value in enumerate(set1):
    print('{}:{}'.format(index,value))
    
>>> 0:大黄
	1:小黄
	2:旺财
```

### 5.4.3、小游戏

```python
print('*'*40)
print('\t\t欢迎来到王者荣耀')
print('*'*40)

role = input('请选择游戏人物:(1.鲁班 2.后羿 3.李白 4.孙尚香 5.貂蝉 6.诸葛亮):')
role_list = ['鲁班','后羿','李白','孙尚香','貂蝉','诸葛亮']
if role not in role_list:
    print('请输入正确的用户名！')
else:
    coins = 1000

    print('欢迎！{}来到王者荣耀，当前金币是:{}'.format(role,coins))

    while True:
        choice = int(input('请选择:\n 1.购买武器\n 2.战斗\n 3.删除武器\n 4.查看武器\n 5.退出游戏\n'))

        if choice == 1:
            print('欢迎来到武器库:')
    # weapons = [['刀',100],['枪',200],['剑',150],['戟',175],['斧',160],['钺',180],['勾',130],['叉',155]]
            weapons = ['刀', '枪', '剑', '戟', '斧', '钺', '勾', '叉']
            price = [100, 200, 150, 175, 160, 180, 130, 155]
            weapon_list = []

            print('武器对应价格如下\n')
            for weapon in weapons:
                num = weapons.index(weapon)
                print(weapon,'：',price[num],end=',')
            weapon_name = input('请输入要购买的武器名称：')
            # if weapon_name not in weapon_list and weapon_name in weapons and coins > price[weapons.index(weapon_name)]:
            #     weapon_list.append(weapon_name)
            #     coins -= price[weapons.index(weapon_name)]
            if weapon_name in weapon_list:
                print('你已经拥有此武器，不可重复购买！')
            if weapon_name not in weapons:
                print('此武器未上架，不可购买！')
            if coins < int(price[weapons.index(weapon_name)]):
                print('金币不足，请充值或者战斗！')
            else:
                weapon_list.append(weapon_name)
                coins -= price[weapons.index(weapon_name)]
                print('{}购买武器{}成功！'.format(role,weapon_name))
                print('现有武器：',weapon_list,'剩余金币：',coins)

        if choice == 2:
            print('战斗开始.......')
        if choice == 3:
            pass
        if choice == 4:
            pass
        if choice == 5:
            answer = input('确定要离开游戏么？(yes/no)')
            if answer == 'yes':
                break
            else:
                print('输入错误，请重新选择！')
```

## 5.5、字典

键必须是**唯一**的，但值则不必。

值可以取任何数据类型，但键必须是**不可变**的，如字符串，数字或元组。

### 5.5.1、空字典定义

```python
dict1 = dict()
dict = {}
```

### 5.5.2、访问字典的值

dict[key]

### 5.5.3、内置方法

| 序号 | 函数及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | [radiansdict.clear()](https://www.runoob.com/python3/python3-att-dictionary-clear.html) 删除字典内所有元素 |
| 2    | [radiansdict.copy()](https://www.runoob.com/python3/python3-att-dictionary-copy.html) 返回一个字典的浅复制 |
| 3    | [radiansdict.fromkeys()](https://www.runoob.com/python3/python3-att-dictionary-fromkeys.html) 创建一个新字典，以序列seq中元素做字典的键，val为字典所有键对应的初始值 |
| 4    | [radiansdict.get(key, default=None)](https://www.runoob.com/python3/python3-att-dictionary-get.html) 返回指定键的值，如果值不在字典中返回default值 |
| 5    | [key in dict](https://www.runoob.com/python3/python3-att-dictionary-in.html) 如果键在字典dict里返回true，否则返回false |
| 6    | [radiansdict.items()](https://www.runoob.com/python3/python3-att-dictionary-items.html) 以列表返回可遍历的(键, 值) 元组数组 |
| 7    | [radiansdict.keys()](https://www.runoob.com/python3/python3-att-dictionary-keys.html) 返回一个迭代器，可以使用 list() 来转换为列表。<!--键--> |
| 8    | [radiansdict.setdefault(key, default=None)](https://www.runoob.com/python3/python3-att-dictionary-setdefault.html) 和get()类似, 但如果键不存在于字典中，将会添加键并将值设为default |
| 9    | [radiansdict.update(dict2)](https://www.runoob.com/python3/python3-att-dictionary-update.html) 把字典dict2的键/值对更新到dict里 |
| 10   | [radiansdict.values()](https://www.runoob.com/python3/python3-att-dictionary-values.html) 返回一个迭代器，可以使用 list() 来转换为列表。<!--值--> |
| 11   | [pop(key[,default\])](https://www.runoob.com/python3/python3-att-dictionary-pop.html) 删除字典给定键 key 所对应的值，返回值为被删除的值。key值必须给出。 否则，返回default值。 |
| 12   | [popitem()](https://www.runoob.com/python3/python3-att-dictionary-popitem.html) 随机返回并删除字典中的最后一对键和值。 |

```python
dict1 = dict()
dict1['name'] = 'bob'
print(dict1)
dict1['age'] = 20
print(dict1)

print('name' in dict1)
r = dict1.keys()
r1 = list(r)
print(r1)
```

### 5.5.4、符号判断

仅支持is  in

```python
print('name' in dict1)
print(dict1['name'] is 'bob')
```

### 5.5.5、可变不可变

改变变量的值其对应的内存地址是否发生改变

str  int  float tuple

list  dict  set

# 6、函数

## 6.1、定义

```python
def 函数名([形参]):
    函数体
    
books = ['红楼梦','西游戏','水浒传','新白娘子传奇']

def show_books():
    print('书籍如下：'.center(40,'*'))
    for book in books:
        print(book)
print(show_books)	#打印函数的地址
show_books()
```

## 6.2、参数

### 6.2.1、普通参数

#### 6.2.1.1、生成验证码

参数个数需和定义时个数一致。

```python
import random

def generate_code(num):
    code = ''
    s = '123456789abcdefghigklmnopqrstuvwxyz'
    for i in range(num):
        ran = random.choice(s)
        code += ran
    print('验证码是：', code)

generate_code(4)
```

#### 6.2.1.2、可变和不可变类型参数

strings, tuples, 和 numbers 是不可更改的对象，而 list,dict 等则是可以修改的对象。

```python

from collections.abc import Iterable

def func(name,friends):
    print('{}的朋友如下：'.format(name))
    if isinstance(friends, Iterable):
        for friend in friends:
            print(friend)
        else:
            print(friends)

name = '钢铁侠'
friends = ['雷神','黑寡妇','鹰眼','绿巨人']
func(name,friends)
print(id(friends))

def add_friends(f):
    name = input('输入名字：')
    if isinstance(f, list):
        f.append(name)

add_friends(friends)
func(name, friends)
print(id(friends))      # 参数为可变类型，传递的是参数地址。
```

```python
# 传递的参数为不可变参数时,函数运算不改变参数原值。
name = 'jack'

def change_name(name):
    name = name[::-1]
    print('新的名称：', name)

change_name(name)
print(name)
```

### 6.2.2、默认值参数

```python
def func(a=8, b=10):
    r = a + b
    print('和为：', r)

func(15)  # 只给a赋值
func(b=15)  # 指定赋值
```

### 6.2.3、可变参数

拆包、装包

```python
list_1 = [1, 2, 3, 4, 5, 7]
print(*list_1)
x, y, *z = list_1
print(x, y, z)
```

```python
def join(seq, *args):
    s = ''
    count = 0
    for i in args:
        s += i
        if not count == len(args) - 1:
            s += seq
        count += 1
    print('------', s)

join('-', 'hello', 'world', 'hello', 'jack')
```

```python
def func(*args, **kwargs):
    print(args)
    print(kwargs)

func(1, 2, 3, 4, a=10, b=15)
```

## 6.3、函数之间调用

```python
books = ['红楼梦', '西游记', '水浒传', '新白娘子传奇']


def show_books():
    print('图书馆书籍如下：'.center(40, '*'))
    for index, book in enumerate(books):
        print('{}:{}'.format(index + 1, book))


def add_book(book_name):
    if book_name and book_name not in books:
        books.append(book_name)
        show_books()
        print('添加成功！')
    else:
        print('添加失败')


add_book('斩龙')
print(books)

add_book('西游记')
```

```python
import random


def func1(a):
    for i in range(a):
        print(i)


def func2():
    ran = random.randint(1, 6)
    func1(ran)


func2()
```

## 6.4、返回值

如果函数无返回值，返回为None。

```python
# 取出最大值的函数
list_a = [1, 2, 3, 4, 5, 56, 7]


def find_max(list_1):
    if isinstance(list_1, list):
        max_num = list_1[0]
        for i in list_1:
            if i > max_num:
                max_num = i
        return max_num   # 不仅可以返回值，还可以结束方法调用。


max_n = find_max(list_a)
print(max_n)
-----------------------------------------------------------------------

list_a = [1, 2, 3, 4, 5, 56, 7]


def find_max(list_1):
    if isinstance(list_1, list):
        max_num = list_1[0]
        max_index = 0
        for index, i in enumerate(list_1):
            if i > max_num:
                max_num = i
                max_index = index
        return max_num, max_index  # 不仅可以返回值，还可以结束方法调用。


r = find_max(list_a)
print(r)
```

```python
def func(n):
    for i in range(n):
        if i == 4:
            print('打印结束！')
            return   # 下方的代码全部不执行。
        print('==========>')
    print('------------>执行完毕')
    return 'over'

r = func(6)
print(r)
```

## 6.5、函数的作用域 LEGB

locals()	<!--函数的本地变量-->
enclosing     <!--嵌套-->
globals()	<!--全局-->
builtins	<!--内置-->

### 6.5.1、变量修改时的可变不可变类型

```python
number = 10    #全局变量


def func():
    global number    # 不可变类型变量修改全局变量需要的关键字。
    number += 10
    return number


r = func()
print(r)
```

```python
# 可变类型变量修改时不需要关键字global
books = ['红楼梦', '西游记', '水浒传', '新白娘子传奇']


def func():
    books.append('斩龙')
    print(books)


func()
print(books)
```

### 6.5.2、enclosing

内部变量---->外部变量---->全局变量

如果外层函数变量是可变类型时，不必加nonlocal也可以修改。

```python
a = 100

def func():
    a = 10
    b = 'good'
    c = [1, 2, 3, 4]

    def inner_func():
        a = 50
        print('I\'am inner func',a)

    inner_func()
    return a

r = func()
print(r)
```

```python
a = 100
b = 20


def func(b):
    a = 10

    def inner_func(c):
        nonlocal b    # 表示使用外层函数的变量。
        c = a + b + c
        b = c
        print('a':a)
        print('b':b)
        print(c)

    inner_func(5)


func(30)
```

```python
set1 = {1, 4, 6, 7}
set2 = {1, 3, 6, 8}


def func(set_1, set2):
    set2 = set_1 & set2   # 无法修改全局的set2


func(set1, set2)
print(set1, set2, sep='\n')
```

### 6.5.3、闭包

#### 6.5.3.1、特点

+ 一个函数中定义了另一个函数。

+ 内层函数使用了外层函数的变量。

+ 返回值是内层函数。
+ 延迟数据的回收。

```python
def outr_func(n):
    a = 10

    def inner_func():
        r = a + n
        print('r的值为：', n)
    return inner_func

result = outr_func(1)
print(result)
result()
```

### 6.5.4、函数作为参数

```python
def A():
    print('hello world')

def func(f):
    print('---->', f)
    f()
    print('----->over')

func(A)

def func2(f):
    print('------->start', f)

    def inner_func():
        print('-------> inner_func')
        f()

    print('-------->end')
    return inner_func

result = func2(A)
print(result)
result()
```

## 6.6、装饰器

+ 遵循闭包的概念。

+ 参数为函数。

```python
def func(f):
    print('----------->1')
    def wrapper():
        print('装饰前。。验证功能')  # 这个部分实现验证代码
        f()   
        print('装饰后。。')
    print('----------->2')
    return wrapper

@func  #调用了func函数  执行函数体的内容 加载内部函数   返回内部函数  buy_ticket接收了func的返回值
def buy_ticket():
    print('买票')

buy_ticket()
```

```python
def decorator(func):
    def wrapper():
        func()
        print('刷漆')
        print('铺地板')
        print('买家具')

    return wrapper


@decorator
def house():
    print('毛坯房')


house()
```

```python
def decorator(func):
    count = 0

    def warpper():
        nonlocal count
        func()
        count += 1
        print('第{}次调用{}函数'.format(count, func.__name__))

    return warpper


@decorator
def show():
    print('正在使用show函数。。。。')


for i in range(3):
    show()
```

### 6.6.1、装饰带参数函数

```python
def decorator(func):
    count = 0

    def warpper(*args, **kwargs):
        nonlocal count
        func(*args,**kwargs)
        count += 1
        print('第{}次调用{}函数'.format(count, func.__name__))

    return warpper


@decorator
def test(n):
    print('调用test函数。。。',n)

test(10)
```

### 6.6.2、装饰有返回值的函数

```python
flag = False


def decorator(func):
    def wrapper(*args, **kwargs):
        print('---->用户验证')
        r = func(*args, **kwargs)
        if r:
            return 'success'
        else:
            return 'fail'

    return wrapper


def login():
    global flag
    username = input('输入用户名')
    password = input('输入密码')
    if username == 'admin' and password == '123':
        flag = True
    else:
        flag = False


@decorator
def islogin():
    if flag:
        return True
    else:
        return False


login()
r = islogin()
print(r)
```

+ 买票前的登录验证

```python
islogin = False


def login():
    global islogin
    username = input('输入用户名:')
    password = input('输入密码:')
    if username == 'admin' and password == '123':
        islogin = True
        # return islogin
    return islogin


def login_required(func):
    def wrapper(*args, **kwargs):
        global islogin
        if islogin:
            func(*args, **kwargs)
        else:
            print('用户未登录，请登录：')
            f = login()
            if f:
                func(*args, **kwargs)

    return wrapper


@login_required
def buy_ticket():
    ticket = {'万达影城': ('哪吒', ['11:35 一号厅', '12:00 二号厅', '13:10 三号厅'])}
    for key, value in ticket.items():
        print('影院：', key)
        print('片名：', value[0])
        print('时间：')
        for i in value[1]:
            print(i)


buy_ticket()
```

### 6.6.3、装饰器带参数

+ 通过三层函数实现

```python
def decorator(number):
    print('------------>1')

    def decorator1(func):
        print('--------->2')

        def warpper(*args, **kwargs):
            print('--------->start')
            func(*args, **kwargs)
            print('---------->end')

        print('--------->3')
        return warpper

    print('--------->4')
    return decorator1


@decorator(10)
def show():
    print('---------- 调用show函数')


show()
```

### 6.6.4、多层装饰器

+ 离得近的先装饰

```python
def decorator1(func):
    def warpper(*args, **kwargs):
        func(*args, **kwargs)
        print('-------->1')
        print('--------->2')

    return warpper


def decorator2(func):
    def warpper(*args, **kwargs):
        func(*args, **kwargs)
        print('-------->3')
        print('--------->4')

    return warpper


@decorator2
@decorator1
def house():
    print('未装饰前')


house()
```

## 6.7、匿名函数

```python
list_1 = [('bob', 12), ('lucy', 14), ('jack', 18), ('lily', 22), ('jecy', 10), ('tom', 6)]
list_2 = sorted(list_1, key=lambda x: x[1], reverse=True)
print(list_2)

list_1.sort(key=lambda x: x[1], reverse=True)
print(list_1)
```

## 6.8、高阶函数

把函数作为参数的函数

### 6.8.1、sorted函数

```python
dict1 = {'张三': 90, '李四': 100, '王五': 98, '赵六': 92}
result = sorted(dict1.items(), key=lambda x: x[1])
dict1 = dict(result)
print(dict1)
```

### 6.8.2、map函数

```python
map1 = map(lambda x: x ** 2, [1, 2, 3, 4, 5])
print(map1)
print(list(map1))

names = ['tom', 'jack', 'lily', 'lucy']
map2 = map(lambda x: x.capitalize(), names)
print(list(map2))

map0 = map(lambda x, y: x + y, [1, 2, 3], [4, 5, 6, 7])
print(list(map0))
```

### 6.8.3、filter函数

Function 返回值必须是bool类型

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 0, 10]
filter1 = filter(lambda x: x % 2 == 0, numbers)
print(list(filter1))

filter2 = filter(None, numbers)
print(list(filter2))

list1 = ['hello', 20, '30', 'hi100', 70, 'yes', '99']
filter3 = filter(lambda x: isinstance(x, int) or x.isdigit(), list1)
print(list(filter3))

filter4 = filter(lambda x: str(x).isdigit(), list1)
print(list(filter4))

students = [('bob', 12), ('lucy', 14), ('jack', 18), ('lily', 22), ('jecy', 10), ('tom', 6)]
filter5 = filter(lambda x: x[1] > 10, students)
print(list(filter5))
```

### 6.8.4、reduce函数

Function 函数的参数必须为两个。

```python
from functools import reduce

list1 = [1, 2, 3, 4]
result = reduce(lambda x, y: x + y, list1)
print(result)

list1 = [1, 2, 3, 4]
result = reduce(lambda x, y: x + y, list1, 10)
print(result)
```

### 6.8.5、偏函数partial

固定函数中的部分参数。

```python
from functools import partial, wraps

r = int('123', base=8)
print(r)

r = int('001010101', base=2)
print(r)

int1 = partial(int, base=8)
print(int1('123'))
print(int1('16'))
print(int1('25'))
```

### 6.8.6、wraps

```python
from functools import wraps

def decorator(func):
    @wraps(func)
    def wrapper():
        func()
        print('刷漆')
        print('铺地板')
        print('买家具')

    return wrapper


@decorator
def house():
    print('毛坯房')

print(house.__name__)
house()
```

## 6.9、列表推导式

+ 格式 [表达式 for i in Iterrable if 条件]
+ 格式[表达式1 if 条件 else 表达式2 for i in Iterrable]
+ 格式[表达式 for i in Iterrable for i in Iterrable]

```python
list1 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
list2 = [x for x in list1 if x % 2 == 0]
print(list2)

list3 = [x + 1 if x % 2 == 0 else x + 2 for x in list1]
print(list3)

list0 = [1, 2, 3]
list4 = [(x, y) for x in list1 for y in list0]
print(list4)
```

## 6.10、字典推导式

```python
dict1 = {'a': 1, 'b': 2, 'c': 3}
dict2 = {v: k for k, v in dict1.items()}
print(dict2)

foods = ['火锅', '茶', '麻辣香锅', '红烧肉']
dict3 = {index + 1: value for index, value in enumerate(foods)}
print(dict3)
```

## 6.11、生成器

### 6.11.1、产生方式

+ 推导式使用的符号（）

+ 函数+yield

  定义函数+关键字：yield

  调用函数生成一个生成器对象。执行时会在yield处暂停，下一次调用时从yield后面开始。

  函数有返回值时，返回值的内容会作为生成器完成后报错的内容。

```python
import types
from collections.abc import Iterable, Iterator

g = (x for x in range(2000))
print(type(g))
print(next(g))
print(next(g, 10))  # 迭代完之后如果不想报错可以赋值一个默认值。
print(g)

r = range(5)
print(isinstance(r, Iterator))
print(isinstance(r, Iterable))
print(isinstance(r, types.GeneratorType))
```

```python
list_1 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
g = (x for x in list_1 if x % 2 == 0)
print(next(g))


def func():
    for i in range(5):
        yield i
        print('------------>', i)


g1 = func()
print(g1)
print(next(g1))
print(next(g1))
print(next(g1))
print(next(g1))
print(g1.__next__())
g1.close()		#关闭生成器
print(next(g1))
```

### 6.11.2、send函数

```python
def func():
    sum =0
    for i in range(5):
        x = yield i
        sum += (x +i)
        print('sum------->', sum)


g1 = func()
g1.send(None)   #第一次传值的时候由于yield的暂停特性，所以无结果
g1.send(8)
g1.send(10)
```

```python
def func(n):
    a,b=1,1
    for i in range(n):
        yield a
        a,b=b,a+b
    return 'over'
g= func(5)
print(g.__next__())
print(g.__next__())
print(g.__next__())
print(g.__next__())
print(g.__next__())
```

### 6.11.3、应用

```python
def study():
    for i in range(5):
        print('学习中', i)
        yield


def Listen_music():
    for i in range(5):
        print('听歌中', i)
        yield


def weichat():
    for i in range(5):
        print('聊天中', i)
        yield


g1 = study()
g2 = Listen_music()
g3 = weichat()
while True:
    try:
        print(g1.__next__())
        print(g2.__next__())
        print(g3.__next__())
    except:
        pass
```

### 6.11.4、关系

可迭代的不一定为迭代器，可能为字符串、列表、字典、元组等。但是可以通过iter函数转换为迭代器。

生成器既是迭代器也是可迭代对象。

## 6.12、递归函数

函数的定义中调用自身。

定义时需要确定函数的出口。

```python
count = 10


def func():
    global count
    if count < 100:
        print('-------->', count)
        count += 1
        func()


func()
```

```python
count = 5


def get_sum(n):
    global count
    if n == count:
        return count
    else:
        return get_sum(n + 1)


s = get_sum(1)
print('------>', s)
```

### 6.12.1、递归求累加和

```python
count = 10   #终点


def get_sum(n):
    global count
    if n == count:
        return count
    else:
        return n + get_sum(n + 1)


s = get_sum(1)	#n为起点
print('------>', s)
```

### 6.12.2、斐波那契数列

```python
list1 = []


def func(n):
    for i in range(n):
        if i == 1 or i == 0:
            list1.append(1)
        else:
            list1.append(list1[i - 1] + list1[i - 2])


func(8)
print(list1)
```

```python
list1 = []


def func(n):
    if n == 1 or n == 2:
        return 1
    else:
        return func(n - 1) + func(n - 2)


print(func(8))
list2 = []
for i in range(1, 9):
    list2.append(func(i))
print(list2)
```

# 7、面向对象

## 7.1、类和对象的关系

```
对象：
    世间万物皆对象，对象是具体的某个人或事物。
    对象的集合----->具有共同特征、动作----->可归为类
```

定义示例

```python
class Book:
    bname = '红楼梦'
    author = '曹雪芹'
    publish = '人民教育出版社'
    price = 100
    bno = '78263478'

    def reduce_book(self):
        print('借出一本书')

    def increase_book(self):
        print('还回一本书')

class Students:
    name = '张三'
    sno = '001'
    clazz  = '三年一班'
    books  = []
    age = 18
    gender = 'man'

    def sign_in(self):
        print('打卡')

    def study(self):
        print('学习中')
```

## 7.2、属性

```python
class Book:
    bname = '盗墓笔记'
    price = 30


print(Book)
print(Book.bname)

b1 = Book()
print(b1.bname)

b2 = Book()
print(b2.bname)

Book.bname = '鬼吹灯'
print(b1.bname)
print(b2.bname)

b1.bname = '寻龙诀'
b2.bname = '怒晴湘西'
print(b1.bname)
print(b2.bname)
print(Book.bname)
```

## 7.3、方法

### 7.3.1、普通方法

```python
class Card:
    def __init__(self, cardno):
        self.money = 0
        self.cardno = cardno

    def save_money(self):
        print('存钱成功')

    def withdraw_money(self):
        print('取钱成功')


card1 = Card('12345678')
card1.save_money()
card1.withdraw_money()
```

```python
class Card:
    def __init__(self, cardno,phone):
        self.money = 0
        self.cardno = cardno
        self.phone = phone

    def save_money(self,money):
        self.money+=money
        # print('{}存钱成功,余额为：{}'.format(self.cardno,self.money))
        self.show()
    def withdraw_money(self,money):
        if money > self.money:
            print('余额不足')
        else:
            self.money-=money
            print('{}取钱成功,余额为：{}'.format(self.cardno,self.money))
    def show(self):
        print('用户的详细信息：')
        print('卡号是：{}，余额是：{}，手机号码是：{}'.format(self.cardno,self.money,self.phone))

card1 = Card('12345678',12345678901)
card1.save_money(10000)
card1.withdraw_money(5000)
card1.show()
```

### 7.3.2、类方法

```python
class Utils:

    version = '1.0'
    @classmethod
    def conn_db(cls):
        print('加载数据库驱动程序')
        print('版本是：',cls.version)
        print('数据库建立连接成功！')

    def select_data(self):
        pass
Utils.conn_db()

u = Utils()
u.conn_db()		#对象调用类方法。cls还是类本身
```

### 7.3.3、静态方法

```python
class Utils:
    version = '1.0'

    @staticmethod
    def select_data():
        print('查询数据库的数据部分，当前版本号是:', Utils.version)


Utils.select_data()

u = Utils()
u.select_data()
```

### 7.3.4、魔术方法

系统自动调用的方法称为魔术方法。

#### 7.3.4.1、初始化魔术方法，\__init__

```python
class Student:
    def __init__(self,name,age,gender):
        self.name = name
        self.age = age
        self.gender = gender

s1 = Student('bob',19,'man')
print(s1.name,s1.age,s1.gender)
```

#### 7.3.4.2、实例化魔术方法,\__new__

```python
class Person:
    def __init__(self):
        print('------->init')

    def __new__(cls, *args, **kwargs):
        print('--------->new')
        return object.__new__(cls)


p = Person()
print(p)
```

#### 7.3.4.3、析构魔术方法，\__del__回收空间

```python
class Person:
    def __init__(self):
        print('------->init')

    def __new__(cls, *args, **kwargs):
        print('--------->new')
        return object.__new__(cls)

    def __del__(self):
        print('--------->del')

p = Person()
# p3 = p 
del p  # 删除地址的引用

p1 = Person()

p2 = Person()
```

#### 7.3.4.4、\_\_str\_\_,\__repr__,将对象转成字符串的表示形式。

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        return self.name + '\t' + str(self.age)

    def __repr__(self):
        return 'hello world'


p = Person('zhangsan', 20)
print(p)

r = repr(p)
print(r)
```

#### 7.3.4.5、\__call__

```python
class Person:

    def __call__(self, *args, **kwargs):
        print('我被调用了')


p = Person()
print(p)
p()
```

#### 7.3.4.6、运算相关魔术方法

gt lt ge ne le eq 

```python
class Cat:
    def __init__(self,nickname,age):
        self.nickname = nickname
        self.age = age

    def __gt__(self, other):
        return self.age > other.age

    def __eq__(self, other):
        return self.age == other.age

c1 = Cat('花花',2)
c2 = Cat('小黄',2)

print(c1 == c2)
```

```python
class Cat:
    def __init__(self,nickname,age):
        self.nickname = nickname
        self.age = age

    def __gt__(self, other):
        return self.age > other.age

    def __eq__(self, other):
        return self.age == other.age

    def __add__(self, other):
        return self.age + other.age

c1 = Cat('花花',2)
c2 = Cat('小黄',2)

print(c1 == c2)
result = c1 + c2
print(result)
```

#### 7.3.4.7、\_\_getattr__,\_\_setattr\_\_

访问不存在的属性时触发。

```python
class Person:
    def __init__(self,name):
        self.name  = name

    def __getattr__(self, item):
        if item == 'age':
            return 20
        elif item == 'gender':
            return '男'
        else:
            return '不存在此属性。'

    def __setattr__(self, key, value):      # 赋值之前会执行此动作，但是所赋值并未保存
        print(key,value)
				object.__setattr__(self,key,value)  # 真正赋值的操作

p1 = Person('bob')
print(p1.age)
print(p1.name)
p1.phone =110
print(p1.__dict__)
```

## 7.4、私有化

只能在类体中访问。外界不能访问。

类属性访问需**self** 或者**类名**来访问。

```python
class Person:
    def __init__(self,name):
        self.name  = name
        self.__age = 20

    def show_age(self):
        print('我的年龄是：',self.__age)

    def __test(self):
        print('我是私有化方法')

p = Person('john')
print(p._Person__age)		# 外界要访问被私有化的属性和方法时的格式
p.show_age()
p._Person__test()
```

## 7.5、封装

将属性私有化，并定义共有get、set方法。

```python
class Person:

    __slots__ = ['__name','__age','__flag'] # 限制外界对内部属性的赋值。

    def __init__(self,name,age):
        self.__name  = name
        self.__age = age
        self.__flag = False

    def get_name(self):
        if self.__flag:
            return self.__name
        else:
            return '没有权限查看'

    def get_age(self):
        return self.__age

    def set_name(self,name):
        if len(name) > 5:
            self.__name = name
        else:
            print('名字必须大于等于6位')

    def set_age(self,age):
        if 125>age>0:
            self.__age = age
        else:
            print('年龄不在规定范围0-125')

p = Person('tom',20)
print(p.get_name())
p.set_age(25)
print(p.get_age())
p.set_age(1000)
```

+ @property装饰器的使用，使函数的名称更加便捷。

```python
class Person:
    __slots__ = ['__name', '__age', '__flag', '__password']  # 限制外界对内部属性的赋值。

    def __init__(self, name, age, password):
        self.__name = name
        self.__age = age
        self.__flag = False
        self.__password = password

    @property
    def name(self):
        if self.__flag:
            return self.__name
        else:
            return '没有权限查看'

    def get_age(self):
        return self.__age

    @name.setter
    def name(self, name):
        if len(name) >= 6:
            self.__name = name
        else:
            print('名字必须大于等于6位')

    def set_age(self, age):
        if 125 > age > 0:
            self.__age = age
        else:
            print('年龄不在规定范围0-125')

    def login(self, name, password):
        if self.__name == name and self.__password == password:
            print('登陆成功！')
            self.__flag = True
        else:
            print('用户名或者密码错误！')


p = Person('tom', 20, '1234')
p.login('tom', '1234')

print(p.name)
p.name = 'steven'
print(p.name)
```

## 7.6、继承

### 7.6.1、单继承

+ 子类继承父类的非私有属性和方法。

+ 如果子类定义了和父类的相同一个属性，先找子类的属性。

+ 如果子类定义了和父类的相同一个方法，则认为是子类重写了父类的方法。<!--overwrite-->。重写时参数应该和父类保持一致！

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
        self.__money = 100000

    def eat(self):
        print('吃饭中！')

    def sleep(self):
        print('睡觉中！')


class Student(Person):
    def __init__(self):
        print('---------> student init')


class Teacher(Person):
    def show(self):
        print(self.name)
        print(self.age)
        # print(self.__money)


t = Teacher('tom', 25)
t.show()

s = Student()
s.eat()
s.sleep()
```

```python
class Animal:
    type1 = '动物'

    def __init__(self):
        self.name = '花花'
        print('------->Animal')

    def run(self):
        print('正在奔跑。。。')

    def eat(self):
        print('喜欢吃。。。')

    def __str__(self):
        print('当前类型：', self.type1)
        # print('当前类型：', Animal.type1)    #注意这两行的区别


class Dog(Animal):
    type1 = '狗'

    def __init__(self, name, color):
        Animal.__init__(self)  # 继承父类的init，注意此处的前后关系
        self.name = name
        self.color = color
        # Animal.__init__(self)

    def see_home(self):
        print('看家高手！')

    def eat(self):
        print('喜欢吃骨头')


d = Dog('大黄', '黄色')
print(d.name)
d.run()
d.eat()
d.__str__()
```

+ 子类调用父类的方法

```python
class Father:
    def __init__(self):
        self.a = 10
        self.b = 8

    def show(self):
        r = self.a + self.b
        print('运算结果是：', r)

    def func(self, n):
        print('父类的func方法', n)


class Son(Father):
    def __init__(self):
        Father.__init__(self)
        self.a = 100

    def show(self):
        r = self.a + self.b
        print('运算结果是：', r)

    def func(self, n):
        # Father.func(self,n)
        super(Son, self).func(n)    # 调用父类的两种方法
        print('----------->son')


s = Son()
s.show()
s.func(8)
```

### 7.6.2、多继承

+ 新式类<!--2.3版本后-->，C3算法，先深度找，后广度找。

```python
class Father1:
    def func(self):
        print('--------->father1 func1')


class Father2:
    def func(self):
        print('--------->father2 func1')


class Father3:
    def func(self):
        print('--------->father3 func1')


class Son(Father1, Father2, Father3):
    pass


s = Son()
s.func()
print(Son.__mro__)	# 查看继承的顺序的一个方法
```

### 7.6.3、继承之包含关系

+ 对象之间的比较

```python
class A:
    def __init__(self,a,b):
        self.a = a
        self.b = b

    def __eq__(self, other):
        if self is other:
            print('=======>')
            return True
        else:
            if self.a == other.a and self.b == other.b:
                return True
            return False

a1 = A(5,'abc')
a2 = A(5,'abc')
a3 = a2

print(a1 == a2)
print(a2 == a3)
```

+ Has a 一个类中使用了另一个自定义类型。

```python
class Computer:
    def __init__(self, brand, type, color):
        self.brand = brand
        self.type = type
        self.color = color

    def online(self):
        print('正在使用电脑上网。。。')

    def __str__(self):
        return self.brand + '---' + self.type + '----' + self.color


class Book:
    def __init__(self, bname, author, number):
        self.bname = bname
        self.author = author
        self.number = number

    def __str__(self):
        return self.bname + '-----' + self.author + '-----' + str(self.number)


class Student:
    def __init__(self, name, computer, book):
        self.name = name
        self.computer = computer
        self.books = []
        self.books.append(book)

    def borrow_book(self, book):
        for bookn in self.books:
            if bookn.bname == book.bname:
                print('已经借过此书！')
                break
        else:
            self.books.append(book)
            print('添加成功！')

    def show_book(self):
        for book in self.books:
            print(book.bname)

    def __str__(self):
        return self.name + '---' + str(self.computer) + '----' + str(self.books)


c = Computer('mac', 'mac pro', '深灰色')
book1 = Book('盗墓笔记', '南派三叔', 10)
s = Student('songsong', c, book1)
print(s)
s.show_book()
print('*' * 50)
book2 = Book('鬼吹灯', '天下霸唱', 5)
s.borrow_book(book2)
s.show_book()
```

```python
import random


class Road:
    def __init__(self, name, len):
        self.name = name
        self.len = len


class Car:
    def __init__(self, brand, speed):
        self.brand = brand
        self.speed = speed

    def get_time(self, road):
        ran_time = random.randint(1, 10)
        msg = '{}品牌的车在{}上以{}速度行驶{}小时'.format(self.brand, road.name, self.speed, ran_time)
        print(msg)

    def __str__(self):
        return '{}品牌的，速度:{}'.format(self.brand, self.speed)


r = Road('京藏高速', 12000)

audi = Car('奥迪', 120)
print(audi)
r.name = '京哈高速'
audi.get_time(r)
```

+ Super 调用，is a

```python
class Person:
    def __init__(self, no, name, salary):
        self.no = no
        self.name = name
        self.salary = salary

    def __str__(self):
        msg = '工号：{}，姓名：{}，本月工资：{}'.format(self.no, self.name, self.salary)
        return msg

    def getSalary(self):
        return self.salary


class Worker(Person):
    def __init__(self, no, name, salary, hours, per_hour):
        # super().__init__(no,name,salary)		# 两种不同的形式
        super(Worker, self).__init__(no, name, salary)
        self.hours = hours
        self.per_hour = per_hour


w = Worker(123, 'tom', 123456, 10, 10000)
print(w.salary)
```

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def eat(self):
        print(self.name + '正在吃饭。。。')

    def run(self):
        print(self.name + '正在跑步。。。')


class Student(Person):
    def __init__(self, name, age, clazz):
        super().__init__(name, age)
        self.clazz = clazz


class Employee(Person):
    def __init__(self, name, age, salary, manager):
        super().__init__(name, age)
        self.salary = salary
        self.manager = manager


class Doctor(Person):
    def __init__(self, name, age, patients):
        super().__init__(name, age)
        self.patients = patients


s = Student('jack', 18, 'python1905')
e = Employee('tom', 23, 20000, 'king')
lists = ['zhangsan', 'lisi', 'wangwu']
d = Doctor('lucy', 25, lists)

s.run()
e.run()
d.run()
```

## 7.7、多态

```python
class PetShop:
    def __init__(self, name):
        self.name = name
        self.pets = []

    def save_pet(self, pet):
        # print(issubclass(Cat,Pet))  # 判断一个类是否是另元组中类的的子类，Pet为一个元组中的一个值。
        # print(isinstance(pet, Pet))
        if isinstance(pet, Pet):
            self.pets.append(pet)
            print('宠物增加,{}'.format(pet.pname))
        else:
            print('不是宠物，不收留')

    def sale_pet(self, pet):
        if isinstance(pet, Pet):
            self.pets.remove(pet)
        else:
            print('宠物减少')

    def search_pet(self, pname):
        for pet in self.pets:
            if pname == pet.pname:
                print('宠物在商店里！')
                break
        else:
            print('不存在此宠物！')

    def all_pets(self):
        print('所有宠物如下：')
        for pet in self.pets:
            print(pet)


class Pet:
    type = '宠物'

    def __init__(self, pname, color, age):
        self.pname = pname
        self.color = color
        self.age = age

    def __str__(self):
        return '当前类型是：{}，宠物名：{}'.format(self.type, self.pname)


class Dog(Pet):
    type = '狗'

    def see_home(self):
        print('特长看家')


class Cat(Pet):
    type = '猫'

    def catch_mouse(self):
        print('特长抓老鼠')


shop = PetShop('爱宠')
cat = Cat('花花', '黄色', 3)
shop.save_pet(cat)
dog = Dog('大黄', '棕色', 3)
shop.save_pet(dog)
shop.all_pets()
shop.search_pet('花花')
```

## 7.8、类装饰器

```python
class decorator:
    def __init__(self, args):
        self.args = args

    def __call__(self, *args, **kwargs):
        class inner_class:
            def __init__(self, func):
                self.func = func

            def __call__(self, *args, **kwargs):
                self.func(*args, **kwargs)
                print('化妆')
                print('变成小萝莉')

        return inner_class(*args)


@decorator(5)
def girl():
    print('俺是大妈')


girl()
```

```python
class logger(object):
    def __init__(self, level='INFO'):
        self.level = level

    def __call__(self, func):
        def wrapper(*args, **kwargs):
            print("[{level}]: the function {func}() is running...".format(level=self.level, func=func.__name__))
            func(*args, **kwargs)

        return wrapper


@logger(level="WARNING")
def say(something):
    print("say {}!".format(something))


say("hello")
```

## 7.9、元类type

```python
# class Student:
#     '''
#     学生类的文档
#     '''
#
#     type1 = '学生'
#     def __init__(self,name):
#         self.name =name
#
# s = Student('tom')
# print(Student.__dict__)
'''
这两段代码等效
'''

Student = type('Student',(object,),{'type1':'学生类'})
print(type(Student))
s= Student()
print(s)
```

```python
class ListMetaclass(type):
    def __new__(cls, name, bases, attrs):
        print(name)
        print(bases)
        print(attrs)
        attrs['b'] = 'world'
        # if attrs.get('test'):
        #     attrs.pop('test')
        return type.__new__(cls, name, bases, attrs)


class MyList(object, metaclass=ListMetaclass):
    a = 'hello'

    def test(self):
        print('------>test')


l = MyList()

print(l.a, l.b)
l.test()
```

## 7.10、单例

使多个对象使用同一个地址

```python
class Singleton:
    #   私有化
    __instance = None

    def __new__(cls):
        if cls.__instance is None:
            cls.__instance = object.__new__(cls)
            return cls.__instance
        else:
            return cls.__instance


s = Singleton()
s1 = Singleton()

print(dir(Singleton))
print(s)
print(s1)
```

### 7.10.1、类装饰器实现单例

```python
class cls_decorator:
    def __init__(self, f):
        self.f = f
        self.__instance = {}

    def __call__(self, *args, **kwargs):
        if self.f not in self.__instance:
            self.__instance[self.f] = self.f()  # self.f =Singleton
        return self.__instance[self.f]  # Singleton绑定一个地址


@cls_decorator
class Singleton:
    def __init__(self):
        print('------>singleton init')


s = Singleton()
s1 = Singleton()
print(s is s1)
```

### 7.10.2、函数装饰器实现单例

```python
def func_decorator(cls):
    _instance = {}

    def wrapper(*args, **kwargs):
        if cls not in _instance:
            _instance[cls] = cls()
        return _instance[cls]

    return wrapper


@func_decorator
class Singleton:
    def __init__(self):
        print('------>singleton init')


s = Singleton()
s1 = Singleton()
print(s is s1)
```

## 7.11、异常

### 7.11.1、Try  except else finally

```python
def chu(a, b):
    try:
        a = int(a)
        b = int(b)  # t = TypeError   raise
        return a / b
    except TypeError:  # isinstance(obj,TypeError)
        print('类型出错了！')
    except ZeroDivisionError:
        print('除数为零')
    except Exception as err:
        print('----->出错了', err)


r = chu(10, 'a')
print(r)
```

### 7.11.2、常用于结尾的资源释放，文件关闭，数据库连接断开。

```python
def func(opr):
    result = 0
    try:
        n1 = int(input('输入第一个数：'))
        n2 = int(input('输入第一个数：'))
        if opr == '+':
            print('加法运算')
            result = n1 + n2
        elif opr == '-':
            print('减法运算')
            result = n1 - n2
        elif opr == '*':
            print('乘法运算')
            result = n1 * n2
        elif opr == '/':
            print('除法运算')
            result = n1 / n2
        return result		# return在前，后面的else不执行
    except Exception as err:
        print('出错了！', err)

    # else:
    #     print('正确执行！')
    #     return result

    finally:
        print('------->')
        return result + 1


r = func('+')
print(r)
```

### 7.11.3、异常的传递

```python
import random

sum = 0


def func(list1):
    global sum
    for a in list1:
        print(a)
        sum += a


def get_random():
    try:
        list1 = []
        for i in range(5):
            ran = random.randint(1, 10)
            list1.append(ran)
            list1.append('9')

        func(list1)
    except Exception as err:
        print('--------->', err)


try:
    get_random()
except Exception as err:
    print(err)
```

### 7.11.4、自定义异常

```python
class UserNameError(Exception):
    def __init__(self, *args, **kwargs):
        pass


class PasswordError(Exception):
    def __init__(self, *args, **kwargs):
        pass


def register():
    username = input('请输入用户名:')
    if len(username) < 6 or username[0].isdigit():
        raise UserNameError('用户名格式错误！')
    else:
        print('用户名合法')
        password = input('输入密码：')
        if 10 >= len(password) >= 6:
            print('注册成功！')
        else:
            raise PasswordError('密码格式错误！')


try:
    register()
except Exception as err:
    print(err)
```

# 8、模块

<!--模块是一个包含所有你定义的函数和变量的文件，其后缀名是.py。模块可以被别的程序引入，以使用该模块中的函数等功能。这也是使用 python 标准库的方法。-->

## 8.1、导入方法：

`import，from ... import ...,from ... import *`

## 8.2、\__name__属性

一个模块被另一个程序第一次引入时，其主程序将运行。如果我们想在模块被引入时，模块中的某一程序块不执行，我们可以用__name__属性来使该程序块仅在该模块自身运行时执行。

```python
if __name__ == '__main__':
   print('程序自身在运行')
else:
   print('我来自另一模块')
```

## 8.3、包

可以认为是是一个文件夹+\__init__文件。

比如一个模块的名称是 A.B， 那么他表示一个包 A中的子模块 B 。

同包之间的引用也要使用包名来导入。

作用：

​	组织模块。

​	避免模块名冲突。

\__all__对*有限制作用。

```python
__all__ = ["echo", "surround", "reverse"]  #位于第一行
```

如果\__init__中没有任何内容，from 包名 import *  ，并无任何意义。

避免循环导入。

# 9、文件

作用：持久化数据

## 9.1、语法

### 9.1.1、open() 

会返回一个 file 对象，基本语法格式如下:

```python
open(filename, mode)
```

- filename：包含了你要访问的文件名称的字符串值。
- mode：决定了打开文件的模式：只读，写入，追加等。所有可取值见如下的完全列表。这个参数是非强制的，默认文件访问模式为只读(r)。
- Encodeing 可以指定文件编码以避免乱码。

不同模式打开文件的完全列表：

| 模式 | 描述                                                         |
| :--- | :----------------------------------------------------------- |
| r    | 以只读方式打开文件。文件的指针将会放在文件的开头。这是默认模式。 |
| rb   | 以二进制格式打开一个文件用于只读。文件指针将会放在文件的开头。 |
| r+   | 打开一个文件用于读写。文件指针将会放在文件的开头。           |
| rb+  | 以二进制格式打开一个文件用于读写。文件指针将会放在文件的开头。 |
| w    | 打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。 |
| wb   | 以二进制格式打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。 |
| w+   | 打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。 |
| wb+  | 以二进制格式打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。 |
| a    | 打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。 |
| ab   | 以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。 |
| a+   | 打开一个文件用于读写。如果该文件已存在，文件指针将会放在文件的结尾。文件打开时会是追加模式。如果该文件不存在，创建新文件用于读写。 |
| ab+  | 以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。如果该文件不存在，创建新文件用于读写。 |

|    模式    |  r   |  r+  |  w   |  w+  |  a   |  a+  |
| :--------: | :--: | :--: | :--: | :--: | :--: | :--: |
|     读     |  +   |  +   |      |  +   |      |  +   |
|     写     |      |  +   |  +   |  +   |  +   |  +   |
|    创建    |      |      |  +   |  +   |  +   |  +   |
|    覆盖    |      |      |  +   |  +   |      |      |
| 指针在开始 |  +   |  +   |  +   |  +   |      |      |
| 指针在结尾 |      |      |      |      |  +   |  +   |

### 9.1.2、文件对象的方法

#### 9.1.2.1、f.read()  f.readline() f.readlines()  f.close()

为了读取一个文件的内容，调用 f.read(size), 这将读取一定数目的数据, 然后作为字符串或字节对象返回。

size 是一个可选的数字类型的参数。 当 size 被忽略了或者为负, 那么该文件的所有内容都将被读取并且返回。

```python
##    文件复制

with open('/Users/muchengxin/Documents/斯佳丽-1.jpg','rb') as stream:
    container = stream.read()
    with open('/Users/muchengxin/Documents/相关软件包/test.jpg','wb') as wstream:
        wstream.write(container)

print('文件复制完成！')
```

#### 9.1.2.2、f.write()

```python
with open('user.txt','w') as wstream:
    wstream.write('明天就是周末了，是个好天气。。。')
    wstream.writelines(['hello\n', 'world\n'])
```

#### 9.1.2.3、持久化账户密码案例

```python
class User:
    def __init__(self):
        self.username = None
        self.password = None

    def __str__(self):
        return self.username

    def login(self):
        self.username = input('输入用户名：')
        self.password = input('输入密码：')
        if self.username and self.password:
            with open('user.txt', 'r') as rs:
                users = rs.readlines()
                for user in users:
                    user = user.replace('\n', '')
                    ulist = user.split(' ')
                    if self.username == ulist[0] and self.password == ulist[1]:
                        print('登录成功！')
                        return False
                else:
                    print('不存在此用户！')
        return True

    def register(self):
        self.username = input('输入用户名：')
        self.password = input('输入密码：')
        if self.username and self.password:
            with open('user.txt', 'a') as ws:
                ws.write('{} {}\n'.format(self.username, self.password))
            print('注册成功！')
            return True
        else:
            print('用户名或密码不能为空！')
            return True


def main():
    flag = True
    u = User()
    u.register()
    while flag:
        print('----------用户登录--------')
        flag = u.login()


if __name__ == '__main__':
    main()
```

### 9.1.3、csv格式处理

#### 9.1.3.1、列表写入,读取

```python
import csv


def write_csv(file_path):
    with open(file_path, 'w', newline='') as wstream:
        csv_write = csv.writer(wstream)
        csv_write.writerow(['001', 'xiaohua', 19])
        csv_write.writerow(['002', 'ergou', 20])
        csv_write.writerow(['003', 'lihua', 21])

def dictcsv_read():
    with open(file_path,'r',newline='') as rstream:
        csv_reader = csv.reader(rstream)
        for row in csv_reader:
            print(row)

file_path = r'/Users/muchengxin/Desktop/practice/seconde_demo/user.csv'
write_csv(file_path)
```

#### 9.1.3.2、字典写入,读取

```python
import csv


def dictwrite_csv(file_path):
    with open(file_path, 'w', newline='') as wstream:
        fieldname = ['sno', 'sname', 'age']
        csv_write = csv.DictWriter(wstream, fieldname)
        csv_write.writeheader()
        csv_write.writerow({'sno': '001', 'sname': 'xiaohua', 'age': 19})
        csv_write.writerow({'sno': '002', 'sname': 'ergou', 'age': 20})
        csv_write.writerow({'sno': '003', 'sname': 'lihua', 'age': 18})

        
def dictcsv_read():
    with open(file_path,'r',newline='') as rstream:
        csv_reader = csv.DictReader(rstream)
        for row in csv_reader:
            print(row)       

file_path = r'/Users/muchengxin/Desktop/practice/seconde_demo/user.csv'
dictwrite_csv(file_path)
```

### 9.1.4、序列化

#### 9.1.4.1、json格式

处理后会转成字符串。

```python
import json

dict1 = {'yiban': [
    {'sno': '001', 'sname': 'xiaohua', 'age': 19},
    {'sno': '002', 'sname': 'ergou', 'age': 18},
    {'sno': '003', 'sname': 'xiaoming', 'age': 20}
],
    'erban': [
        {'sno': '004', 'sname': 'xiaohua1', 'age': 19},
        {'sno': '005', 'sname': 'ergou1', 'age': 20},
        {'sno': '006', 'sname': 'xiaoming1', 'age': 22}
    ]
}
result = json.dumps(dict1)
print(result)
print(type(result))

r = json.loads(result)
print(r)
studens = r.get('yiban')
for student in studens:
    if 'xiaohua' == student.get('sname'):
        print('找到了')
        break
else:
    print('查无此人！')

with open('student.txt', 'w') as wstream:
    json.dump(dict1, wstream)
print('保存成功！')

with open('student.txt', 'r') as rstream:
    content = json.load(rstream)
    print(content)
    students = content.get('erban')
    print(students)
```

#### 9.1.4.2、pickle

##### 9.1.4.2.1、处理后会转成字节串。

```python
import pickle

dict1 = {'yiban': [
    {'sno': '001', 'sname': 'xiaohua', 'age': 19},
    {'sno': '002', 'sname': 'ergou', 'age': 18},
    {'sno': '003', 'sname': 'xiaoming', 'age': 20}
],
    'erban': [
        {'sno': '004', 'sname': 'xiaohua1', 'age': 19},
        {'sno': '005', 'sname': 'ergou1', 'age': 20},
        {'sno': '006', 'sname': 'xiaoming1', 'age': 22}
    ]
}

result = pickle.dumps(dict1)
print(result)

r = pickle.loads(result)
print(r)

-----------------------------------------------------------------------------------
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        return self.name


stu = Student('xiaohua', 20)
stu1 = Student('ergou', 21)
bobj = pickle.dumps(stu)
print(bobj)

stu = pickle.loads(bobj)
print(stu)

with open('stus.txt', 'wb') as ws:
    pickle.dump(stu, ws)
    pickle.dump(stu1, ws)

with open('stus.txt', 'rb') as rs:
    while True:
        try:
            content = pickle.load(rs)
            print(content)
        except:
            break
```

##### 9.1.4.2.2、对象的序列化

```python
import pickle
from datetime import datetime


class User:
    def __init__(self):
        self.name = None
        self.password = None
        self.status = None
        self.login_time = None

    def login(self, name, password):
        with open('users.txt', 'rb') as rs:
            flag = False
            user_list = []
            while True:
                try:
                    user = pickle.load(rs)
                    user_list.append(user)
                    if user.name == name and user.password == password:
                        self.name = name
                        self.password = password
                        self.status = user.status
                        if user.status:
                            print('用户已登录！')

                            self.login_time = user.login_time
                            break
                        else:
                            print('登录成功！')
                            user.status = True
                            self.login_time = datetime.now()
                            flag = True


                except:
                    print('用户名或者密码有误！')
                    break

    def logout(self):
        pass

    def publish_comment(self):
        pass

    def register(self):
        pass

    def __str__(self):
        return self.name


def main():
    users = {
        'ergou': {'password': '123', 'status': False, 'login_time': '2019-07-01'},
        'songsong': {'password': '456', 'status': False, 'login_time': '2019-07-06'},
        'xiaohua': {'password': '123456', 'status': True, 'login_time': '2019-06-01'},
    }

    user_objs = []
    for key, value in users.items():
        u = User()
        u.name = key
        for k1, v1 in value.items():
            if k1 == 'password':
                u.password = v1
            elif k1 == 'status':
                u.status = v1
            elif k1 == 'login_time':
                u.login_time = v1

        user_objs.append(u)
    with open('users.txt', 'wb') as ws:
        for user in user_objs:
            pickle.dump(user, ws)
    print('保存成功！')


if __name__ == '__main__':
    main()
    u = User()
    u.login('xiaohua','123456')
```

# 10、常见模块

## 10.1、builtins

## 10.2、os模块

```python
import os, shutil
from os import path

print(os.name)
os.mkdir('test')
os.rmdir('test')  ## 无法删除非空文件夹
shutil.rmtree('test')  ## 可以删除非空文件夹
files = os.listdir('test')  # 列表形式返回结果，只能看到一层结果
os.getcwd()  # 获取当前路径
os.getpid()  # 获取当前进程pid
os.getppid()  # 获取当前进程父进程pid
os.chdir('/home')  # 改变当前路劲到指定位置

print(path.abspath('user.txt'))  # 获取绝对路径
print(path.isabs('user.txt'))  # 判断是否绝对路径
print(path.isfile('user.txt'))  # 判断是否文件
print(path.isdir('user.txt'))  # 判断是否文件夹
print(path.exists('user.txt'))
print(path.join('/Users/muchengxin/Desktop/practice/seconde_demo', 'user.txt'))  # 路径拼接
print(path.split('/Users/muchengxin/Desktop/practice/seconde_demo/user.txt'))  # 获取文件名
print(path.getsize('user.txt'))
path.getatime('user.txt')  # 获取访问时间
path.getctime('user.txt')  # 获取创建时间
path.getmtime('user.txt')  # 获取修改时间
```

## 10.3、time模块

```python
import time, datetime, sys

# from datetime import datetime,date

print(time.time())
st = time.localtime(1593743731.367729)  # 输出结果为一个元祖
print(st.tm_mday)
print(time.asctime(st))  # Fri Jul  3 10:35:31 2020
print(time.strftime('%Y-%m-%d %H-%M-%S', st))  # 2020-07-03 10-35-31

'''
datetime 四个类
date    日期
time    时间
timedelta   时间差
datetime    日期时间

'''
print(datetime.datetime.now())
print(datetime.datetime.today())
print(datetime.datetime.utcnow())  # 时区相关

print(sys.path)
print(sys.version_info, sys.version)
#   改变查询路径的先后顺序
sys.path.insert(0,
                '/usr/local/Cellar/python/3.7.6_1/Frameworks/Python.framework/Versions/3.7/lib/python3.7/lib-dynload')
print(sys.path)

print(datetime.date.today())
# timedelta
t1 = datetime.datetime.now()
tdt = datetime.timedelta(minutes=30)
print(t1 + tdt)
tdt = datetime.timedelta(days=2)
print(t1 + tdt)
```

## 10.4、calendar模块

```python
import calendar

print(calendar.calendar(2020))
print(calendar.month(2020, 7))
print(calendar.weekday(2020, 7, 12))
```

## 10.5、random

```python
import random

print(random.randrange(1, 10))  # 不包含10
print(random.randint(1, 10))  # 包含10
print(random.random())  # 0-1之间小数，不包括1
print(random.choice('abcdefghigklmn'))
list1 = [1, 2, 3, 4, 5, 6, 7, 8, 9]
random.shuffle(list1)  # 随机排列有序序列
print(list1)
```

## 10.6、math

```python
import math

print(math.ceil(9.12))  # 向上取整
print(math.floor(9.12))  # 向下取整
print(math.fmod(10, 3))  # 取余
print(math.sqrt(9))  # 开方
print(math.pow(2, 5))  # 幂次
```

## 10.7、hashlib

### 10.7.1、引入

```python
import hashlib

md5 = hashlib.md5()
s = '123456'
md5.update(s.encode('utf-8'))
print(md5.digest())
print(md5.hexdigest())

sha1 = hashlib.sha1(s.encode('utf-8'))
print(sha1.hexdigest())
print(len(sha1.hexdigest()))

sha256 = hashlib.sha256(s.encode('utf-8'))
print(sha256.hexdigest())
print(len(sha256.hexdigest()))
```

### 10.7.2、用户密码加密保存

```python
import hashlib, csv


def register():
    username = input('用户名：')
    password = input('密码：')
    user = []
    user.append(username)
    user.append(hashlib.sha256(password.encode('utf-8')).hexdigest())
    with open('user.csv', 'a', newline='') as ws:
        csv_ws = csv.writer(ws)
        csv_ws.writerow(user)
    print('注册成功！')


def login():
    username = input('用户名：')
    password = input('密码：')
    password = hashlib.sha256(password.encode('utf-8')).hexdigest()
    with open('user.csv', 'r') as rs:
        csv_rs = csv.reader(rs)
        for user in csv_rs:
            if username == user[0] and password == user[1]:
                print('登录成功')
                break
        else:
            print('登录失败！')


if __name__ == '__main__':
    register()
    login()
```

## 10.8、logging 日志

```python
import logging

# 1.日志对象
logger = logging.getLogger()
# 2.设置级别
logger.setLevel(logging.ERROR)
# 3.创建handler对象
file = 'log.txt'
handler = logging.FileHandler(file)
handler.setLevel(logging.ERROR)

# 4.创建一个formater格式对象
fmt = logging.Formatter('%(asctime)s - %(module)s - %(filename)s[%(lineno)d] - %(levelname)s:%(message)s')
handler.setFormatter(fmt)

# 5.
logger.addHandler(handler)


def func():
    try:
        number = int(input('请输入一个数字：'))
        for i in range(number):
            print('----->', i)
    except:
        logger.error('输入的不是一个数字！')
    finally:
        print('over'.center(30, '*'))


if __name__ == '__main__':
    func()
```

## 10.9、urllib

```python
import urllib.request
import urllib.parse

header = {}
header[
    'User-Agent'] = 'Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.89 Mobile Safari/537.36'
# respones = urllib.request.urlopen('http://meitulu.92demo.com/item/4657.html')
# content = respones.read()
# print(content.decode('utf-8'))

url = 'http://meitulu.92demo.com/e/search/result/'
data = {}
data['searchid'] = 877
data = urllib.parse.urlencode(data).encode('utf-8')

# 创建请求对象
request = urllib.request.Request(url, data, header)
respones = urllib.request.urlopen(request)
content=respones.read()
print(content.decode('utf-8'))
```

# 11、正则

## 11.1、常用函数

### 11.1.1、函数语法：

```python
re.match(pattern, string, flags=0)
```

### 11.1.2、函数参数

| 参数    | 描述                                                         |
| :------ | :----------------------------------------------------------- |
| pattern | 匹配的正则表达式                                             |
| string  | 要匹配的字符串。                                             |
| flags   | 标志位，用于控制正则表达式的匹配方式，如：是否区分大小写，多行匹配等等 |

### 11.1.3、正则表达式修饰符 - 可选标志

| 修饰符 | 描述                                                         |
| :----- | :----------------------------------------------------------- |
| re.I   | 使匹配对大小写不敏感                                         |
| re.L   | 做本地化识别（locale-aware）匹配                             |
| re.M   | 多行匹配，影响 ^ 和 $                                        |
| re.S   | 使 . 匹配包括换行在内的所有字符                              |
| re.U   | 根据Unicode字符集解析字符。这个标志影响 \w, \W, \b, \B.      |
| re.X   | 该标志通过给予你更灵活的格式以便你将正则表达式写得更易于理解。 |

### 11.1.4、示例

```python
import re

# 通过complie函数，返回一个pattern对象
pattern = re.compile('abc')

match_obj = pattern.match('abcdefabcd')
print(match_obj)
# 匹配对象调用group获取匹配的内容
g = match_obj.group()
print(g)

match_obj = re.match('abc','AAABABcdef',re.I)  # 从头开始匹配，忽略大小写
print(match_obj)

match_obj = re.fullmatch('abc','ABCdef',re.I)   # 完全匹配
print(match_obj)

match_obj = re.search('abc','abcdefAABC',re.I)
print(match_obj)
print(match_obj.group(),match_obj.span(),match_obj.start())
```



































```bash
                                  .-.*_,
         ."". ."".               {*(,\}/___           .-"-,
         |   '   |              `;)@\*|"   `",     _.'     \
          \     / ."". ."".      '((/;        | .-'         |
           '. .'  |   '   |       *;-.=-=-=._ .;-,          |
             '     \     /       __;____..---/' -,\        ;
          _,__      '. .'  .--';`_     __.-'`    _/       /
    ,-..-"    "-,     '   /      `;---'__0       \      .'
   /,- \.        \-.     |        /   (__)       \   _.'
   \_       -   -   \   _.-,_     {              |-'`
    /       0 __0 7_/  ({\*,;)     \,'-`-'      /'
   |/        (__)  \   /*(@-'})  ___",_ __ __,;`.--,
    |/             |`  \_,'-;}`.'    `'."".,---.._, )
     \       \/   /'_.-"/ _`)  |`----'`(  (=       /`-._
     .="-,_ __ _,'`    /   ',  \    =";`--'\ '=.  /|   ()()
   __`;.____...-'\.    |     | /`     / |\  '.  `  |  ()@()*
 /`               '    |  ,_/.=\    .;  | |   '.   /-' -##@()
|    .--,          `y-'`\  ||   '--'/|  \ \    \`-`      ##)@()
\   /   `)          \    '.||_,.--'/ /  /  )   |/       *`(\A/)()
 '. | ,-'            }     ||     { |.'.| /_.'./    .-.* { >*< }*)
   `\__/        _..._|     |/     {           |    (\A/) @(/V\)()@()
     /`--....--`  . `}            /{          \___{ >*< }()()##()()*{
    {             ;  }           {  \        /  ###(/V\)()@()(####\)
    {            ,  }            |   `--.. ,'   #########()()#####
     \           _,'             {        /     <><><><><><><><><>
      `,_    ,--'{                \       |        |
       }     {   }                 ;      \        /
  jgs  /     }   {                  \      |      /
    ,-`      }    `-._           _.-'`     {      `-.
  .'     _,-``,       `\       .'          }        .`\
  \(_(_.'      `-.__)_)/       `.-'   _.-'`'-._      \'
                                 `"""`         `""""`
```