---
name: python-code-format
description: 当用户请求编写或在某项任务中需要生成 Python 代码时使用，确保代码符合格式规范并遵循奥卡姆剃刀原则。
---

# Python 代码格式规范技能

## 目标
在生成任何 Python 代码时，必须严格遵守以下格式要求，以确保代码可读性、可维护性和一致性。

核心原则：**最简单的代码往往是最好的代码**，任何人看到时不需要经过思考就能立刻明白它在干什么。能用朴素方案解决的，不要引入不必要的复杂度。注释的本质是替读者省去"推理代码意图"的时间，而不是复述语法。

## 核心规则

### 模块文件头部格式
- 文件开头必须有编码声明、时间、作者、文件名、IDE 和描述。

格式如下：

```python
# -*- coding: utf-8 -*-
# @Time    : {日期，格式YYYY-mm-DD}
# @Author  : {大模型名称}
# @File    : {文件名(包含后缀)}
# @Software: {IDE 名称，如 VSCode, PyCharm}
# @Description: {该代码的作用是什么}

# （此处接导入语句，参考下方"第三方库导入"规则）
```

所有用 `{}` 包裹的占位符，必须根据上下文替换成合适的内容。

例子：`{姓名}` 替换成 `john`，`{日期}` 替换为当前日期。

### 空行规则

- **文件头部与后续代码之间**：一个空行。
- **顶级函数/类之间**：两个空行（遵循 PEP 8）。
- **类内部方法之间**：一个空行（遵循 PEP 8）。
- **导入区域结束与后续代码之间**：两个空行。
- **函数内部逻辑块之间**：可用一个空行分隔不同逻辑段落，但不宜滥用。

### 模块命名规则

- **工具类（utils）模块**：统一使用**大驼峰命名法（PascalCase）**。例如：`DateTimeUtils.py`、`FilePathUtils.py`、`StringFormatter.py`。
- **其他功能模块**：统一使用**蛇形命名法（snake_case）**。例如：`business_model.py`、`api_connector.py`、`text_cleaner.py`、`config_reader.py`。

这一规则同时适用于模块文件名和导入时的别名（如有必要），保持项目内命名风格一致。

### 变量命名补充规则

- **布尔变量**：使用 `is_`、`has_`、`can_`、`should_` 等前缀，使意图一目了然。
  - ✅ `is_active`、`has_permission`、`can_execute`
  - ❌ `active`、`permission`、`executable`
- **私有变量/方法**：单下划线 `_name` 表示模块/类内部使用（protected），双下划线 `__name` 触发名称改写（name mangling），仅在需要避免子类属性冲突时使用双下划线。
- **集合变量**：使用复数形式命名列表/字典/集合。
  - ✅ `users`、`error_messages`、`config_map`
  - ❌ `user_list`、`error_dict`（类型后缀冗余，除非同一作用域有多种集合类型需要区分）

### 常量定义规范

- 常量使用**全大写蛇形命名法（UPPER_SNAKE_CASE）**。
- 所有常量集中定义在模块顶部（位于导入语句之后，函数/类定义之前）。
- 禁止在函数体内部散布魔法数字；超过一次使用的字面量必须提取为常量。
- 模块级常量较多时（超过 10 个），考虑抽取到独立的 `constants.py` 或 `config.py` 中。

示例：

```python
# ------------ 常量定义 ------------
DEFAULT_TIMEOUT_SECONDS = 30  # 第三方 API 文档建议超时不超过 30 秒
MAX_RETRY_COUNT = 3  # 失败重试上限，超过此值视为不可恢复错误
CSV_EXPORT_ENCODING = "utf-8-sig"  # 带 BOM 的 UTF-8，确保 Excel 正确识别中文
DB_CONNECTION_POOL_SIZE = 10  # 连接池大小，根据服务器 4 核 CPU 设定
```

### 枚举（Enum）使用规范

