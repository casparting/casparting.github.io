---
title: Jekyll GitHub Pages 優化 感興趣文章 相關文章 優先顯示最相關文章
excerpt: >
  修改Jekyll的演算法讓文章最下方優先顯示與當頁文章最相關的文章，且如果最相關文章不足還會依序顯示次相關文章。
categories:
  - Blog
tags:
  - GitHub Pages
  - Jekyll
  - Minimal Mistakes
  - Releated Posts
  - Optimize
---
## 讓我的部落格顯示最相關文章
我參考了這位作者的[文章](https://blog.webjeda.com/jekyll-related-posts/)與程式碼，他主要程式碼分成reference by tags和reference by categories，我使用的是reference by tags，經過追蹤程式碼後，考慮我的文章多以tags來標示，且使用他的演算法可以顯示較相關的文章，依據就是tags多達2個的文章會顯示在下方~而這個2是可以自行修改的，所以你要下方顯示越相關的文章，相對顯示的文章數量就會越少~甚至只有兩篇文章~

我把他的code merge過來後變成下面這樣~
[修改前後的比較](https://github.com/mmistakes/minimal-mistakes/commit/c5bc25ffffaa7050be866488a9092fcaccce9263)

結果由這

![releated_posts_notags](/assets/images/releated_posts_notags.png)

變成這樣

![releated_posts_by_2_tags](/assets/images/releated_posts_by_2_tags.png)

但這個有個缺點是~
1. 當較相關文章多時，這些較相關的文章不會依序優先顯示最相關的文章
2. 較相關的文章數量少就顯示的少

## 讓我的部落格優先顯示最相關文章到最不相關的文章都顯示

經過一番折騰後，我參考了[Jekyll Liquid語法](https://shopify.github.io/liquid/)，終於搞定優化了新的演算法~

程式碼如右所示~
[優化成是演算法](https://github.com/mmistakes/minimal-mistakes/commit/81e166acf4c61a55373e42bb672d136e878fb2cb)

現在我的文章會由最相關的文章優先顯示，並且不足12篇的話，繼續依序顯示次相關的文章，到最不相關的文章，直到文章顯示達到12篇就不顯示。

## 結語
邏輯就是這麼好玩~