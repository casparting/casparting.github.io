---
title: Visual Studio Code 零基礎 基本操作教學 撰寫 Python 快速上手 
excerpt: >
  快速使用Visual Studio Code來撰寫程式。
categories:
  - Code Editor
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

## 開啟 Visual Studio Code 快速開始撰寫程式
打開vs code一開始會詢問你是否要做初始設定~

像是選擇vs code的主題顏色~如圖所示。

![vscode_getstart_theme](/assets/images/vscode_getstart_theme.png)

第二個是詢問你是否要跟其他裝置做設定上的同步，這邊我們就不設定直接選擇下一步。

第三個是告訴你可以嘗試使用vs code的命令，快捷鍵是Ctrl+Shift+P，這邊我會在後面進階的部分在說明一樣先選下一個。

Browse Language Extensions就是要推薦你好用的程式語言擴充套件，一樣跳過後面我會帶你一步一步安裝。

最後一個開啟你的程式碼資料夾，一樣跳過我要告訴你往後怎麼自行開啟程式碼。

剩下的Next section都是較進階的用法，我們就看到這邊就好，接下來將繼續vs code的基本操作。

下面分成兩個部分，請依照自己的需求觀看不同的介紹。

1. 你已經有別人提供的程式碼或是已經確認耀基於別人某個程式碼繼續開發的，[請參考這開啟別人的專案或程式碼](#開啟別人的專案或程式碼)。

2. 你是新手想自己新增一個新檔案來撰寫python檔案做練習，[請參考這新增一個新檔案並建立虛擬環境](#新增一個新檔案並建立虛擬環境)。

### 開啟別人的專案或程式碼

請準備好你要開啟的程式碼檔案，這邊我將以python為例子，下載[這個](https://github.com/geekcomputers/Python)python範例程式。

下載方式請點選code選擇Download ZIP如圖所示。
![python_git_download_zip](/assets/images/python_git_download_zip.png)

下載到你想放置的資料夾底下之後解開來~

接下來回到vs code點選左上角的File->Open Folder
選擇你剛剛解開來的最外層資料夾~

此時他會跳出一個訊息問你要不要信任作者~這邊很重要一定要點選左邊的信任，否則會少了一些功能。如圖所示。
![trust_author](/assets/images/trust_author.png)

這時候會看到左邊有一個列表就是我們的資料夾內容，包括哪些程式檔案或資料夾，我們往下拉至Add two numbers.py，點兩下把它開啟，便會看到中間出現他的程式碼。

### 新增一個新檔案並建立虛擬環境
上面是直接開啟別人寫好的程式來執行，接下來我會說明如果自己開一個新檔案寫好程式如何執行與debug。

請先關閉現在的程式資料夾File->Close Floder。

開啟新檔案File->New File->輸入檔案名稱。如test.py。然後儲存到你想要儲存的路徑資料夾底下。建議要新增一個資料夾，把python存到新資料夾內。

建立虛擬環境，因為python除了內建模組外，還有額外的模組，且還有分不同的版本，為了讓每一個python專案都要自己的特定版本的模組設定，所以建議可以建立虛擬環境。詳細虛擬環境說明可以參考[此連結](https://ithelp.ithome.com.tw/articles/10202335)。

按下鍵盤的Ctrl+Shift+P輸入命令python:create Environment，點選第一個新的Venv設定。

如果你還沒裝好python直譯器，請點選後跳出Microsoft store請安裝python直譯器。

安裝好python直譯器或你已經裝好了，在一次下指令會出現python3.11.x版本，看你裝哪一個版本顯示不同。
![python_interpreter](/assets/images/python_interpreter.png)

點選之後會開始執行，可以到下方的output看看他做了什麼事情。

跑完後左邊會看到venv如下圖所示。
![venv](/assets/images/venv.png)

最後就可以開始撰寫python程式囉。

## Python插件安裝 擴充套件安裝

當你建立py檔案或開啟時右下角也會出現提示視窗，詢問你是否要裝python擴充套件，接下來我就要講安裝擴充包來加強程式撰寫的一些輔助功能。

這邊以使用Python程式語言為例，我依序介紹Python好用或常用的擴充套件，並說明其功能及如何使用。

介紹之前我們先來看看vs code提供那些擴充包，熱門排行榜有哪些~

請參考[此連結](https://marketplace.visualstudio.com/VSCode)如果你對上述有一定的瞭解，也可以自行安裝喜歡的擴充套件~

那麼我們回到vs code點選最左邊那行的方塊圖如圖所示。

![vscode_extension](/assets/images/vscode_extension.png)


點選之後在推薦的地方找到python擴充包或上面的搜尋欄位輸入python。如圖所示。

![python_extension](/assets/images/python_extension.png)

點選install然後也可以順便點進去看他的說明。
這個套件包可以幫助我們

1. 使用python直譯器執行python程式顯示結果
2. 除錯debugging
3. 程式命令補全

安裝好之後，他出現提示視窗要我們按照步驟來做初始設定，第一步建立python檔案，我們已經準備好了，第二步設定python直譯器設定，[剛剛前面第二點已經說明過](#新增一個新檔案並建立虛擬環境)，如果你是使用別人的專案，可以在這邊載入venv。

![python_init_setup](/assets/images/python_init_setup.png)

第三個設定是執行與除錯，開啟一個python檔案後(File->Open File)右上角有一個播放的圖案

![python_run](/assets/images/python_run.png)

點選他開始執行程式。我們就以Add two sum為例子，按下播放圖案執行後Windows會跳出視窗問你是否允許python直譯器，請點選允許~

執行後就會看到下方Terminal出現的執行結果~
![python_extrun_terminal_outputension](/assets/images/run_terminal_output.png)

如果我們在檔案內輸入一些程式碼，python的擴充包功能，有命令補全(auto-completionx)功能，會推薦你可用的函示或變數名稱等，如圖所示。P.S.如果沒出現請重啟vs code。

![python_command](/assets/images/python_command.png)

再來要講怎麼除錯debugging，這邊要先說明一下debugging大致要分成幾個方法：
* 下breakpoint中斷點
* 單步執行step into
* 全速執行run(到中斷點會停止)
* 執行下一行程式不進入函式step over

之後有多餘時間我在[特別寫一篇除錯程式碼的常用方法]({% link _posts/2023-07-19-Visual_Studio_Code_python_debugging_tutorial.md %})~

怎麼先假設你對上面列的有基本認知~

那怎麼下breakpoint，有兩種方法，
1. 滑鼠游標移動到第幾列程式碼數字的前方出現紅點，點左鍵。
2. 打字的閃爍游標，在躍下中斷點那行，按下F9。

如圖所示。

![setbreakpoint](/assets/images/setbreakpoint.png)

設定好中斷點之後，按下F5點選python file開始執行程式與除錯。

![run_debug](/assets/images/run_debug.png)

如果不要除錯可以按Ctrl+F5。

所以程式會執行到中斷點後停止，此時可以看當前程式的變數數值狀態等。

![debug_info](/assets/images/debug_info.png)

當然你也可以繼續step into或step over就點選下面的button。

![debug_button](/assets/images/debug_button.png)

你還可以到debug console輸入變數名稱，他就會印出來。

![debug_input_variable](/assets/images/debug_input_variable.png)

最後一個部分~當我們寫python程式需要用到其他模組的時候，會import模組名稱，這時候如果你模組沒有安裝的話就無法執行程式。

```
import numpy as np

msg = "Roll a dice"
print(msg)

print(np.random.randint(1,9))
```

如下圖所示。

![import_numpy](/assets/images/import_numpy.png)

請在這邊的terminal中輸入以下指令。
```console
python.exe -m pip install numpy 
```
結果如下圖所示。

![python_install_numpy](/assets/images/python_install_numpy.png)

如果用到其他套件一樣用此指令方式安裝。

這邊我們可以再回去看venv如圖所示~

![venv_numpy](/assets/images/venv_numpy.png)

多了numpy的module，所以理論上你把這包資料夾專案含venv檔案內容，對方只要執行就會自動安裝numpy的module，不用在下pip install安裝。(待驗證)

到這邊已經可以快速開始撰寫python並且執行與除錯了~

想要繼續深入學習請繼續看[Visual_Studio_Code_python_基本操作_常用快捷鍵]({% link _posts/2023-07-16-Visual_Studio_Code_python_基本操作_常用快捷鍵.md %})
以及[Visual_Studio_Code_python_進階擴充套件_安裝_使用_介紹]({% link _posts/2023-07-16-Visual_Studio_Code_python_進階擴充套件_安裝_使用_介紹.md %})~~

以上如果有任何問題歡迎下方留言~

<!-- 安裝好之後，他出現提示視窗要我們按照步驟來做初始設定，第一步建立python檔案，我們已經準備好了，第二步設定python環境變數，這是讓我們可以直接在vs code中使用python直譯器，點選Create Environment，再點選上面第一個Venv是比較新的設定方式。如圖所示。

![python_envirmenot_setting](/assets/images/python_envirmenot_setting.png)

如果點完後他搜尋不到python就會出現Python is not installed, please download and install it.

直接點選他，他會跳出Microsoft store讓你可以直接安裝python直譯器。點選取得。

安裝好了再重複上述步驟直到出現python 3.11.x版本，而不再是Python is not installed。一樣點選。 -->

## 結語
幫助想要快速上手程式語言的新手~