- 当一组固定值在代码中反复出现时，必须使用 `enum.Enum` 替代字符串常量或整数常量。
- 枚举类名使用 PascalCase，成员名使用 UPPER_SNAKE_CASE。
- 如枚举值不需要关心具体数值，使用 `auto()` 自动分配。
- 枚举成员需添加行内注释说明含义。

示例：

```python
from enum import Enum, auto


class TaskStatus(Enum):
    """任务状态枚举"""
    PENDING = auto()  # 等待执行
    RUNNING = auto()  # 正在执行
    SUCCESS = auto()  # 执行成功
    FAILED = auto()  # 执行失败
    CANCELLED = auto()  # 已取消


class HttpMethod(Enum):
    """HTTP 请求方法枚举"""
    GET = "GET"
    POST = "POST"
    PUT = "PUT"
    DELETE = "DELETE"
```

### 第三方库导入

- **所有导入语句按项目功能模块分组**，组名根据模块的实际功能自定义，例如 `common`、`database`、`string-formatter`、`api-connector`、`business`、`tools` 等。
- 组间用注释空行分隔。每组之间间隔一行
- 分组名称和数量完全取决于当前项目的模块划分，不应机械套用固定名称

- 每组内的导入按**字母序**排列（标准库、第三方库、项目内模块依次），尽可能使用 `from ... import` ，并且在有多个`from ... import`对象时换行并对齐
- 组注释格式：`# ------------ 组名 ------------`

示例（展示不同项目可能的分组）：

```python
# ------------ common ------------
import os
import sys
import random
from datetime import datetime
from datetime import timedelta
from typing import (
    Any,
    List,
    Dict,
    Optional,
    Tuple
)

# ------------ database ------------
import sqlite3

# ------------ api-connector ------------
import httpx
from requests.auth import HTTPBasicAuth

# ------------ business ------------
import business_model

# ------------ utils/tools ------------
from utils import text_cleaner
from utils import DateTimeUtils
from utils import FilePathUtils
from utils import unicode_normalizer
```

- 导入区域结束与后续代码之间应有一个空行分隔。

### 类型注解规范

- 所有公共函数/方法的**参数和返回值**必须添加类型注解。
- 私有函数/方法的类型注解建议添加，但非强制。
- 类属性和实例属性建议使用注解语法声明类型（而非仅在 docstring 中描述）。
- 复杂的联合类型、可选类型使用 `typing` 模块提供的 `Optional`、`Union`、`Any` 等。
- 循环引用类型可使用 `from __future__ import annotations` 或字符串形式的前向引用。

示例：

```python
from __future__ import annotations

from typing import Optional, Union

class ExampleClass:
    """类型注解示例类"""

    def process(self, data: list[str], count: int = 0) -> Optional[str]:
        """处理数据并返回结果

        Args:
            data (list[str]): 待处理的字符串列表
            count (int): 处理次数上限，默认为 0 表示不限制

        Returns:
            Optional[str]: 处理结果，无数据时返回 None
        """
        if not data:
            return None
        return ", ".join(data[:count] if count > 0 else data)
```

### 函数文档字符串

- 每个函数必须包含详细的 docstring，说明功能、参数、返回值、可能抛出的异常和示例。
- docstring 按以下顺序组织：一行摘要 → 详细描述（可选） → `Args:` → `Returns:` → `Raises:` → `Example:`
- `Raises:` 部分列出该函数可能抛出的异常及触发条件，**当函数内部显式 `raise` 了异常时必须书写**，仅使用内置函数/库默认抛出的异常可省略。
- 示例部分必须使用 `Example:` 标题，并给出可运行的代码和预期输出。
- **例外规则**：
  - `@property` 装饰的方法：跳过 `Args:` 和 `Raises:` 部分
  - `__init__` 方法：跳过 `Returns:` 部分（构造方法隐式返回 None）
  - `main()` 入口函数：`Returns:`、`Raises:` 和 `Example:` 可选，视复杂度而定

格式为：

