# 6. 输出工作区

## 为什么有这条规则

LLM 编码会话会产生各种制品：临时脚本、生成的报告、截图、日志文件。如果没有一个指定的存放位置，这些东西会在项目根目录下堆积——与源文件、配置文件和构建产物混在一起——让人无法区分哪些是永久的、哪些是可丢弃的。

这条规则强制了两项规范：（1）所有制品的放入 `Claude_Code_Files/`，绝不放入项目根目录；（2）每次会话使用一个带日期的子文件夹。日期前缀（`YYYYMMDD-`）意味着文件夹按时间顺序排列；描述后缀则使其可被找到。

一个具体案例：今天的会话产生了三个临时 Python 脚本（`check_quotes.py`、`fix_quotes.py`、`fix_savefig.py`）和一个 skill 仓库。它们分别放入了 `20260530-skill-review/`（临时）和 `20260530-academic-writing-toolkit-dev/`（项目）。没有一个文件落到项目根目录。

## 实践中是什么样子

**坏——根目录混乱：**
```
myproject/
├── fix.py
├── fix2.py
├── check_output.py
├── report.md
├── screenshot.png
├── src/
├── ...
```
哪些文件是项目源码、哪些是会话制品？不阅读内容就无从知晓。

**好——带日期和描述的文件夹：**
```
myproject/
├── Claude_Code_Files/
│   ├── 20260521-legado-espresso/
│   │   ├── test-report.md
│   │   └── screenshots/
│   └── 20260530-skill-review/
│       ├── check_quotes.py
│       └── fix_quotes.py
├── src/
├── ...
```
永久文件与临时文件泾渭分明。

## 什么时候可以放宽

对于不产生文件的、一次性的简单命令（如 `git status`），这条规则没有额外开销。这条规则在你要向磁盘写入文件时激活——问问自己：这个东西属于项目的永久组成部分，还是一个会话制品？
