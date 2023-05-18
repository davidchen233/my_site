---
title: 淺談 JavaScript 引擎
authors: [haku]
tags: [javascript]
---

# 淺談 JavaScript 引擎

> 在學會怎麼寫 JavaScript 後，來了解一下他的引擎和執行環境吧

## 關於 JavaScript 引擎

### 先來談談什麼是 JavaScript 引擎吧 ？

- 他是一個用來執行 JavaScript 的程序，而現行所有瀏覽器都有自己的 JavaScript 引擎
- 目前現存的 JavaScript 引擎
  - **V8** - 由 Google 為了 Chrome 所建立的開源 JavaScript 引擎
    - 目前 Microsoft Edge 和 NodeJS 也是使用 V8
  - **JavaScriptCore** - 由 Apple 為了 Safari 所建立的開源 JavaScript 引擎
  - **Chakra** - Microsoft 過去給 Microsoft Edge 使用的 JavaScript 引擎
  - **Rhino** - 由 Mozilla 為了 FireFox 所建立的開源 JavaScript 引擎
    - 將 JavaScript 轉成 Java Classes
  - **SpiderMonkey** - Mozilla FireFox 過去所使用的 JavaScript 引擎
- JavaScript 引擎主要被拆為兩塊 - **Memory Heap** & **Call Stack**
  - **Memory Heap** - 這裡負責處理記憶體的分配
    → 為一塊非結構化的記憶池 ( **Memory Pool** )，存著 Application 所需用到的 **Objects**
  - **Call Stack** - 實際使用 Execution Context 執行 Code 的地方

> 在實際執行之前，仍有些前置動作需要完成，讓我們一起來看看發生了什麼事吧～

## **Compilation (編譯) & Interpretation (直譯)**

> 已知，電腦只能解讀 0 & 1，也因此電腦程序最終都需要被轉換成機器語言
> → 而轉換的方式分為了 Compilation 和 Interpretation

- **Compilation (編譯)**
  - 「編譯」程序主要分為兩個步驟
    1. 整個原始碼會被**一次**轉換成機器語言，而這些機器語言會被寫入一個可攜式且能夠在任何地方都能執行的檔案 (不分平台或作業系統)
    2. 在機器上執行
  - 因為會**提前做好轉換**，將來需要執行該程式時，只要直接執行機器碼，不需要再重新編譯
    ![@Created By 王廷齊](./img/Untitled.png)
- **Interpretation (直譯)**
  - 在「直譯」過程中，「直譯器」會跑遍整個原始碼，並**一行一行的執行**
  - 不同於「編譯」所需要的兩個步驟程序，「直譯」會**同時進行**讀取轉換 & 執行機器碼的動作
  - 由此可知，他不會提前做好轉換的動作，而是**在當下要執行前作轉換**
    ![@Created By 王廷齊](./img/Untitled%201.png)

> _現在已經了解兩者的差別了，所以 JavaScript 到底是屬於哪一種呢 !?_
>
> - 在**過去**，JavaScript 為完全直譯式的語言，缺點在於執行速度上，會大幅慢於編譯式語言
>   - 而在現今需要高效能和快速解析的 Application 中，就產生了問題
>     → 想像在遊戲中移動一個物品，卻需要花一段時間才能抵達我們要其移動到的位置 !?
> - 為了改善上述狀況，現行 JavaScript 衍伸出了一種稱為「**即時編譯 (JIT)**」的處理方式
>     <aside>
>     💡 至今還有許多人認為 JavaScript 是種直譯式語言呢 QQ 趕快告訴他實情吧！
>     
>     </aside>

## 何謂「即時編譯 **(JIT)」**？

> **JIT -** Just-in-Time Compilation

- 「即時編譯」結合了「編譯」&「直譯」兩種概念的特性
  - 整個原始碼會被**一次**轉換成機器語言，接著**馬上執行**
  - 差別在於轉換後的機器語言，**不會被寫入檔案中**等待被執行
- 這樣的方式非常適合現今需要高效能的 Application，會大幅快於一行一行的去執行

> 以下 V8 引擎情況作介紹~

## 那 JavaScript 中使用的 JIT 實際上是怎麼執行的呢？

- 示意圖
  ![@Created By 王廷齊](./img/Untitled%202.png)
  @Created By 王廷齊