```python
def func_name(arg: str) -> int:
    """这个函数的作用是什么

    Args:
        arg (str): 这个参数的作用是什么

    Returns:
        int: 返回什么

    Raises:
        ValueError: 当 arg 为空字符串时抛出

    Example:
        >>> results = func_name("test")
        >>> print(results)
        1
    """
    return 1
```

### 类定义

- 类名使用大驼峰命名法（PascalCase）。
- 方法名使用蛇形命名法（snake_case）。
- 类变量和实例变量需要注释说明。
- 类方法和普通函数一样需要完整 docstring。

示例：

```python
class ClassName:
    """这个类的作用"""
    class_arg_name = ""  # 类变量注释，描述它的作用

    def __init__(self, arg1: str):
        """初始化 ClassName 实例

        Args:
            arg1 (str): 参数1的描述

        Raises:
            ValueError: 当 arg1 为空字符串时抛出
        """

    def class_func_name(self, arg: str) -> int:
        """这个函数的作用是什么

        Args:
            arg (str): 这个参数的作用是什么

        Returns:
            int: 返回什么

        Example:
            >>> results = obj.class_func_name("data")
            >>> print(results)
            1
        """
        return 1
```

### 错误处理规范

- **优先使用内置异常**：`ValueError`、`TypeError`、`FileNotFoundError` 等。仅在需要按异常类型分流业务逻辑时，才定义自定义异常类。
- **异常链**：捕获异常后重新抛出时，必须使用 `raise ... from e` 保留原始堆栈信息，而非丢失因果链。
- **精确捕获**：仅捕获预期的具体异常类型，**禁止**使用裸露的 `except:` 或宽泛的 `except Exception:`，除非在最外层入口做兜底记录。
- **清理资源**：涉及文件、网络、数据库连接的资源管理，优先使用 `with` 语句（上下文管理器），而非 `try/finally` 手动关闭。

示例：

```python
import json
from pathlib import Path


class ConfigParseError(Exception):
    """配置文件解析异常，业务层可根据此类型进行降级处理"""


def load_config(file_path: str) -> dict:
    """加载并解析 JSON 配置文件

    Args:
        file_path (str): 配置文件路径

    Returns:
        dict: 解析后的配置字典

    Raises:
        FileNotFoundError: 配置文件不存在
        ConfigParseError: JSON 解析失败
    """
    config_path = Path(file_path)
    if not config_path.is_file():
        raise FileNotFoundError(f"配置文件不存在: {file_path}")

    try:
        with open(config_path, encoding="utf-8") as f:
            return json.load(f)
    except json.JSONDecodeError as e:
        raise ConfigParseError(f"JSON 格式错误: {e}") from e
```

### 字符串格式化规范

- **优先使用 f-string**（Python 3.6+）：可读性最高，性能最好。
- 需要在多处复用同一模板时，使用 `str.format()`。
- **禁止使用 `%` 格式化**（`"%s" % value`），该方式已过时且容易出错。
- f-string 中不要写入复杂表达式；如需计算，先赋值给变量再引用。

示例：

```python
# ✅ f-string——简洁直观
name = "Alice"
count = 5
message = f"用户 {name} 有 {count} 条未读消息"

# ✅ str.format——模板复用场景
TEMPLATE = "文件 {filename} 第 {line} 行: {error}"
error_msg = TEMPLATE.format(filename="data.csv", line=42, error="字段缺失")

# ❌ 禁止
message = "用户 %s 有 %d 条未读消息" % (name, count)
```

### 上下文管理器使用规范

- 所有需要显式释放的资源（文件、网络连接、数据库游标、锁等）**必须**使用 `with` 语句管理。
- 自定义需要资源管理的类时，实现 `__enter__` 和 `__exit__` 方法使其成为上下文管理器。
- 简单场景也可用 `contextlib.contextmanager` 装饰器快速创建。

示例：

```python
from pathlib import Path

# ✅ 文件操作使用 with
with open("data.csv", encoding="utf-8") as f:
    content = f.read()

# ✅ 使用 pathlib 的写入方法（自动管理资源）
Path("output.txt").write_text("hello", encoding="utf-8")

# ❌ 手动管理——容易遗漏关闭
f = open("data.csv", encoding="utf-8")
content = f.read()
f.close()
```

