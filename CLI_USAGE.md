# 命令行工具使用说明

## 概述

现在你只需要提供一个Excel文件，就能自动生成Android或iOS资源文件，无需通过UI界面操作。

## 快速开始

### 1. 生成Android strings.xml文件

```bash
cd generator_language_config/src
java -cp ".:/Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/library/*" generator_language_config.ExcelToResourceGenerator /path/to/your/data.xlsx /path/to/output android
```

### 2. 生成iOS .strings文件

```bash
cd generator_language_config/src
java -cp ".:/Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/library/*" generator_language_config.ExcelToResourceGenerator /path/to/your/data.xlsx /path/to/output ios
```

## Excel文件格式要求

```
| key    | en    | zh    | fr    | de    |
|---------|-------|-------|-------|-------|
| welcome | Hello | 欢迎你 | Bonjour| Hallo  |
| btn_ok | OK    | 确定   | OK    | OK     |
| <!-- 这是注释 --> | ...    | ...    | ...    | ...     |
```

**格式要求：**
- 第一行第一列必须是 `key`
- 从第二列开始是语言名称（如: en, zh, fr, de）
- 后续每行第一列是key，对应列是翻译文本
- 支持注释：key列中使用 `<!-- 注释内容 -->`

## 自动转义功能

工具已经集成了字符串转义功能，自动处理以下特殊字符：

### Android strings.xml转义
- 单引号 `'` → `\'`
- 双引号 `"` → `\"`
- 反斜杠 `\` → `\\`

### iOS .strings转义
- 单引号 `'` → `\'`
- 双引号 `"` → `\"`
- 反斜杠 `\` → `\\`

这样你就不用担心编译错误了！

## 输出示例

### Android生成结果

```
============================================================
Excel资源文件生成器
============================================================
输入文件: /path/to/data.xlsx
输出目录: /path/to/output
目标平台: Android (strings.xml)
============================================================

正在生成Android strings.xml文件...
✓ Android文件生成完成！

------------------------------------------------------------
生成的文件列表：
------------------------------------------------------------
  ✓ values/strings.xml
  ✓ values-zh/strings.xml
  ✓ values-fr/strings.xml
  ✓ values-de/strings.xml
------------------------------------------------------------
总计: 4 个文件
============================================================
```

### iOS生成结果

```
============================================================
Excel资源文件生成器
============================================================
输入文件: /path/to/data.xlsx
输出目录: /path/to/output
目标平台: iOS (.strings)
============================================================

正在生成iOS .strings文件...
✓ iOS文件生成完成！

------------------------------------------------------------
生成的文件列表：
------------------------------------------------------------
  ✓ en.strings
  ✓ zh.strings
  ✓ fr.strings
  ✓ de.strings
------------------------------------------------------------
总计: 4 个文件
============================================================
```

## 批量处理建议

如果你需要处理多个Excel文件，可以创建批处理脚本：

### macOS/Linux

```bash
#!/bin/bash

# 生成所有Excel文件
for excel in /path/to/excels/*.xlsx; do
    java -cp ".:/Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/library/*" \
        generator_language_config.ExcelToResourceGenerator \
        "$excel" \
        /path/to/output \
        android
done
```

### Windows

```batch
@echo off

for %%f in (C:\path\to\excels\*.xlsx) do (
    java -cp ".;C:\path\to\library\*" generator_language_config.ExcelToResourceGenerator "%%f" C:\path\to\output android
)
```

## 与UI工具的对比

| 特性 | UI工具 | 命令行工具 |
|------|----------|--------------|
| 操作方式 | 图形界面 | 命令行 |
| 批量处理 | 不方便 | 非常方便 |
| 自动化集成 | 需要手动点击 | 可脚本化 |
| 使用难度 | 简单但慢 | 快速高效 |

## 故障排查

### 编译错误

如果遇到类找不到错误，确保编译顺序：

```bash
# 1. 先编译StringEscapeUtil
javac -encoding UTF-8 -cp "/Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/library/*" \
    -d /Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/src \
    /Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/src/generator_language_config/util/StringEscapeUtil.java

# 2. 编译其他util类
javac -encoding UTF-8 -cp "/Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/library/*:/Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/src" \
    -d /Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/src \
    /Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/src/generator_language_config/util/ExcelUtil.java \
    /Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/src/generator_language_config/util/XMLUtil.java \
    /Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/src/generator_language_config/util/StringsUtil.java

# 3. 编译主工具
javac -encoding UTF-8 -cp "/Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/library/*:/Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/src" \
    -d /Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/src \
    /Users/genius/Documents/StudioProject/generate_strings_tool/generator_language_config/src/generator_language_config/ExcelToResourceGenerator.java
```

### 运行错误

如果遇到 `ClassNotFoundException`，检查classpath路径是否正确。

## 更新历史

### v1.1 (2025-02-06)
- ✅ 创建命令行工具类 ExcelToResourceGenerator
- ✅ 集成统一的字符串转义功能（StringEscapeUtil）
- ✅ 自动检测Excel语言列并批量生成
- ✅ 支持Android和iOS双平台输出
- ✅ 添加详细的帮助文档和使用示例
