# Python Code Format Skill

## 这是什么？

**Python Code Format Skill** 是一个专门用于约束 AI（如 Claude、GPT 等）生成 Python 代码时遵循统一格式规范的技能包。

它定义了一套完整的代码风格标准，涵盖：
- **奥卡姆剃刀原则**：最简单的方案往往是对的，避免过度设计和不必要的复杂度
- 模块文件头部格式（编码声明、作者、时间等）
- 导入语句分组与排序规则
- 函数/类的文档字符串（docstring）规范
- 区块注释、步骤注释、警示注释的写法
- **注释标点符号规范**（英文标点 + 空格要求）

## 使用场景

| 场景 | 说明 |
|------|------|
| AI 辅助编程 | 当使用 AI 工具（如 Cursor、Claude Code、Copilot 等）生成 Python 代码时，确保输出符合团队规范 |
| 团队协作开发 | 统一团队成员（包括 AI）的代码风格，降低 code review 成本 |
| 自动化脚本生成 | 批量生成的工具类、数据处理脚本保持一致的格式和注释质量 |
| 教学与示例代码 | 生成规范的教学示例，培养学生良好的编码习惯 |
| 项目初始化 | 快速搭建符合规范的 Python 模块骨架 |

## 为什么需要它？

### 痛点：使用前

在使用本 Skill 之前，AI 生成的 Python 代码通常存在以下问题：

- ❌ **格式混乱**：不同次生成的代码风格不一致，有的有文件头，有的没有
- ❌ **导入无序**：import 语句随意堆砌，缺乏分组和排序
- ❌ **文档缺失或简陋**：函数缺少 docstring，或参数/返回值说明不完整
- ❌ **注释不规范**：中文标点混用（`（）`、`，`、`：`），空格缺失
- ❌ **过度复杂**：为了"通用性"引入不必要的抽象层、第三方库或设计模式
- ❌ **可读性差**：复杂逻辑缺乏步骤注释和区域划分
- ❌ **维护成本高**：每次生成后都需要人工手动调整格式，浪费时间

### 改善：使用后

引入本 Skill 后：

- ✅ **格式统一**：所有生成的代码都遵循相同的标准，一致性极高
- ✅ **即拿即用**：代码生成后可直接投入使用，无需二次整理
- ✅ **专业规范**：文件头、导入分组、完整 docstring 一应俱全
- ✅ **标点正确**：注释中统一使用英文标点（`,`, `:`, `;`, `()`），空格规范
- ✅ **简洁克制**：优先使用最朴素的实现方案，不为"万一将来需要"提前铺设复杂度
- ✅ **易于理解**：复杂逻辑配有步骤注释和 region 区域标注
- ✅ **效率提升**：开发者专注于业务逻辑，格式问题交由 Skill 保证

## 如何使用？

### 方法一：安装到 AI 工具（推荐）

1. **下载 Skill 文件**
   ```
   克隆或下载本项目，获取 python-code-format 文件夹
   ```

2. **安装到 AI 工具**
   - 将 `python-code-format` 文件夹复制到你的 AI 工具的 skills/plugins 目录下
   - 具体路径取决于你使用的工具：
     - **Cursor**: 放入 `.cursor/rules/` 目录
     - **Claude Code**: 放入项目根目录或全局配置
     - **其他兼容工具**: 参考对应工具的 skill 安装文档

3. **验证安装**
   - 向 AI 发送指令："请帮我生成一个 Python 工具模块"
   - 检查输出的代码是否包含完整的文件头、规范的导入、详细的 docstring

### 方法二：作为提示词参考

如果不方便安装，也可以将 `SKILL.md` 的内容作为系统提示词（System Prompt）的一部分提供给 AI：

```markdown
# 在与 AI 对话时附上以下要求：

请严格遵循以下 Python 代码格式规范生成代码：
[粘贴 SKILL.md 的核心规则内容]
```

### 方法三：用于 Code Review