### 区块注释

- 当代码超过 8-10 行时，使用 `region` 风格的区块注释，标明代码作用。
- 注释必须成对出现：`# region ---------------------------- 描述 ----------------------------` 和 `# endregion ---------------------------- 描述 ----------------------------`
- 横线数量统一为 28 个 `-`，以保证视觉对齐

实例：

```python
# region ---------------------------- 这一块代码的作用(简要描述) ----------------------------
func_name(arg=1)
func_name(arg=2)
func_name(arg=3)
# endregion ---------------------------- 这一块代码的作用(简要描述) ----------------------------
```

### 重要/警示注释

- 对需要谨慎修改、可能产生重大影响、或不得已而为之的代码，使用醒目的多行注释。
- 每行文本不宜过长，可适当换行。
- 警示注释采用成对标记，首尾各一行 `===` 分隔线，保证视觉上清晰闭合。

格式：

```python
# ==========================================================================================================
# 描述如下步骤的做法，如果一行的文本太多，注意换行，保证一行的注释文本不要太多
# 就像这样换行
# ==========================================================================================================
func_name(arg=3)
# ==========================================================================================================
```

### 注释标点符号规范

- **必须使用英文标点符号**：所有代码注释中的标点符号统一使用英文半角字符，禁止使用中文全角标点。
- **括号**：使用英文圆括号 `()` 而非中文全角 `（）`。
  - ✅ 正确：`# 读取文件内容 (包含编码处理)`
  - ❌ 错误：`# 读取文件内容（包含编码处理）`
- **逗号**：使用英文逗号 `,` 并在后面加空格。
  - ✅ 正确：`# 参数包括file_path, delimiter, encoding`
  - ❌ 错误：`# 参数包括file_path，delimiter，encoding`
- **冒号**：使用英文冒号 `:` 并在后面加空格（用于列表、解释等场景）。
  - ✅ 正确：`# Args: file_path, mode, encoding`
  - ❌ 错误：`# Args：file_path，mode，encoding`
- **分号**：使用英文分号 `;` 并在后面加空格（如需在注释中使用分号分隔多项）。
  - ✅ 正确：`# 支持格式: json; csv; xml; yaml`
  - ❌ 错误：`# 支持格式：json；csv；xml；yaml`
- **句号**：使用英文句号 `.` 后面加空格（如需完整句子结尾）。
  - ✅ 正确：`# This is a complete sentence. Next part...`
  - ❌ 错误：`# 这是一个完整句子。下一部分...`
- **感叹号/问号**：使用英文 `!` / `?`，后面加空格（如有后续文字）。
- **空格规范总结**：
  - 英文逗号、冒号、分号等标点**后面必须加空格**
  - 英文括号 `( )` 内外侧与文字之间**加空格**（除非内侧紧跟换行或无内容）
  - 示例：`# 处理数据 (支持多种格式) 并返回结果`

### 行内注释格式

- **行内注释必须紧跟在代码之后**，格式为：`代码 + 空格 + # + 空格 + 注释文本`
- **禁止使用大量空格对齐行内注释**：不要为了让同一区域内的多行注释对齐而插入大量空格。每行的行内注释独立放置，仅保留代码与 `#` 之间一个空格即可。

格式对比：

```python
# ❌ 错误示例: 使用大量空格对齐行内注释
file_path = os.path.normpath(file_path)                       # 规范化路径
rows_data = []                                                # 存储最终结果的列表
csv_reader = csv.DictReader(f, delimiter=delimiter)           # DictReader直接返回字典形式
rows_data.append(dict(row))                                   # 转换为普通dict，避免后续引用问题

# ✅ 正确示例: 代码后仅跟一个空格，不额外对齐
file_path = os.path.normpath(file_path) # 规范化路径
rows_data = [] # 存储最终结果的列表
csv_reader = csv.DictReader(f, delimiter=delimiter) # DictReader直接返回字典形式
rows_data.append(dict(row)) # 转换为普通dict，避免后续引用问题
```

