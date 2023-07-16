---
title: GitHub tonken 過期 怎麼辦 password authentication was removed command line
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

但這是一次性的，請繼續下面步驟永久更新

## 非vs code 手動更新 token

請先到你的git檔案庫頁面，點選clone複製連結~

把連結

https://github.com/casparting/casparting.github.io.git

改成

https://username:token@github.com/casparting/casparting.github.io.git

username是你的帳號

token是你的token密碼

然後在終端機輸入以下指令(我的token不顯示要自行更改)
```
git remote set-url origin https://casparting:token@github.com/casparting/casparting.github.io.git
```

Ref: [How to update a GitHub access token via command line](https://stackoverflow.com/questions/70283510/how-to-update-a-github-access-token-via-command-line)

## 結語
給自己的快速紀錄~