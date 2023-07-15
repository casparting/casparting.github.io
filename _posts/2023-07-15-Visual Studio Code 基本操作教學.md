---
title: Visual Studio Code 零基礎 基本操作教學 Python 常用插件 擴充包 推薦
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
這邊以Windows為例子，請依照你的作業系統(OS)的不同下載不同的版本[連結在這邊](https://code.visualstudio.com/download)，這邊要注意他的版本有分User Installer與System Installer如圖所示，請下載對應的版本。

![vscode_installer](/assets/images/vscode_installer.png)

安裝的過程中有分成兩種User Setup和System Setup，前者會把vs code安裝到Local AppData目錄中，僅只有當前使用者用戶可以使用vs code，而後者是安裝到system's Program Files，安裝過程需要使用系統權限安裝，此安裝可以讓Windows上所有的使用者用戶都可以使用vs code。

看個人需求選擇，如果您不知道我會建議後者System Setup。注意安裝過程都使用預設設定，除非瞭解其用意要自行調整之外。

請注意建議新手安裝vs code的過程中不要更改設定，直接使用他建議的設定按下一步安裝完成。

補充如果你已經安裝過vs code並且已經使用過了，可是現在想要重新學vs code基本操作，建議你把vs code記錄刪掉重新按照我下面的步驟學習~

刪除記錄還原到預設值的方法~

Windows：
1. 請開啟資料夾輸入這個路徑 %APPDATA%\Code ，把Code整個資料夾刪除。
2. 請開啟資料夾輸入這個路徑 %USERPROFILE%\\.vscode，把vscode刪除。

補充：如何把介面改成英文
開啟vs code，點選ctrl+shift+P，輸入display選擇en。

## 開啟 Visual Studio Code
打開vs code一開始會詢問你是否要做初始設定~

像是選擇vs code的主題顏色~如圖所示。

![vscode_getstart_theme](/assets/images/vscode_getstart_theme.png)

第二個是詢問你是否要跟其他裝置做設定上的同步，這邊我們就不設定直接選擇下一步。

第三個是告訴你可以嘗試使用vs code的命令，快捷鍵是Ctrl+Shift+P，這邊我會在後面進階的部分在說明一樣先選下一個。

Browse Language Extensions就是要推薦你好用的程式語言擴充套件，一樣跳過後面我會帶你一步一步安裝。

最後一個開啟你的程式碼資料夾，一樣跳過我要告訴你往後怎麼自行開啟程式碼。

剩下的Next section都是較進階的用法，我們就看到這邊就好，接下來將繼續vs code的基本操作。

請準備好你的程式碼檔案，這邊我將以python為例子，下載[這個](https://github.com/geekcomputers/Python)python範例程式。

下載方式請點選code選擇Download ZIP如圖所示。
![python_git_download_zip](/assets/images/python_git_download_zip.png)

下載到你想放置的資料夾底下之後解開來~

接下來回到vs code點選左上角的File->Open Folder
選擇你剛剛解開來的最外層資料夾~

此時他會跳出一個訊息問你要不要信任作者~這邊很重要一定要點選左邊的信任，否則會少了一些功能。如圖所示。
![trust_author](/assets/images/trust_author.png)

這時候會看到左邊有一個列表就是我們的資料夾內容，包括那些程式檔案或資料夾，我們往下拉至Add two numbers.py，點兩下把它開啟，便會看到中間出現他的程式碼。

這時候右下角也會出現提示視窗，詢問你是否要裝python擴充套件，接下來我就要講安裝擴充包來加強程式撰寫的一些輔助功能。

## Visual Studio Code插件安裝 擴充套件安裝
這邊以使用Python程式語言為例，我依序介紹Python好用或常用的擴充套件，並說明其功能及如何使用。

介紹之前我們先來看看vs code提供那些擴充包，熱門排行榜有哪些~

請參考[此連結](https://marketplace.visualstudio.com/VSCode)如果你對上述有一定的瞭解，也可以自行安裝喜歡的擴充套件~

那麼我們回到vs code點選最左邊那行的方塊圖如圖所示。

![vscode_extension](/assets/images/vscode_extension.png)


點選之後在推薦的地方找到python擴充包或上面的搜尋欄位輸入python。如圖所示。

![python_extension](/assets/images/python_extension.png)

點選install然後也可以順便點進去看他的說明。
這個套件包可以幫助我們debugging、程式補全等。

<!-- 安裝好之後，他出現提示視窗要我們按照步驟來做初始設定，第一步建立python檔案，我們已經準備好了，第二步設定python環境變數，這是讓我們可以直接在vs code中使用python直譯器，點選Create Environment，再點選上面第一個Venv是比較新的設定方式。如圖所示。

![python_envirmenot_setting](/assets/images/python_envirmenot_setting.png)

如果點完後他搜尋不到python就會出現Python is not installed, please download and install it.

直接點選他，他會跳出Microsoft store讓你可以直接安裝python直譯器。點選取得。

安裝好了再重複上述步驟直到出現python 3.11.x版本，而不再是Python is not installed。一樣點選。 -->


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