[简体中文](../../#中文字体简繁处理工具) **繁體中文** 

# 中文字型簡繁處理工具（bgtsai fork · 繁體修復版）

> 本 fork 自 [GuiWonder/TCFontCreator](https://github.com/GuiWonder/TCFontCreator)，修復內容見下方「本 fork 的修改」章節。

簡繁轉換字型製作 同義字(簡體字 繁體字 異體字)補全字型檔 合併簡繁字型 合併字型。

## 本 fork 的修改

在使用原版工具處理含日文、韓文的整合字型時，發現一簡多繁模式（動態詞彙匹配）會誤刪日韓文字圖，追蹤後定位到 `removeglyhps()` 的字圖清理白名單完全沒有涵蓋日文假名與韓文字母區段。本 fork 修復了這個問題：

- 白名單新增日文假名（平假名/片假名，`U+3040–U+30FF`）與韓文字母（諺文字母 `U+1100–U+11FF`、諺文相容字母 `U+3130–U+318F`）區段
- 新增 `datas/UsedChar_JP.txt`：日本常用漢字表 2136 字（2010 年改定版）
- 新增 `datas/UsedChar_KR.txt`：KS X 1001 常用韓文音節表 2350 字
- `converto.py`（otfcc 引擎）與 `convertf.py`（FontForge 引擎）兩套實作皆已同步修復

詳細的問題根因、修復細節與已知限制，見對應的 commit 紀錄。

### 自訂保留清單

除了內建的常用漢字表、常用韓文音節表，新增了 `datas/UsedChar_Custom.txt`，讓使用者可以自行補充「常用清單沒收錄、但希望轉換後保留」的字——例如日本人名用漢字、罕見國字、特定符號等。使用方式與其他 `UsedChar_*.txt` 相同：逐字一行，`#` 開頭的整行視為註解。這份檔案本身可選，不存在也不影響其他保留清單正常運作。

### 白名單擴充：拉丁文擴充/希臘文/西里爾文/一般符號類

用官方 [Unicode Blocks.txt](https://www.unicode.org/Public/UNIDATA/Blocks.txt) 對原始字型實際收錄的字圖做系統性交叉比對，找出「字型裡確實有、但白名單沒涵蓋」的字圖缺口，而不是等使用者一個個回報缺字才補。這次擴充範圍：

- 拉丁文擴充系列（Latin-1 Supplement、Latin Extended-A/B/Additional、IPA Extensions、Combining Diacritical Marks）
- 希臘文與科普特文、西里爾文（含補充區）
- 一般符號類（一般標點、上下標、貨幣符號、類單位符號、數字形式、箭頭、數學運算子、製表符、方塊元素、幾何圖形、雜項符號、裝飾符號、點字等）

刻意不收錄 Private Use Area（PUA，字型廠商用來放 Nerd Font 圖示等自訂符號的保留區）——數量龐大且非標準文字符號，收錄會嚴重排擠字圖空間。

### 字圖空間回報

一簡多繁模式的字圖空間有 65535 個上限，這次轉換處理會給出明確回報：

- **轉換失敗**（空間不足）：明確指出還需要減少幾個字元，並指向 `datas/UsedChar_Custom.txt` 或其他保留清單——通常代表自訂保留清單加得太多，需要精簡
- **轉換成功**：印出這個字型剩餘可用於自訂保留清單的字圖空間數量，方便你知道還能再補充多少字

這兩種訊息都是透過標準輸出（`print`/例外訊息）呈現，GUI 介面與命令列/自動化流程的 log 都會自動顯示，不需要另外設定。

---

## 功能
### 生成簡繁轉換字型
#### 1. 生成簡轉繁字型
可選擇繁體預設、繁體臺灣、繁體香港、繁體舊字形 4 種繁體異體字。
對於簡繁一對多情況可選擇不處理一簡多繁、使用單一常用字、使用詞彙動態匹配一簡多繁、使用臺灣詞彙動態匹配（包含臺灣常用語以及化學元素名稱的轉換）。
#### 2. 生成繁轉簡字型
繁入簡出的字型。
### 補充字型檔
#### 1. 從其他字型檔補入
可一次補入多個字型檔。
#### 2. 使用字型本身簡繁異體補充
使用字型檔中存在的異體字、繁體字、簡體字補全缺失的字元，在不增加字形的情況下可顯示更多的字元。
#### 3. 合併簡體與簡入繁出字型
針對於簡體編碼的簡繁字型的合併。
### 操作介面
#### 1. Windows 系統
Windows 系統下可直接使用圖形介面。
#### 2. Linux 或 Mac 系統
需在終端中執行，在終端中執行 `python run_in_command_line_tc.py` 或 `python3 run_in_command_line_tc.py`。執行前需要確保 otfcc 相關檔案已新增允許執行許可權，`chmod +x ./otfcc/*`。

## 常見問題
#### 1. 某些字型處理失敗怎麼辦？
答：工具提供 oftcc 和 FontForge 兩種字型處理方法，如果處理失敗，可嘗試換另一種。如果是在 Windows 系統下處理失敗，可嘗試使用**不帶有中文或特殊符號的路徑**。
#### 2. 對於轉換規則不滿意，可否自行修改？
答：可以。本工具所使用的轉換字典為純文字格式，位於 **datas** 目錄中。
#### 3. 本帳戶下其他支援「一繁多簡」的簡轉繁字型（如尙古字型），其處理方法與本工具相同嗎？
答：不相同。本工具支援詞彙級別的簡繁轉換，在「一簡多繁」或「一繁多簡」的轉換過程中，參考了「[繁媛明朝](https://github.com/ayaka14732/FanWunMing)」的處理方法：先在字型中追加空白字圖 A，透過 OpenType 特性將原字詞（如「干燥」）替換為空白字圖 A，再將空白字圖 A 替換為目標字詞（「乾燥」）。
相比之下，這種由詞彙到詞彙的轉換不僅讓使用者可以更方便、直接地修改轉換字典以滿足個性化需求，而且能完美實現區域詞彙的轉換（例如透過上述流程將「内存」轉換為「記憶體」）。
其他注意事項請參閱[《正確實現簡轉繁字型》](https://ayaka.shn.hk/s2tfont/hant/)。
#### 4. 工具內的「繁體（預設）」是什麼標準？
答：本工具的核心轉換字表主要源自 [OpenCC](https://github.com/BYVoid/OpenCC) 專案；工具內提及的「繁體（預設）」參照 [OpenCC](https://github.com/BYVoid/OpenCC) 標準繁體。

## 下載地址

**本 fork 打包版**：可從 [Releases](https://github.com/bgtsai/TCFontCreator/releases) 頁面下載，直接執行不需要另外安裝 Python 或 FontForge。

上游原版：[GuiWonder/TCFontCreator Releases](https://github.com/GuiWonder/TCFontCreator/releases)。

## 特別感謝

本專案由上游 [GuiWonder/TCFontCreator](https://github.com/GuiWonder/TCFontCreator) fork 而來，特別感謝：

* [otfcc](https://github.com/caryll/otfcc)
* [FontForge](https://github.com/fontforge/fontforge)
* [Open Chinese Convert](https://github.com/BYVoid/OpenCC)
* [《正確實現簡轉繁字型》](https://ayaka.shn.hk/s2tfont/hant/)、[繁媛明朝](https://github.com/ayaka14732/FanWunMing)

本 fork 額外引用：

* [tsmsogn/joyo_kanji](https://github.com/tsmsogn/joyo_kanji) —— 日本常用漢字表（2010 年改定版）2136 字清單資料來源
* KS X 1001 —— 韓國國家標準，本 fork 新增的常用韓文音節清單（2350 字）依此標準透過 `euc_kr` 編碼表推算取得

