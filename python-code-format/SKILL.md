---
name: python-code-format
description: 严格遵循指定格式生成 Python 代码，包含文件头、导入排序、函数与类文档字符串、区块注释等规范。
---

# Python 代码格式规范技能

## 目标
在生成任何 Python 代码时，必须严格遵守以下格式要求，以确保代码可读性、可维护性和一致性。

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
from utils import DateTimeutils
from utils import FilePathutils
from utils import unicode_normalizer
```

### 函数文档字符串

- 每个函数必须包含详细的 docstring，说明功能、参数、返回值和示例。
- 示例部分必须使用 `Example:` 标题，并给出可运行的代码和预期输出。

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

### 复杂逻辑步骤注释

- 对于逻辑复杂、不能一眼看懂的代码，使用“步骤注释”解释每一步。

格式：

```python
# 步骤1: 简要描述其行为
# 步骤2: 简要描述其行为
# 步骤3: 简要描述其行为
func_name(arg=1)  # 步骤1: 补充描述
# 步骤2: 补充描述
func_name(arg=2)
# 步骤3: 补充描述
func_name(arg=3)
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

## 执行要求

- 生成任何代码时，必须**完整应用**上述格式。
- 注释要通俗易懂，数量要充分。
- 保持代码整洁，避免无意义的空行，但必要的分组空行必须保留。
- 对复杂度高的代码，应尽量使用步骤注释或区域注释，降低理解成本。

## 使用示例

当用户请求“写一个读取CSV文件的函数”时，应输出包含完整文件头、导入、docstring、示例和必要区块注释的格式化代码。

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
    # region ---------------------------- 1. 参数校验与路径处理 ----------------------------
    # 步骤1: 检查文件路径是否为空或非法
    # 步骤2: 规范化路径，确保跨平台兼容
    # 步骤3: 检查文件是否存在
    if not file_path:
        raise ValueError("文件路径不能为空")
    file_path = os.path.normpath(file_path)                       # 步骤2: 规范化路径
    # 步骤3: 校验文件是否真实存在
    if not os.path.isfile(file_path):
        raise FileNotFoundError(f"找不到指定的CSV文件: {file_path}")
    # endregion ---------------------------- 1. 参数校验与路径处理 ----------------------------

    # region ---------------------------- 2. 打开文件并读取内容 ----------------------------
    rows_data = []                                                # 存储最终结果的列表
    try:
        # 使用with语句自动管理文件资源，指定编码为utf-8-sig以处理BOM头
        with open(file_path, mode="r", encoding="utf-8-sig") as f:
            csv_reader = csv.DictReader(f, delimiter=delimiter)   # DictReader直接返回字典形式
            for row in csv_reader:
                rows_data.append(dict(row))                       # 转换为普通dict，避免后续引用问题
    except csv.Error as e:
        raise RuntimeError(f"CSV解析失败: {e}") from e
    except PermissionError as e:
        raise PermissionError(f"没有权限读取文件: {e}") from e
    # endregion ---------------------------- 2. 打开文件并读取内容 ----------------------------

    # region ---------------------------- 3. 返回处理结果 ----------------------------
    return rows_data
    # endregion ---------------------------- 3. 返回处理结果 ----------------------------
```

