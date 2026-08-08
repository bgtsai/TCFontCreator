**简体中文** [繁體中文](README-TC.md#中文字型簡繁處理工具) 

# 中文字体简繁处理工具
简繁转换字体制作 同义字(简体字 繁体字 异体字)补全字库 合并简繁字体 合并字体。

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
#### 3. 本账户下其他支持“一繁多简”的简转繁字体（如尙古字体），其处理方法与本工具相同吗？
答：不相同。本工具支持词汇级别的简繁转换，在“一简多繁”或“一繁多简”的转换过程中，参考了“[繁媛明朝](https://github.com/ayaka14732/FanWunMing)”的处理方法：先在字体中追加空白字图 A，通过 OpenType 特性将原字词（如“干燥”）替换为空白字图 A，再将空白字图 A 替换为目标字词（“乾燥”）。
相比之下，这种由词汇到词汇的转换不仅让使用者可以更方便、直接地修改转换字典以满足个性化需求，而且能完美实现区域词汇的转换（例如通过上述流程将“内存”转换为“記憶體”）。
其他注意事项请参阅[《正确实现简转繁字体》](https://ayaka.shn.hk/s2tfont/)。
#### 4. 工具内的“繁体（预设）”是什么标准？
答：本工具的核心转换字典主要源自 [OpenCC](https://github.com/BYVoid/OpenCC) 项目；工具内提及的“繁体（预设）”参照 [OpenCC](https://github.com/BYVoid/OpenCC) 标准繁体。

## 下载地址
可从 [Releases](https://github.com/GuiWonder/TCFontCreator/releases) 页面下载。

## 特别感谢
* [otfcc](https://github.com/caryll/otfcc)
* [FontForge](https://github.com/fontforge/fontforge)
* [Open Chinese Convert](https://github.com/BYVoid/OpenCC)
* [《正确实现简转繁字体》](https://ayaka.shn.hk/s2tfont/)、[繁媛明朝](https://github.com/ayaka14732/FanWunMing)

