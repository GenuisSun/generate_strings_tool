# AGENTS.md

This document provides coding guidelines for agentic contributors working in the **generate_strings_tool** repository.

## Project Overview

Java Swing desktop application for converting between Excel files and localized resource files (Android `strings.xml` and iOS `.strings`). Built with IntelliJ IDEA.

## Build & Run

**Build:** Use IntelliJ IDEA's built-in Build → Make Project (Cmd+F9 on macOS)
**Run:** Run main class `generator_language_config.UIMain` (Ctrl+R on macOS)

No Maven/Gradle build system. Dependencies are in `generator_language_config/library/`.

## Dependencies

- Apache POI 3.8 (Excel processing)
- Dom4j 1.6.1 (XML processing)
- Java Swing (UI)

Add new JARs to `generator_language_config/library/` and update `.iml` module files.

## Code Style Guidelines

### Imports
Group imports by category in this order:
1. Third-party libraries (alphabetical)
2. `java.*` (standard library, alphabetical)
3. Project packages (`generator_language_config.*`)

Use static imports sparingly, only for constants across packages.

```java
import org.apache.poi.hssf.usermodel.*;
import org.dom4j.Document;

import java.io.File;
import java.io.IOException;
import java.util.Map;

import generator_language_config.util.ExcelUtil;
```

### Naming Conventions
- **Classes:** `PascalCase` - `ExcelUtil`, `MainFrame`, `StringEntity`
- **Methods/Variables:** `camelCase` - `readExcel`, `outputFile`, `processData`
- **Constants:** `UPPER_SNAKE_CASE` - `KEY_FLAG`, `ANNOTATION_FLAG`, `DEFAULT_FLAG`
- **Packages:** `lowercase` - `generator_language_config.ui`, `generator_language_config.util`

### Formatting
- 4-space indentation (no tabs)
- Use `LinkedHashMap` when order matters (XML/string resource generation)
- Prefer `Map<String, Object>` for flexible value types
- Braces on same line for methods and control structures

### Error Handling
- Throw `IOException` or `DocumentException` for file operations
- Print stack traces for unexpected exceptions: `e.printStackTrace()`
- Use `JOptionPane.showMessageDialog()` for user-facing errors:
  ```java
  JOptionPane.showMessageDialog(null, "请选择Excel", "提示", JOptionPane.WARNING_MESSAGE);
  ```

### File I/O
- Use `FileInputStream`/`FileOutputStream` for binary files
- Use `BufferedReader`/`BufferedWriter` for text files
- Always close resources (use try-catch, not try-with-resources due to legacy style)
- Create directories with `file.getParentFile().mkdirs()`

### UI Patterns (Swing)
- Extend `JFrame` for main windows
- Implement `ActionListener` for event handling
- Use `null` layout with absolute positioning (legacy style)
- Pattern: Button creation → setBounds → add to frame → add listener
```java
private JButton generateButton;

public MyFrame() {
    super("Title");
    initUI();
    this.setSize(500, 400);
    this.setLocationRelativeTo(null);
    this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
}

private void initUI() {
    this.setLayout(null);
    generateButton = new JButton("开始生成");
    generateButton.setBounds(150, 260, 200, 40);
    generateButton.addActionListener(this);
    this.add(generateButton);
}
```

### Special Constants
- `ExcelUtil.ANNOTATION_FLAG = "##ANN##"` - Marks comment entries in Excel
- `ExcelUtil.KEY_FLAG = "key"` - First column must be "key" in Excel
- Comments in Excel key column: `<!-- comment -->`

### Testing
No test framework present. To add tests manually:
1. Create test directory structure: `generator_language_config/test/`
2. Write test files following same package structure
3. Use standard Java assertions for validation

## Key Utility Classes

- **ExcelUtil**: Excel ↔ XML conversion
- **StringsUtil**: Excel ↔ iOS .strings conversion
- **XMLUtil**: XML read/write with Dom4j
- **ExcelFilter/XmlFilter/StringsFilter**: JFileChooser file filters

## Entry Point

`generator_language_config.UIMain.main(String[] args)` - launches the main menu frame
