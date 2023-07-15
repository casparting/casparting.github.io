---
title: Visual Studio Code 基本操作教學 Python 常用插件 擴充包 推薦
excerpt: >
  快速了解Visual Studio Code的基本操作方式。
categories:
  - IDE
tags:
  - Visual Studio Code
  - Tutorial
  - Python
  - Extension
---
## Visual Studio Code簡介
程式語言已為職場上必備的基礎，在學習每種程式語言到最後會需要使用編輯器來編輯程式語言，而具有多功能的編輯器通常我們稱作整合開發環境Integrated Development Environment(IDE)，其最基本功能包刮專案開啟、撰寫、模擬、編譯、除錯與執行程式，使用IDE就是為了增加撰寫與除錯效率，那常見的IDE有哪些呢?簡單舉個例，支援C語言的IDE有CodeBlocks、Eclipse等，支援Java的IDE有Netbeans、Eclipse等，而下面談到的Visual Studio Code(vs code)為什麼這麼廣泛受工程師歡迎，個人覺得有四大原因，第一程式碼是開源的而且lightweight，多人維護相對穩健，事實上他只是一個程式編輯器(code editor)，並不是龐大的IDE，常見的程式編輯器還有Notepad++等。第二它可以用在很多不同的程式語言，不像傳統上面列的IDE大多是針對某個程式語言的開發環境。第三它有很多擴充包可以安裝，方便使用者自行客製化增加其他額外功能，像是程式補全、Debugging等，最後一個是他跨平台可以安裝在Windows, Linux, Mac OS。

## Visual Studio Code下載與安裝
這邊以Windows為例子，請依照你的作業系統(OS)的不同下載不同的版本[連結在這邊](https://code.visualstudio.com/download)，這邊我就提供Windows的[載點](https://go.microsoft.com/fwlink/?LinkID=534107)。

安裝的過程中有分成兩種User Setup和System Setup，前者會把vs code安裝到Local AppData目錄中，僅只有當前使用者用戶可以使用vs code，而後者是安裝到system's Program Files，安裝過程需要使用系統權限安裝，此安裝可以讓Windows上所有的使用者用戶都可以使用vs code。

看個人需求選擇，如果您不知道我會建議後者System Setup。
## Visual Studio Code插件安裝
這邊以使用Python程式語言為例，我依序介紹Python好用或常用的擴充套件，並說明其功能及如何使用。

介紹之前我們先來看看vs code提供那些擴充包，熱門排行榜有哪些~

請參考[此連結](https://marketplace.visualstudio.com/VSCode)如果你對上述有一定的瞭解，也可以自行安裝喜歡的擴充套件~



## Visual Studio Code基本操作

程式碼的大鋼，讓你透過右邊這個小地圖，可以知道大概位於程式的哪個區段，並且可以案主滑鼠左鍵上下拖曳如圖所示~

![minimap](/assets/images/minimap.png)

另外圖中可以看到在if-else的區域之間有垂直的線條，表示這個區域的程式碼是屬於if(err)的內容~

檔案與程式的路徑，讓你透過上面這個bar，知道檔案與程式的路徑如圖所示~

![breadcrumbs](/assets/images/breadcrumbs.png)

如果要關掉不顯示View->Apperance->Breadcrumbs點選~

檔案瀏覽，按下最左邊的檔案圖案，會顯示你所開啟的程式檔案路徑(資料夾)內的所有檔案或資料夾，依階層的方式呈現~如圖所示~

![explorer](/assets/images/explorer.png)

此圖表示我開啟了CASPARTING.GITHUB.IO的目錄(資料夾)，而該目錄內有_data、_includes等資料夾，還有_config.yml、ads.txt等檔案~

在這邊對資料夾或檔案按下滑鼠右鍵或左鍵~可以對其做下列處裡：
* 建立、刪除與重新命名檔案或目錄
* 拖曳移動檔案或資料夾
* 瀏覽整個資料內容
* 開啟終端機並直接到該路徑
* 可以同時點選多個檔案或路徑做操作
* 可以同時點選兩個檔案做compare selected

補充說明我對_includes目錄按下右鍵，點選Open in integrated Terminal在下方會出現，終端機並進入到這個資料夾內~如圖所示~

![open_intergrated_terminal](/assets/images/open_intergrated_terminal.png)



### 常用好用的快捷鍵

## Visual Studio Code額外插件安裝

## Visual Studio Code進階操作

## Visual Studio Code結合WSL

## 結語
讓部落格文章能公開在網路上被人搜尋到~