1.  在 Code 中，HTML 處理器會去尋找 `<script>` 和他所連結的資源，並對其做載入
    - 這些資源來源可能為「網路」、「暫存區」或其他「Service Worker」
    - 這些資源會以位元組流 ( **Byte Streams** ) 的方式進來
      ![[https://miro.medium.com/max/720/1*cOoWhcaQqt7YIefpVMjTYw.gif](https://miro.medium.com/max/720/1*cOoWhcaQqt7YIefpVMjTYw.gif)](./img/Untitled%203.png)
      [https://miro.medium.com/max/720/1\*cOoWhcaQqt7YIefpVMjTYw.gif](https://miro.medium.com/max/720/1*cOoWhcaQqt7YIefpVMjTYw.gif)
2.  接著會透過 **Byte Stream Decoder** 對這些載入的串流資料進行解碼

    - 將每行 Code 拆分成對於 JavaScript 代表著某種意義的不同單元碎片 ( Tokenizing )
      → ex. `let`、`function`、`static` 等關鍵字
      ![[https://miro.medium.com/max/720/1*Eb2a3HrsWCQogSpEW9gHjA.gif](https://miro.medium.com/max/720/1*Eb2a3HrsWCQogSpEW9gHjA.gif)](./img/Untitled%204.png)

          [https://miro.medium.com/max/720/1*Eb2a3HrsWCQogSpEW9gHjA.gif](https://miro.medium.com/max/720/1*Eb2a3HrsWCQogSpEW9gHjA.gif)

    - 引擎使了兩種處理器 - **預處理器** & **處理器**
      - 為了降低載入頁面所需的時間，引擎會盡量避免去馬上解析並非馬上要用到的 Code
        - ex. 有些 Function 可能要等到使用這點擊某個按鈕後才會觸發
      - **預處理器** - 負責處理那些那些之後才會用到的 Code
      - **處理器** - 負責處理那些那些馬上會用到的 Code
    - 以 `const x = 23;` 為例，將語法單元化，將 JavaScript 內的標點符號、詞彙一一解析出來
      ![Untitled](./img/Untitled%205.png)

3.  處理器會使用 **Byte Stream Decoder** 所產生的 Tokens 建立節點

    - 並用他們建立一種稱為 **Abstract Syntax Tree (AST)** 的資料結構
        <aside>
        💡 此處和 DOM Tree 的生成沒有任何關係喔！
        
        </aside>
        
        ![[https://miro.medium.com/max/720/1*r4CyGfK7TWvm1sFl1jaOWQ.gif](https://miro.medium.com/max/720/1*r4CyGfK7TWvm1sFl1jaOWQ.gif)](./img/Untitled%206.png)
        
        [https://miro.medium.com/max/720/1*r4CyGfK7TWvm1sFl1jaOWQ.gif](https://miro.medium.com/max/720/1*r4CyGfK7TWvm1sFl1jaOWQ.gif)

    - 以 `const x = 23;` 為例，建立 **AST**
      ![Untitled](./img/Untitled%207.png)

4.  「直譯器 ( **Interpreter** )」會去查看建立的 **AST**，看看有沒有出現語法錯誤，
    接著用他去產生機器碼 ( **Bytecode** )
    ![[https://miro.medium.com/max/720/1*5WJid_AePzCASZ0NTLZv-w.gif](https://miro.medium.com/max/720/1*5WJid_AePzCASZ0NTLZv-w.gif)](./img/Untitled%208.png)

        [https://miro.medium.com/max/720/1*5WJid_AePzCASZ0NTLZv-w.gif](https://miro.medium.com/max/720/1*5WJid_AePzCASZ0NTLZv-w.gif)

        - 結束後會將 **AST** 移除來減少記憶體使用
        - 這個部分會因執行環境有所差異
        - 為了讓程式可以盡快被執行，一開始產生的為一組未優化的機器碼版本

5.  隨著 **Bytecode** 被執行，內容也隨之被產生，並接著去偵測是否有優化的可能性

    - 這些資訊 ( **Bytecode** & 執行反饋 ) 會被送進優化用的編譯器中，並用他們重新產生優化後的機器碼
        <aside>
        💡 此優化過程所使用的執行緒，和 Main Thread 是分開的
        → JavaScript 開發者無法從原始碼中，存取到此演算法
        
        </aside>
        
        ![[https://miro.medium.com/max/4800/1*xJ3kFQ776JaMquxron2-gQ.gif](https://miro.medium.com/max/4800/1*xJ3kFQ776JaMquxron2-gQ.gif)](./img/Untitled%209.png)
        
        [https://miro.medium.com/max/4800/1*xJ3kFQ776JaMquxron2-gQ.gif](https://miro.medium.com/max/4800/1*xJ3kFQ776JaMquxron2-gQ.gif)

    - 優化用的編譯器只會去優化引擎執行 **Bytecode** 時所看過的部分
      - 由於 JavaScript 是 **Dynamic Typed** 的語言，也就意味資料型別可能隨時改變，
        如果每次執行都得去判斷型別就會很慢 - 如果某塊 Code 被重複執行了很多次，且每次都返回相同的型別
        → 編譯器就能直接用此型別而不需判斷 - 如果過程中型別出現改變，將會回去解析原本的 **Bytecode**

---

### 參考文章

- [What is the JavaScript Engine?](https://www.htmlgoodies.com/javascript/what-is-the-javascript-engine-and-how-it-works/)
- [JavaScript 的運行](https://ithelp.ithome.com.tw/articles/10264531?sc=rss.iron)
- [Inside the JavaScript Engine](https://blog.devgenius.io/inside-the-javascript-engine-bb7b9f26e84b)
- [The Complete JavaScript Course 2023: From Zero to Expert!](https://www.udemy.com/course/the-complete-javascript-course/)
- [JavaScript Visualized: The JavaScript Engine](https://javascript.plainenglish.io/javascript-visualized-the-javascript-engine-1e3fc5d5310d)
