---
title: Visual Studio Code 基本操作教學 常用快捷鍵
excerpt: >
  快速了解Visual Studio Code的基本操作方式。
categories:
  - Code Editor
tags:
  - Visual Studio Code
  - Tutorial
  - Python
  - Extension
---
## Visual Studio Code基本操作
繼上一篇[Visual_Studio_Code_基本操作教學_python_快速上手]({% link _posts/2023-07-15-Visual_Studio_Code_基本操作教學_python_快速上手.md %})讓新手能快速開始寫程式，接下來底下在說明其他vs code的基本操作。

### 程式碼大綱
讓你透過右邊這個小地圖，可以知道大概位於程式的哪個區段，並且可以案主滑鼠左鍵上下拖曳如圖所示~

![minimap](/assets/images/minimap.png)

另外圖中可以看到在if-else的區域之間有垂直的線條，表示這個區域的程式碼是屬於if(err)的內容~

### 檔案與程式的路徑
讓你透過上面這個bar，知道檔案與程式的路徑如圖所示~

![breadcrumbs](/assets/images/breadcrumbs.png)

如果要關掉不顯示View->Apperance->Breadcrumbs點選~

### 檔案瀏覽
按下最左邊的檔案圖案，會顯示你所開啟的程式檔案路徑(資料夾)內的所有檔案或資料夾，依階層的方式呈現~如圖所示~

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

### 搜尋整個資料夾

我們看到下圖中第二個放大鏡的圖案

![vscode_tutorial_bar](/assets/images/vscode_tutorial_bar.png)

這功能很常用到也很重要，在搜尋整個專案時，可以找一些函示的名稱或變數名出現在那些檔案之中，有助於追蹤程式碼。

### 常用好用的快捷鍵

|  基本快捷鍵  |    功能說明    |
|----------|:-------------:|
| ctrl + s |存檔|
| ctrl + a |全選|
| ctrl + f |當前檔案或路徑搜尋|
| ctrl + shift + f |根目錄資料底下全部搜尋|

|  進階快捷鍵  |    功能說明    |
|----------|:-------------:|
| ctrl + shift + p |vs code 命令列|
| ctrl + F5 |執行程式|
| F5 |執行程式並且除錯|

有興趣想深入的還可以看我這篇[vs code python進階擴充套件 安裝 使用](./2023-07-16-Visual%20Studio%20Code%20python%20進階擴充套件%20安裝%20使用%20介紹.md)以及[Visual Studio Code結合WSL](./2023-06-17-VScode%20+%20Remote%20Wsl%20+%20Docker%20的使用.md)

## 結語
讓使用vs code更具有效率~