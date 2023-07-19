---
title: Visual Studio Code Python Debugging 偵錯 除錯 找錯誤 找問題 了解程式 方式 教學 
excerpt: >
  說明如何使用vs code python extension來除錯程式，找出問題點或了解程式。
categories:
  - Code Editor
tags:
  - Visual Studio Code
  - Tutorial
  - Python
  - Extension
  - Debugging
---
## 程式錯誤有哪些?
在了解怎麼找程式錯誤或問題之前，我們要先了解程式基本上有分成哪幾種錯誤類型?

常見的錯誤類型：
* Syntax errors
* Logic errors
* Semantic errors

我將以這支程式作為範例來說明上述三個error是什麼~

```python
num1 = input('Enter a number:')
num2 = input('Enter another number:'
sum = num1 - num2

print('The sum of', num1, 'and', num2, 'is', sum)
```
### Syntax errors
出現語法錯誤時，你在使用python直譯器執行你的程式碼的時候，直譯器就會直接告訴你哪邊錯誤~並且會說你SyntaxError~如下圖所示。

![python_syntax_error](/assets/images/python_syntax_error.png)

圖說可以看到她說檔案File sum.py檔案 第2列 程式碼出現 語法錯誤 少了括號~

語法錯誤通常是一開始學程式會先碰到的錯誤問題，他比較容易解決，因為你在使用直譯器執行時，直譯器就會判斷必告訴你可能的錯誤跟錯誤的地方在哪裡~簡單來說就是文法錯誤，所以直譯器根本就讀不懂才會知道你有錯~

### Logic errors
邏輯錯誤就是邏輯上不正確的錯誤，例如我要做兩數相加運算，程式上我卻寫兩數相乘，或是if-else, loop 的邏輯flow順序出錯，這種邏輯上的錯誤通常直譯器不會判斷出來，因為通常你文法沒有錯，即使有錯誤訊息也僅能參考，並不會直接指出你的邏輯錯誤點是什麼~

所以底下我們就把程式的*號改成+號吧~如下所示。

```python
num1 = input('Enter a number:')
num2 = input('Enter another number:')
sum = num1 + num2

print('The sum of', num1, 'and', num2, 'is', sum)
```

執行結果我輸入了2和3~結果如圖所示。

![python_Semantic_error](/assets/images/python_Semantic_error.png)

什麼答案竟然不是5，而且直譯器還沒有報錯的幫我執行完成了!!

### Semantic errors
語意錯誤~這是一個比較困難的錯誤問題，通常不僅你的文法沒錯，邏輯上你也沒錯，可是思考很久還是不知道哪邊出錯了，僅能靠debugging慢慢找出一些方向出來~

這邊就讓你新手們慢慢思考問題在哪等等下面會帶你怎麼找出錯誤~

## 開始除錯 找錯誤 找問題 Debug
當我們新手接手別人的專案或程式，天啊!上百行上千行的程式，學長姊突然丟給我要我解決錯誤問題~我可能看了天荒地老還找不出問題點~

利用debugging來協助加速找出錯誤：
1. 我們可以先從程式的結果來判斷程式可能出現的錯誤點在哪邊，對症下藥打中斷點~

```python
num1 = input('Enter a number:')
num2 = input('Enter another number:')
sum = num1 + num2

print('The sum of', num1, 'and', num2, 'is', sum)
```

就我們這隻程式為範例，我們已經輸入了數字2和數字3，他2+3計算完之後變成23，難道是電腦太笨了嗎?? 從他的結果23我們可以推斷，電腦雖然輸出結果不如我們預期的答案5，但是的確他正確接收了我們的數字2和數字3~

所以我們對症下藥下中斷，就可以下在第三列，得到數字2和數字3之後，這邊我想要講的是如果今天程式把幾百行，我們要從結果去推斷來讓程式執行時停在那邊看當前的結果是什麼~

程式執行時停止??

所謂的中斷點的意思就是我在某一列程式碼下中斷點紅圈圈之後，程式碼的執行順序都是由第一列開始執行依序執行第二列第三列~當他執行到第三列準備要執行第三列程式碼時，發現中斷點就會停止執行動作停下來!

所以有一個很重要的重點!!! 我下中斷點在第三列程式跑到中斷紅點後，直譯器執行完幾列程式了??

答案是第一跟第二列

第三列**還沒**執行!!!

如圖所示~

![python_find_error](/assets/images/python_find_error.png)

中斷點下的方式就是
1. 滑鼠游標移動到要下中斷點列的前面案左鍵
2. 打字閃爍游標移動到要下中斷點的列數按下快捷鍵F9

好了之後~我們按下快捷鍵F5開始執行與debugging，或是上方案Run->Starting Debugging。

如同我上面說的程式會執行中斷點之前的所有程式碼，所以這邊你一樣要在terminal輸入數字2和數字3，記得變數變因不要太常更換否則不容易除錯。

然後我們看到左邊出現變數內的數值如圖所示~

![python_varaible_number](/assets/images/python_varaible_number.png)

我們發現到它顯示num1: '2'是字串2 num2: '3'是字串3~

這時候我們就知道原來他是把字串做相加才會得到23~

正確的程式碼如下所示~
```python
num1 = input('Enter a number:')
num2 = input('Enter another number:')
sum = int(num1) + int(num2)

print('The sum of', num1, 'and', num2, 'is', sum)
```

debugging 還有其他功能接下來將繼續說明~

為了方便說明接下來的功能，我將程式修改成如下所示~
```python
def add(num1,num2):
    sum=int(num1)+int(num2)
    sum=int(num1)+int(num2)
    return sum

numa = 2
numb = 3

print('The sum is', add(numa,numb))
print('The sum is', add(numa,numb))
```

待續。。。
## 程式除錯練習題

這邊已經先假定你已經知道if-else、loop以及function是什麼~下面要講的程式不會有這些意義的解釋。請先準備好這些知識在往下看才會理解哦~

底下我將以這個程式碼來讓各位試著自己解決問題以及瞭解程式(善用debugging)，這隻程式碼主要在做的事情就是除法~[這位作者](https://github.com/geekcomputers/Python)把除法寫成函式，其中函式裡不使用/來做除法運算，那我們都知道除法也可以用多次減法來達到一樣的效果，如下所示~
```python
# Python3 program to divide a number 
# by other without using / operator 

# Function to find division without 
# using '/' operator 
def division(num1, num2): 
	
	if (num1 == 0): return 0
	if (num2 == 0): return INT_MAX 
	
	negResult = 0
	
	# Handling negative numbers 
	if (num1 < 0): 
		num1 = - num1 
		
		if (num2 < 0): 
			num2 = - num2 
		else: 
			negResult = true 
	# If num2 is negative, make it positive		
	elif (num2 < 0): 
		num2 = - num2 
		negResult = true 
	
	# if num1 is greater than equal to num2 
	# subtract num2 from num1 and increase 
	# quotient by one. 
	quotient = 0

	while (num1 >= num2): 
		num1 = num1 - num2 
	quotient += 1
	
	# checking if neg equals to 1 then 
	# making quotient negative 
	if (negResult): 
			quotient = - quotient 
	return quotient 

# Driver program 
num1 = input('Enter a number:')
num2 = input('Enter another number:')

# Pass num1, num2 as arguments to function division
print(division(num1, num2)
```

答案就試著自己去她的github找尋吧~搜尋能力也是基本能力哦~

## 結語
幫助想要快速上手程式語言的新手~