- 正确格式规则：`代码 # 注释文本`（代码与 `#` 之间一个空格，`#` 与注释文本之间一个空格）
- 不再使用的代码**不应**以行内注释形式注释掉，应直接删除。

#### 必须添加行内注释的场景

以下四类代码**必须**有行内注释，缺少注释会显著增加理解成本：

- **非显而易见的字面量**：魔法数字、硬编码常量、特殊格式字符串、正则表达式等，注释应说明"为什么是这个值"而非"这个值是什么"。
  - ✅ `timeout = 30  # 第三方API文档建议超时不超过30秒`
  - ❌ `timeout = 30  # 设置超时为30秒`
- **有副作用或依赖外部状态的操作**：文件 I/O、网络请求、数据库操作、全局变量修改等。
  - ✅ `os.makedirs(output_dir, exist_ok=True)  # 递归创建目录，已存在也不报错`
- **算法核心步骤 / 非直觉写法**：循环中的关键分支、递归终止条件、性能优化技巧、反直觉的实现方式。
  - ✅ `idx = max(0, min(idx, len(arr) - 1))  # 将索引钳制在合法范围内，防止越界`
- **容易产生歧义的类型转换或运算**：隐式类型转换、位运算、精度敏感的浮点计算等。
  - ✅ `timestamp_ms = int(t.timestamp() * 1000)  # datetime转毫秒时间戳，API要求整数`

#### 不应添加行内注释的场景

以下情况添加行内注释反而干扰阅读，应该省略：

- **自解释的命名**：变量/函数名已经清晰表达了含义。
  - ❌ `user_name = "Alice"  # 设置用户名为Alice`（完全多余）
- **纯组装/透传逻辑**：仅调用命名良好的函数，无额外处理。
  - ❌ `result = calculate_average(data)  # 计算平均值`（函数名已说明一切）
- **标准的 getter/setter / property**：无额外业务逻辑。
  - ❌ `return self._value  # 返回私有属性的值`（多余）

#### 密度控制

- **理想密度**：每 3～8 行代码至少有一条行内注释，但不宜每行都加。
- **判断标准**：一个不熟悉该模块的人，扫描代码时能否在 3 秒内把握每一块的意图。如果能，密度合适；如果某段代码需要停下来推演才能理解，说明注释不够或位置不对。
- **均匀分布**：注释应分散在关键节点上，而不是集中堆在某一个区域。

#### 对比示例

```python
# ✅ 合理密度——关键步骤有注释，自解释代码略过
total = 0
for item in items:
    if item.status != Status.ACTIVE:  # 跳过非活跃状态的项
        continue
    total += item.price * 1.13  # 含13%消费税
return round(total, 2)  # 保留两位小数，避免浮点误差

# ❌ 注释过多——每行都注释，干扰阅读
total = 0  # 初始化总和为0
for item in items:  # 遍历items列表
    if item.status != Status.ACTIVE:  # 如果项的状态不是活跃
        continue  # 跳过本次循环
    total += item.price * 1.13  # 总数加上价格乘1.13
return round(total, 2)  # 返回保留两位小数的结果

# ❌ 注释过少——关键逻辑缺乏解释，需推演才能理解
total = 0
for item in items:
    if item.status != Status.ACTIVE:
        continue
    total += item.price * 1.13
return round(total, 2)
```

### `__all__` 导出规范

- 被其他模块 `import *` 使用的模块，必须定义 `__all__` 列表明确公开 API。
- `__all__` 中字符串按字母序排列。
- 仅放入确实需要被外部使用的公共符号，内部辅助函数/类不应列入。
- 没有 `from xxx import *` 使用场景的脚本/内部模块可省略 `__all__`。

示例：

```python
__all__ = [
    "ConfigParseError",
    "load_config",
    "save_config",
]
```

### `if __name__ == "__main__"` 入口规范