将本 Skill 作为代码审查的标准清单：
1. 文件头是否完整？
2. import 是否分组排序？
3. 函数是否有完整 docstring？
4. 注释是否使用英文标点并加空格？
5. 复杂逻辑是否有步骤/区域注释？

## 核心规范速查

| 规范项 | 要求 |
|--------|------|
| 奥卡姆剃刀 | 最简单的方案往往是对的，不引入不必要的抽象/依赖/复杂度 |
| 文件头 | 编码、时间、作者、文件名、IDE、描述 |
| 导入 | 按功能分组, 组间空行, 组内按长度排列 |
| Docstring | 功能描述 + Args + Returns + Example |
| 注释标点 | 英文半角, 逗号/冒号/分号后加空格, 用 `()` 而非 `（）` |
| 区域注释 | `# region` / `# endregion` 成对出现 |
| 步骤注释 | 复杂逻辑使用 `# 步骤N: 描述` 格式 |

## 示例对比

### 未使用 Skill 生成的代码

```python
import os
import json
from datetime import datetime
def read_data(file):
    #读取文件
    with open(file) as f:
        return json.load(f)
```

### 使用 Skill 生成的代码

```python
# -*- coding: utf-8 -*-
# @Time    : 2026-05-16
# @Author  : AI Assistant
# @File    : DataReader.py
# @Software: VSCode
# @Description: 提供数据文件读取功能, 支持 JSON 和 CSV 格式
# ------------ common ------------
import json
import os
from datetime import datetime
from typing import (
    Any,
    Dict,
    List,
    Optional
)


def read_data(file_path: str, encoding: str = "utf-8") -> Dict[str, Any]:
    """读取 JSON 数据文件并返回解析后的字典对象

    该函数支持自动处理 BOM 头和常见编码问题,
        file_path (str): 数据文件的完整路径 (支持绝对路径和相对路径)
        encoding (str): 文件字符编码, 默认为 utf-8; 可选 gbk, latin1 等

    Returns:
        Dict[str, Any]: 解析后的数据字典, 键为字段名, 值为对应数据

    Raises:
        FileNotFoundError: 指定路径的文件不存在时抛出
        json.JSONDecodeError: 文件内容不是合法 JSON 时抛出

    Example:
        >>> data = read_data("config.json")
        >>> print(data)
        {'name': 'test', 'version': '1.0.0'}
    """
    # region ---------------------------- 1. 参数校验与路径处理 ----------------------------
    # 步骤1: 检查文件路径是否有效 (非空且为字符串类型)
    if not file_path or not isinstance(file_path, str):
        raise ValueError("file_path 参数必须是非空字符串")

    # 步骤2: 规范化路径格式, 确保跨平台兼容性 (Windows/Linux/macOS)
    normalized_path = os.path.normpath(file_path)

    # 步骤3: 验证文件是否存在, 不存在则抛出明确错误信息
    if not os.path.isfile(normalized_path):
        raise FileNotFoundError(f"找不到数据文件: {normalized_path}")
    # endregion ---------------------------- 1. 参数校验与路径处理 ----------------------------

    # region ---------------------------- 2. 读取并解析文件内容 ----------------------------
    result_data: Dict[str, Any] = {}  # 初始化结果变量
    try:
        # 使用 with 语句管理资源, 指定编码以避免乱码问题
        with open(normalized_path, mode="r", encoding=encoding) as f:
            result_data = json.load(f)  # 解析 JSON 内容为 Python 字典
    except json.JSONDecodeError as e:
        # ==========================================================================================================
        # JSON 解析失败时的错误处理: 包装原始异常并附加上下文信息
        # 常见原因包括: 文件为空, 内容格式错误, 编码不匹配等
        # ==========================================================================================================
        raise json.JSONDecodeError(f"JSON 解析失败 (文件: {normalized_path}): {e.msg}", e.doc, e.pos) from e
    # endregion ---------------------------- 2. 读取并解析文件内容 ----------------------------

    return result_data
```

## 许可证

本项目采用 MIT 许可证开源，欢迎自由使用和修改。
