---
name: python-code-format
description: 严格遵循指定格式生成 Python 代码，包含文件头、导入排序、函数与类文档字符串、区块注释等规范。
---

# Python 代码格式规范技能

## 目标
在生成任何 Python 代码时，必须严格遵守以下格式要求，以确保代码可读性、可维护性和一致性。

核心原则：**任何人看到这行代码时，不需要经过思考就能立刻明白它在干什么**。注释的本质是替读者省去"推理代码意图"的时间，而不是复述语法。

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
# 这里是 import 区域
```

所有用 `{}` 包裹的占位符，必须根据上下文替换成合适的内容。

例子：`{姓名}` 替换成 `john`，`{日期}` 替换为当前日期。

### 模块命名规则

- **工具类（utils）模块**：统一使用**大驼峰命名法（PascalCase）**。例如：`DateTimeUtils.py`、`FilePathUtils.py`、`StringFormatter.py`。
- **其他功能模块**：统一使用**蛇形命名法（snake_case）**。例如：`business_model.py`、`api_connector.py`、`text_cleaner.py`、`config_reader.py`。

这一规则同时适用于模块文件名和导入时的别名（如有必要），保持项目内命名风格一致。

### 第三方库导入

- **所有导入语句按项目功能模块分组**，组名根据模块的实际功能自定义，例如 `common`、`database`、`string-formatter`、`api-connector`、`business`、`tools` 等。
- 组间用注释空行分隔。每组之间间隔一行
- 分组名称和数量完全取决于当前项目的模块划分，不应机械套用固定名称

- 每组内的导入按长度排序（或字母序），尽可能使用 `from ... import` ，并且在有多个`from ... import`对象时换行并对齐
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

### 函数文档字符串

- 每个函数必须包含详细的 docstring，说明功能、参数、返回值和示例。
- 示例部分必须使用 `Example:` 标题，并给出可运行的代码和预期输出。
- **例外规则**：
  - `@property` 装饰的方法：跳过 `Args:` 部分（属性不接受参数）
  - `__init__` 方法：跳过 `Returns:` 部分（构造方法隐式返回 None）
  - `main()` 入口函数：`Returns:` 和 `Example:` 可选，视复杂度而定

格式为：

```python
def func_name(arg: str) -> int:
    """这个函数的作用是什么

    Args:
        arg (str): 这个参数的作用是什么

    Returns:
        int: 返回什么

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

    def __init__(self):
        """初始化 ClassName 实例

        Args:
            arg1 (str): 参数1的描述
        """
        self.init_class_arg_name = ""  # 初始化变量注释，描述它的作用

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

### 区块注释

- 当代码超过 8-10 行时，使用 `region` 风格的区块注释，标明代码作用。
- 注释必须成对出现：`# region ----- 描述 -----` 和 `# endregion ----- 描述 -----`

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
    
    Example:
        >>> data = read_csv("example.csv")
        >>> print(data)
        [
            {'姓名': '张三', '年龄': '25', '城市': '北京'},
            {'姓名': '李四', '年龄': '30', '城市': '上海'}
        ]
    """
    # region ---------------------------- 参数校验与路径处理 ----------------------------
    # 检查文件路径是否为空或非法
    if not file_path:
        raise ValueError("文件路径不能为空")
    file_path = os.path.normpath(file_path) # 规范化路径
    # 校验文件是否真实存在
    if not os.path.isfile(file_path):
        raise FileNotFoundError(f"找不到指定的CSV文件: {file_path}")
    # endregion ---------------------------- 参数校验与路径处理 ----------------------------

    # region ---------------------------- 打开文件并读取内容 ----------------------------
    rows_data = [] # 存储最终结果的列表
    try:
        # 使用with语句自动管理文件资源，指定编码为utf-8-sig以处理BOM头
        with open(file_path, mode="r", encoding="utf-8-sig") as f:
            csv_reader = csv.DictReader(f, delimiter=delimiter) # DictReader直接返回字典形式
            for row in csv_reader:
                rows_data.append(dict(row)) # 转换为普通dict，避免后续引用问题
    except csv.Error as e:
        raise RuntimeError(f"CSV解析失败: {e}") from e
    except PermissionError as e:
        raise PermissionError(f"没有权限读取文件: {e}") from e
    # endregion ---------------------------- 打开文件并读取内容 ----------------------------

    # region ---------------------------- 返回处理结果 ----------------------------
    return rows_data
    # endregion ---------------------------- 返回处理结果 ----------------------------
```