- 可直接执行的 Python 文件必须在末尾包含 `if __name__ == "__main__":` 保护块。
- 入口逻辑封装在一个 `main()` 函数中，`if __name__` 块内只调用 `main()`。
- `main()` 函数的 docstring 中 `Returns:`、`Raises:`、`Example:` 可选。
- 如需命令行参数，使用 `argparse` 在 `main()` 内部解析，不暴露裸 `sys.argv`。

示例：

```python
import argparse


def main() -> None:
    """入口函数：解析命令行参数并执行主逻辑"""
    parser = argparse.ArgumentParser(description="CSV 文件处理工具")
    parser.add_argument("file_path", help="CSV 文件路径")
    parser.add_argument("--delimiter", default=",", help="分隔符，默认逗号")
    args = parser.parse_args()

    data = read_csv(args.file_path, delimiter=args.delimiter)
    print(f"读取到 {len(data)} 行数据")


if __name__ == "__main__":
    main()
```

### 文件编码规范补充

- Python 3 默认使用 UTF-8 编码，`# -*- coding: utf-8 -*-` 声明主要用于向后兼容和编辑器识别，建议保留。
- 读写外部文件时，**显式指定 `encoding="utf-8"`**，不要依赖系统默认编码。
- 处理 Excel 导出的 CSV 时，优先使用 `encoding="utf-8-sig"` 以去除 BOM 头。
- 需要兼容 GBK/GB2312 等非 UTF-8 编码的遗留系统时，在 `open()` 中显式声明编码并添加注释说明原因。

示例：

```python
# ✅ 显式指定编码
with open("report.csv", encoding="utf-8-sig") as f:  # utf-8-sig 去除 BOM，兼容 Excel 导出
    data = f.read()

# ✅ 兼容遗留系统——注释说明原因
with open("legacy_data.csv", encoding="gbk") as f:  # 上游系统使用 GBK 编码，无法修改
    data = f.read()
```

### 函数长度与复杂度参考

- 单个函数建议不超过 50 行（不含 docstring 和空行）。
- 如果函数超过此阈值，考虑拆分为多个职责单一的子函数。
- 圈复杂度（cyclomatic complexity）建议控制在 10 以内；如果 `if/for/while` 嵌套超过 3 层，应重构。
- 这并非硬性规则，当拆分会破坏逻辑内聚性时允许适当超出，但需要添加充分的区域注释解释原因。

### 奥卡姆剃刀原则：最简单的方案往往是对的

在选择实现方式时，优先选择**最朴素、最直觉的方案**，除非有明确的性能数据或正确性证据要求你选择更复杂的路径。

判断"更简单"不是凭感觉，而是多维度比较：
- **代码行数更少**（在可读性不下降的前提下）
- **引入的抽象更少**（少定义一个类层级、少创建一个中间抽象）
- **依赖更少**（少用一个第三方库、不引入不必要的模块或外部服务）
- **执行路径更短**（更少的 if/else 分支、更少的中间跳转）

复杂度应该是"被证明需要"之后才引入的，而非"万一将来需要"就提前铺设。

#### 数据结构选择

能用内置类型解决的，不引入第三方库；能用简单结构解决的，不做过度封装。

**判据**：如果你需要引入一个外部依赖（如 `pandas`、`numpy`）仅仅是为了一个内置类型也能完成的操作，停下来重新评估。

```python
# ❌ 过度复杂 —— 为了取 CSV 某列的最大值引入 pandas
import pandas as pd

df = pd.read_csv("data.csv")
results = df["score"].max().tolist()

# ✅ 简单直接 —— 只用标准库，一个生成器表达式解决
import csv

with open("data.csv", encoding="utf-8-sig") as f:
    reader = csv.DictReader(f)
    results = max(int(row["score"]) for row in reader)
```

> 并不是说 `pandas` 不好——如果后续确实需要大量数据清洗、分组聚合，引入它是合理的。但当需求仅限于读取一列求最大值时，标准库的代码更轻、依赖更少、别人理解成本更低。

