---
title: GitHub tonken 過期 怎麼辦 password authentication was removed
excerpt: >
  快速了解怎麼更新GitHub token。
categories:
  - GitHub
tags:
  - Visual Studio Code
  - GitHub
  - token
---
## 到GitHub首頁更新token

請到[此連結](https://github.com/settings/tokens)更新新的token。

以我為例我的token名稱是github pages，點選~

再點選regenerate token，設定好期限幾天~後按下update token~

他會給你一組token碼~把它複製下來~

## 到vs code git push
到vs code在終端機git push時會跳出視窗，要你輸入新的密碼，這時候接上你剛剛複製的token碼即可~

## 非vs code 手動更新 token
一樣我們點選左邊欄位的方塊圖，開啟extension搜尋。並在搜尋欄位輸入black。
![python_black_extension](/assets/images/python_black_extension.png)


安裝完之後重啟vs code，然後開啟python檔案，在空白處按下右鍵，Format Document With...選擇Black即可。

#### 使用formatting整理程式碼

按下快捷鍵shift+alt+f來整理程式碼。

整理程式之前
```
some_relatively_long_statement_on_input=True
another_relatively_long_statement_on_input=True

def some_function():
    if some_relatively_long_statement_on_input and another_relatively_long_statement_on_input:
        a=1
```


整理程式之後
```
some_relatively_long_statement_on_input = True
another_relatively_long_statement_on_input = True


def some_function():
    if (
        some_relatively_long_statement_on_input
        and another_relatively_long_statement_on_input
    ):
        a = 1
```

#### formatting的客製化設定

我們對於程式撰寫有一定基礎後，程式為了讓每個人容易閱讀，我們會用有規則的方式來命名變數或含函式名稱，也會限制我們每一行程式碼幾個字數。

根據[PEP 8 – Style Guide for Python Code](https://peps.python.org/pep-0008/)的說明python撰寫程式的共同規則提到，每一行程式限制79字元。

> Limit all lines to a maximum of 79 characters

而我們使用的Black也提到符合PEP 8的[規範](https://github.com/psf/black#the-black-code-style)~

> Black is a PEP 8 compliant opinionated formatter.

但字數我們可以設定的更完美~設定成每一行79個字元~

設定方式為File->Preference->Settings 搜尋black。

找到Black-formatter:Args
點選兩次Add Item，分別輸入--line-length和79，如圖所示。

![python_black_line_length](/assets/images/python_black_line_length.png)

Ref:
* [VSCode: Setting line lengths in the Black Python code formatter](https://dev.to/adamlombard/vscode-setting-line-lengths-in-the-black-python-code-formatter-1g62)
* [Python Black Code Formatter - Is there any way to apply automatic black formatting before commiting project to Github](https://stackoverflow.com/questions/71180810/python-black-code-formatter-is-there-any-way-to-apply-automatic-black-formatti)

### python formatting 自動縮排整理程式碼

## Visual Studio Code進階操作

## Visual Studio Code結合WSL

## 結語
讓部落格文章能公開在網路上被人搜尋到~