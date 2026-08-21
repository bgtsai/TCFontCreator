**简体中文** [繁體中文](README-TC.md#中文字型簡繁處理工具) 

# 中文字体简繁处理工具（bgtsai fork · 繁體修復版）

> 本 fork 自 [GuiWonder/TCFontCreator](https://github.com/GuiWonder/TCFontCreator)，修復內容見下方「本 fork 的修改」章節。

简繁转换字体制作 同义字(简体字 繁体字 异体字)补全字库 合并简繁字体 合并字体。

## 本 fork 的修改

在使用原版工具处理含日文、韩文的整合字型时，发现一简多繁模式（动态词汇匹配）会误删日韩文字图，追踪后定位到 `removeglyhps()` 的字图清理白名单完全没有涵盖日文假名与韩文字母区段。本 fork 修复了这个问题：

- 白名单新增日文假名（平假名/片假名，`U+3040–U+30FF`）与韩文字母（谚文字母 `U+1100–U+11FF`、谚文兼容字母 `U+3130–U+318F`）区段
- 新增 `datas/UsedChar_JP.txt`：日本常用汉字表 2136 字（2010 年改定版）
- 新增 `datas/UsedChar_KR.txt`：KS X 1001 常用韩文音节表 2350 字
- `converto.py`（otfcc 引擎）与 `convertf.py`（FontForge 引擎）两套实作皆已同步修复

详细的问题根因、修复细节与已知限制，见对应的 commit 记录。

### 自订保留清单

除了内建的常用汉字表、常用韩文音节表，新增了 `datas/UsedChar_Custom.txt`，让使用者可以自行补充「常用清单没收录、但希望转换后保留」的字——例如日本人名用汉字、罕见国字、特定符号等。使用方式与其他 `UsedChar_*.txt` 相同：逐字一行，`#` 开头的整行视为注释。这份文件本身可选，不存在也不影响其他保留清单正常运作。

### 白名单扩充：拉丁文扩充/希腊文/西里尔文/一般符号类

用官方 [Unicode Blocks.txt](https://www.unicode.org/Public/UNIDATA/Blocks.txt) 对原始字型实际收录的字图做系统性交叉比对，找出「字型里确实有、但白名单没涵盖」的字图缺口，而不是等使用者一个个回报缺字才补。这次扩充范围：

- 拉丁文扩充系列（Latin-1 Supplement、Latin Extended-A/B/Additional、IPA Extensions、Combining Diacritical Marks）
- 希腊文与科普特文、西里尔文（含补充区）
- 一般符号类（一般标点、上下标、货币符号、类单位符号、数字形式、箭头、数学运算子、制表符、方块元素、几何图形、杂项符号、装饰符号、点字等）

刻意不收录 Private Use Area（PUA，字型厂商用来放 Nerd Font 图示等自订符号的保留区）——数量庞大且非标准文字符号，收录会严重排挤字图空间。

### 字图空间回报

一简多繁模式的字图空间有 65535 个上限，这次转换处理会给出明确回报：

- **转换失败**（空间不足）：明确指出还需要减少几个字元，并指向 `datas/UsedChar_Custom.txt` 或其他保留清单——通常代表自订保留清单加得太多，需要精简
- **转换成功**：印出这个字型剩余可用于自订保留清单的字图空间数量，方便你知道还能再补充多少字

这两种讯息都是透过标准输出（`print`/例外讯息）呈现，GUI 介面与命令列/自动化流程的 log 都会自动显示，不需要另外设定。

---

## 功能
### 生成简繁转换字体
#### 1. 生成简转繁字体
可选择繁体预设、繁体台湾、繁体香港、繁体旧字形 4 种繁体异体字。
对于简繁一对多情况可选择不处理一简多繁、使用单一常用字、使用词汇动态匹配一简多繁、使用台湾词汇动态匹配（包含台湾常用语以及化学元素名称的转换）。
#### 2. 生成繁转简字体
繁入简出的字体。
### 补充字库
#### 1. 从其他字体补入
可一次补入多个字体。
#### 2. 使用字体本身简繁异体补充
使用字库中存在的异体字、繁体字、简体字补全缺失的字符，在不增加字形的情况下可显示更多的字符。
#### 3. 合并简体与简入繁出字体
针对于简体编码的简繁字体的合并。
### 操作界面
#### 1. Windows 系统
Windows 系统下可直接使用图形界面。
#### 2. Linux 或 Mac 系统
需在终端中运行，在终端中运行 `python run_in_command_line_sc.py` 或 `python3 run_in_command_line_sc.py`。运行前需要确保 otfcc 相关文件已添加允许执行权限，`chmod +x ./otfcc/*`。

## 常见问题
#### 1. 某些字体处理失败怎么办？
答：工具提供 oftcc 和 FontForge 两种字体处理方法，如果处理失败，可尝试换另一种。如果是在 Windows 系统下处理失败，可尝试使用**不带有中文或特殊符号的路径**。
#### 2. 对于转换规则不满意，可否自行修改？
答：可以。本工具所使用的转换字典为纯文本格式，位于 **datas** 目录中。
#### 3. 本账户下其他支持"一繁多简"的简转繁字体（如尙古字体），其处理方法与本工具相同吗？
答：不相同。本工具支持词汇级别的简繁转换，在"一简多繁"或"一繁多简"的转换过程中，参考了"[繁媛明朝](https://github.com/ayaka14732/FanWunMing)"的处理方法：先在字体中追加空白字图 A，通过 OpenType 特性将原字词（如"干燥"）替换为空白字图 A，再将空白字图 A 替换为目标字词（"乾燥"）。
相比之下，这种由词汇到词汇的转换不仅让使用者可以更方便、直接地修改转换字典以满足个性化需求，而且能完美实现区域词汇的转换（例如通过上述流程将"内存"转换为"記憶體"）。
其他注意事项请参阅[《正确实现简转繁字体》](https://ayaka.shn.hk/s2tfont/)。
#### 4. 工具内的"繁体（预设）"是什么标准？
答：本工具的核心转换字典主要源自 [OpenCC](https://github.com/BYVoid/OpenCC) 项目；工具内提及的"繁体（预设）"参照 [OpenCC](https://github.com/BYVoid/OpenCC) 标准繁体。

## 下载地址

**本 fork 打包版**：可从 [Releases](https://github.com/bgtsai/TCFontCreator/releases) 页面下载，直接执行不需要另外安装 Python 或 FontForge。

上游原版：[GuiWonder/TCFontCreator Releases](https://github.com/GuiWonder/TCFontCreator/releases)。

## 特别感谢

本项目由上游 [GuiWonder/TCFontCreator](https://github.com/GuiWonder/TCFontCreator) fork 而来，特别感谢：

* [otfcc](https://github.com/caryll/otfcc)
* [FontForge](https://github.com/fontforge/fontforge)
* [Open Chinese Convert](https://github.com/BYVoid/OpenCC)
* [《正确实现简转繁字体》](https://ayaka.shn.hk/s2tfont/)、[繁媛明朝](https://github.com/ayaka14732/FanWunMing)

本 fork 额外引用：

* [tsmsogn/joyo_kanji](https://github.com/tsmsogn/joyo_kanji) —— 日本常用汉字表（2010 年改定版）2136 字清单资料来源
* KS X 1001 —— 韩国国家标准，本 fork 新增的常用韩文音节清单（2350 字）依此标准透过 `euc_kr` 编码表推算取得