#### 算法与实现路径选择

当存在多种等效实现时，选择逻辑最直白、执行路径最短的那个。不要因为"更优雅"或"更通用"而选择迂回的实现方式。

**判据**：如果一个方案让你需要停下来解释"为什么要这么写"，而另一个方案一眼就能看懂，选后者。

```python
# ❌ 过度泛化 —— 为了"通用性"牺牲了直白性
from functools import reduce
from operator import add

def sum_positive(numbers: list[int]) -> int:
    """求和所有正数"""
    return reduce(add, filter(lambda x: x > 0, numbers), 0)

# ✅ 简单直接 —— 生成器表达式，一眼看懂在干什么
def sum_positive(numbers: list[int]) -> int:
    """求和所有正数"""
    return sum(x for x in numbers if x > 0)
```

> 两段代码功能完全等价。前者用了 `reduce` + `filter` + `lambda` 三层嵌套，需要读者在脑中展开函数组合链；后者用 `sum` + 生成器表达式，从左到右读下来就是"对每个 x，如果大于 0 就加总"。简单方案在可读性、调试便捷性上都胜出。

#### 抽象层级选择

不要为单一调用者创建抽象层。接口、基类、策略模式等抽象手段，只有当第二个使用场景真实出现时才引入。

**判据**：如果一个抽象目前只有一个实现类 / 一个调用者，它就是不必要的。

```python
# ❌ 过度抽象 —— 只有一个实现，接口和基类都是噪音
from abc import ABC, abstractmethod


class ReportExporter(ABC):
    """报表导出器抽象基类"""

    @abstractmethod
    def export(self, data: list[dict]) -> str:
        ...


class CsvExporter(ReportExporter):
    """CSV 格式导出实现"""

    def export(self, data: list[dict]) -> str:
        headers = ",".join(data[0].keys()) if data else ""
        rows = "\n".join(",".join(str(v) for v in row.values()) for row in data)
        return f"{headers}\n{rows}"


exporter = CsvExporter()
result = exporter.export(data)

# ✅ 恰到好处 —— 当前只有一个导出需求，一个函数足够
def export_to_csv(data: list[dict]) -> str:
    """将数据导出为 CSV 格式的字符串"""
    headers = ",".join(data[0].keys()) if data else ""
    rows = "\n".join(",".join(str(v) for v in row.values()) for row in data)
    return f"{headers}\n{rows}"


result = export_to_csv(data)
```

> 当未来确实需要支持 JSON、Excel 等多种导出格式时，再引入 `ReportExporter` 抽象基类和多个子类——那时重构也只需要几分钟。提前设计抽象层级，本质上是为尚未发生的问题付费。

#### 代码组织与拆分

不要为了"拆分"而拆分。如果一个函数拆开后，子函数只被一处调用且逻辑不超过 3 行，拆回去更简单。

**判据**：拆分前先问自己——这个子函数有没有独立的名称含义？会不会被复用？如果两个答案都是"否"，就留在原地。

```python
# ❌ 过度拆分 —— 3 行逻辑拆成两个函数，读者需要来回跳转
def calculate_average(scores: list[int]) -> float:
    """计算平均分"""
    return _divide(_sum(scores), _count(scores))


def _sum(scores: list[int]) -> int:
    return sum(scores)


def _count(scores: list[int]) -> int:
    return len(scores)


def _divide(total: int, count: int) -> float:
    return total / count if count > 0 else 0.0

# ✅ 恰到好处 —— 逻辑直白，无需跳转
def calculate_average(scores: list[int]) -> float:
    """计算平均分"""
    if not scores:
        return 0.0
    return sum(scores) / len(scores)
```

> 拆分的目的不是让每个函数尽可能短，而是让每个函数有一个清晰的、独立于其他函数的含义。当子函数的名称和其内容一样长时，说明拆分的意义不大。

### 代码质量检查规则

- 项目推荐使用以下工具组合保证代码质量（按推荐优先级排列）：
  - **Ruff**：统一的 linter + formatter，替代 flake8/isort/black，速度快
  - **Mypy** 或 **Pyright**：静态类型检查
- 提交代码前必须确保 linter 和类型检查无新增错误。
- 推荐在项目根目录配置 `pyproject.toml` 统一规则：

```toml
[tool.ruff]
line-length = 120
target-version = "py310"

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W", "UP"]

[tool.ruff.format]
quote-style = "double"

[tool.mypy]
python_version = "3.10"
strict = true
```

## 执行要求

- 生成任何代码时，必须**完整应用**上述格式。
- 注释要通俗易懂，数量要充分。
- 保持代码整洁，避免无意义的空行，但必要的分组空行必须保留。
- 对复杂度高的代码，应尽量使用区域注释，降低理解成本。

## 使用示例

当用户请求"写一个读取CSV文件的函数"时，应输出包含完整文件头、导入、docstring、示例和必要区块注释的格式化代码。

示例代码，生成的模块命名为`CSVUtils.py`

```python
# -*- coding: utf-8 -*-
# @Time    : 2026-04-26
# @Author  : AI Assistant
# @File    : CSVUtils.py
# @Software: VSCode
# @Description: 提供CSV文件读取功能的工具模块，支持自定义分隔符，返回字典列表

# ------------ common ------------
import csv
import os
import sys
from typing import (
    Any,
    Dict,
    List,
    Optional
)

# ------------ tools ------------
from utils import DateTimeUtils
from utils import FilePathUtils


def read_csv(file_path: str, delimiter: str = ",") -> List[Dict[str, str]]:
    """读取CSV文件并返回包含字典的列表，每行数据转换为一个字典

    Args:
        file_path (str): CSV文件的完整路径
        delimiter (str): 文件使用的分隔符，默认为逗号

    Returns:
        List[Dict[str, str]]: 由每行数据组成的字典列表，键为列名，值为对应字段内容

    Raises:
        ValueError: 当 file_path 为空时抛出
        FileNotFoundError: 当指定路径的文件不存在时抛出
        RuntimeError: 当 CSV 解析失败时抛出
        PermissionError: 当没有文件读取权限时抛出

    Example:
        >>> data = read_csv("example.csv")
        >>> print(data)
        [
            {'姓名': '张三', '年龄': '25', '城市': '北京'},
            {'姓名': '李四', '年龄': '30', '城市': '上海'}
        ]
    """
    # region ---------------------------- 参数校验与路径处理 ----------------------------
    # 后续 os.path.isfile 对空字符串在部分平台上会异常返回，提前拦截
    if not file_path:
        raise ValueError("文件路径不能为空")
    file_path = os.path.normpath(file_path) # 消除路径中的 '.' 和 '..'，避免 isfile 误判
    # 使用 isfile 而非 exists，排除目录路径被误传入的情况
    if not os.path.isfile(file_path):
        raise FileNotFoundError(f"找不到指定的CSV文件: {file_path}")
    # endregion ---------------------------- 参数校验与路径处理 ----------------------------

    # region ---------------------------- 打开文件并读取内容 ----------------------------
    rows_data = [] # 存储最终结果的列表
    try:
        # utf-8-sig 自动跳过 BOM 头，避免 CSV 列名出现 \ufeff 前缀
        with open(file_path, mode="r", encoding="utf-8-sig") as f:
            csv_reader = csv.DictReader(f, delimiter=delimiter) # DictReader 用第一行作为字段名，自动解析为字典
            for row in csv_reader:
                rows_data.append(dict(row)) # 转为普通 dict 拷贝，避免外部修改 reader 内部缓存
    except csv.Error as e:
        raise RuntimeError(f"CSV解析失败: {e}") from e
    except PermissionError as e:
        raise PermissionError(f"没有权限读取文件: {e}") from e
    # endregion ---------------------------- 打开文件并读取内容 ----------------------------

    # region ---------------------------- 返回处理结果 ----------------------------
    return rows_data
    # endregion ---------------------------- 返回处理结果 ----------------------------
```

