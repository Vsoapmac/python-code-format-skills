---
name: python-code-format
description: 当你在生成超过15行代码、编写python函数/类/模块、导入模块、生成.py文件或生产任意python代码，或用户说"注释"/"规范"而编辑的代码为python代码时使用
---

# Python 代码格式规范技能

生成任何 Python 代码时必须严格遵守以下格式。核心原则：**任何人看到时不需要经过思考就能立刻明白它在干什么**。代码的最大成本不是写出来，而是后续反复阅读、理解和修改；注释的本质是替读者省去"推理代码意图"的时间，而非复述语法。能用朴素方案解决的，不要引入不必要的复杂度。

## 模块文件头部

文件开头必须有如下头部，`{}` 占位符根据上下文替换（如 `{日期}` → 当前日期）：

```python
# -*- coding: utf-8 -*-
# @Time    : {日期，格式YYYY-mm-DD}
# @Author  : {作者名称}
# @File    : {文件名(包含后缀)}
# @Software: {IDE 名称，如 VSCode, PyCharm}
# @Description: {该代码的作用是什么}
```

在编写作者名称时, 询问用户作者名称是什么, 并给出选择:

- AI 大模型名称
- 用户自定义名称

然后根据用户选择的名称, 替换 `{作者名称}` 占位符。

## 空行规则

- 文件头部与后续代码之间：1 行；导入区域结束与后续代码之间：2 行
- 顶级函数/类之间：2 行；类内方法之间：1 行（PEP 8）
- 函数内部逻辑块之间可用 1 个空行分隔，不宜滥用

## 命名规则

- **utils 工具类模块**：大驼峰，如 `DateTimeUtils.py`；**其他功能模块**：蛇形，如 `business_model.py`
- **类名** PascalCase；**函数/方法/变量** snake_case
- **布尔变量**：`is_`/`has_`/`can_`/`should_` 前缀（✅ `is_active` ❌ `active`）
- **私有成员**：单下划线 `_name`；双下划线仅在需要避免子类属性冲突时使用
- **集合变量**：使用复数（✅ `users` ❌ `user_list`，类型后缀冗余，除非同一作用域需区分多种集合）
- **常量**：UPPER_SNAKE_CASE，集中定义在导入之后、函数/类之前；禁止函数内散布魔法数字，超过一次使用的字面量必须提取为常量；常量超过 10 个时抽取到独立的 `constants.py`/`config.py`

```python
# ------------ 常量定义 ------------
DEFAULT_TIMEOUT_SECONDS = 30 # 第三方 API 文档建议超时不超过 30 秒
```

## 枚举

一组固定值反复出现时，必须用 `enum.Enum` 替代字符串/整数常量。类名 PascalCase、成员 UPPER_SNAKE_CASE，不关心具体数值时用 `auto()`，成员需行内注释：

```python
class TaskStatus(Enum):
    """任务状态枚举"""
    PENDING = auto() # 等待执行
    RUNNING = auto() # 正在执行
```

## 导入规则

- **所有导入按项目功能模块分组**，组名依项目实际功能自定义（如 `common`、`database`、`api-connector`、`business`、`tools`），不机械套用固定名称
- 组注释格式：`# ------------ 组名 ------------`，组间间隔一行
- **组内按视觉长度排列**：整行字符数越短越靠上（因此 `import xxx` 通常在 `from ... import xxx` 之前）；换行的多行导入块视为最长，放组内最后
- **该排序仅在组内生效**：不同分组之间互不比较、互不影响
- 尽可能使用 `from ... import`；导入对象多时换行并对齐
- 导入区域结束与后续代码之间空一行

导入实例: 

```python
# ------------ common ------------
import os
import sys
from datetime import datetime
from typing import (
    Any,
    Dict,
    Optional
)

# ------------ tools ------------
from utils import DateTimeUtils
from utils import unicode_normalizer
```

## 类型注解

- 公共函数/方法的参数和返回值**必须**注解；私有函数建议注解
- 类属性/实例属性建议用注解语法声明类型（而非仅在 docstring 中描述）
- 复杂类型用 `typing` 的 `Optional`/`Union`/`Any`；循环引用用 `from __future__ import annotations` 或字符串前向引用

## 函数 docstring

每个函数必须有 docstring，按顺序：一行摘要 → 详细描述(可选) → `Args:` → `Returns:` → `Raises:` → `Example:`

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
        >>> func_name("test")
        1
    """
```

- `Raises:` 在函数内显式 `raise` 时必须写；仅内置函数/库默认抛出的异常可省略
- `Example:` 必须给出可运行代码和预期输出
- 例外：`@property` 跳过 `Args:`/`Raises:`；`__init__` 跳过 `Returns:`；`main()` 的 `Returns:`/`Raises:`/`Example:` 可选

## 类定义

- 类变量和实例变量需注释说明作用
- 类方法和普通函数一样需要完整 docstring

## 错误处理

- 优先使用内置异常（`ValueError`、`TypeError` 等）；仅在需按异常类型分流业务逻辑时才自定义异常类
- 捕获后重新抛出必须 `raise ... from e` 保留异常链
- 仅捕获预期的具体异常类型，**禁止**裸 `except:` 或宽泛 `except Exception:`（最外层入口兜底除外）
- 文件/网络/数据库等资源**必须**用 `with` 管理，不用 try/finally 手动关闭；自定义资源类实现 `__enter__`/`__exit__`，简单场景可用 `contextlib.contextmanager`

## 字符串规范

- **统一双引号**（含字典键、f-string）；仅内容本身含双引号时可用单引号包裹
- **优先 f-string**；同一模板多处复用时用 `str.format()`；**禁止 `%` 格式化**
- f-string 内不写复杂表达式，先赋值给变量再引用

## 区块注释

代码超过 8-10 行时使用成对 region 注释，横线统一 28 个 `-`：

```python
# region ---------------------------- 这一块代码的作用 ----------------------------
...
# endregion ---------------------------- 这一块代码的作用 ----------------------------
```

## 重要/警示注释

对需谨慎修改、影响重大或不得已而为之的代码，用成对 `===` 分隔线包裹，单行文本不宜过长、注意换行：

```python
# ==========================================================================================================
# 描述如下步骤的做法，一行文本太多时注意换行
# ==========================================================================================================
func_name(arg=3)
# ==========================================================================================================
```

## 注释标点

注释中统一使用**英文半角标点**（`()` `,` `:` `;` `.` `!` `?`），禁止中文全角标点；标点后必须加空格，英文括号内外侧与文字之间加空格。

- ✅ `# 处理数据 (支持多种格式) 并返回结果`
- ❌ `# 处理数据（支持多种格式）并返回结果`

## 行内注释

- 格式：`代码 # 注释文本`（`#` 前后各一个空格）；**禁止**用大量空格把多行行内注释对齐
- 不再使用的代码直接删除，不以注释形式保留

**必须注释**的四类场景（说明"为什么"而非"是什么"）：

- 非显而易见的字面量（魔法数字、特殊字符串、正则）：✅ `timeout = 30 # 第三方API文档建议超时不超过30秒` ❌ `# 设置超时为30秒`
- 有副作用/依赖外部状态的操作（文件 I/O、网络、数据库、全局变量修改）
- 算法核心步骤 / 非直觉写法：✅ `idx = max(0, min(idx, len(arr) - 1)) # 钳制索引在合法范围，防止越界`
- 易歧义的类型转换或运算（隐式转换、位运算、精度敏感浮点）

**不应注释**：自解释命名（❌ `user_name = "Alice" # 设置用户名`）、纯组装/透传调用、标准 getter/setter/property。

**密度**：每 3-8 行代码至少一条，不要每行都加；判断标准是不熟悉该模块的人能否 3 秒内把握每块意图；注释分散在关键节点而非堆在一处。

## `__all__` 与入口规范

- 被 `from xxx import *` 使用的模块必须定义 `__all__`（字母序，仅列公共符号）；无此场景可省略
- 可直接执行的文件末尾必须有 `if __name__ == "__main__":`，块内只调用 `main()`，入口逻辑封装在 `main()` 中
- 命令行参数用 `argparse` 在 `main()` 内解析，不暴露裸 `sys.argv`

## 文件编码

- 保留 `# -*- coding: utf-8 -*-` 声明（向后兼容与编辑器识别）
- 读写外部文件**显式指定 `encoding="utf-8"`**，不依赖系统默认编码
- 处理 Excel 导出的 CSV 用 `utf-8-sig` 去除 BOM；兼容 GBK 等遗留编码时须加注释说明原因

## 函数长度与复杂度

- 单函数建议 ≤50 行（不含 docstring/空行），超出考虑拆分为职责单一的子函数
- 圈复杂度建议 ≤10；`if/for/while` 嵌套超过 3 层应重构
- 非硬性规则：拆分会破坏逻辑内聚性时可超出，但需区域注释解释原因

## 奥卡姆剃刀原则

优先选择最朴素、最直觉的方案，除非有明确的性能数据或正确性证据。复杂度应"被证明需要"后才引入，而非"万一将来需要"提前铺设。判断"更简单"的维度：代码行数更少、抽象更少、依赖更少、执行路径更短。

- **数据结构**：内置类型能解决就不引入第三方库（❌ 为取 CSV 一列最大值引入 pandas；✅ 标准库 `csv` + 生成器表达式）
- **实现路径**：选逻辑最直白、一眼能看懂的（❌ `reduce(add, filter(lambda x: x > 0, nums), 0)`；✅ `sum(x for x in nums if x > 0)`）
- **抽象层级**：只有一个实现/一个调用者的接口、基类、策略模式都是不必要的噪音，等第二个使用场景真实出现再引入
- **函数拆分**：子函数只被一处调用且逻辑 ≤3 行时不要拆；拆分前问两个问题——有独立的名称含义吗？会被复用吗？都是"否"就留在原地

## 代码质量检查

- 工具组合：**Ruff**（linter + formatter，替代 flake8/isort/black）+ **Mypy/Pyright**（类型检查）；提交前确保无新增错误
- 推荐 `pyproject.toml` 配置：ruff `line-length = 120`、`select = ["E", "F", "I", "N", "W", "UP"]`、`quote-style = "double"`；mypy `strict = true`

## 执行要求与综合示例

生成任何代码必须**完整应用**上述格式；注释通俗易懂、数量充分；避免无意义空行但保留分组空行；复杂代码用区域注释降低理解成本。综合效果示意（模块命名为 `CSVUtils.py`）：

```python
# -*- coding: utf-8 -*-
# @Time    : 2026-04-26
# @Author  : AI Assistant
# @File    : CSVUtils.py
# @Software: VSCode
# @Description: 提供CSV文件读取功能的工具模块，返回字典列表

# ------------ common ------------
import os
import csv
from typing import (
    Dict,
    List
)


def read_csv(file_path: str, delimiter: str = ",") -> List[Dict[str, str]]:
    """读取CSV文件并返回包含字典的列表，每行数据转换为一个字典

    Args:
        file_path (str): CSV文件的完整路径
        delimiter (str): 文件使用的分隔符，默认为逗号

    Returns:
        List[Dict[str, str]]: 每行数据组成的字典列表，键为列名

    Raises:
        FileNotFoundError: 当指定路径的文件不存在时抛出
        RuntimeError: 当 CSV 解析失败时抛出

    Example:
        >>> read_csv("example.csv")
        [{'姓名': '张三', '年龄': '25'}]
    """
    # region ---------------------------- 参数校验与路径处理 ----------------------------
    file_path = os.path.normpath(file_path) # 消除路径中的 '.' 和 '..'，避免 isfile 误判
    if not os.path.isfile(file_path): # isfile 而非 exists，排除目录被误传入
        raise FileNotFoundError(f"找不到指定的CSV文件: {file_path}")
    # endregion ---------------------------- 参数校验与路径处理 ----------------------------

    # region ---------------------------- 打开文件并读取内容 ----------------------------
    rows_data = [] # 存储最终结果的列表
    try:
        # utf-8-sig 自动跳过 BOM 头，避免列名出现 \ufeff 前缀
        with open(file_path, mode="r", encoding="utf-8-sig") as f:
            for row in csv.DictReader(f, delimiter=delimiter):
                rows_data.append(dict(row)) # 转普通 dict 拷贝，避免外部修改 reader 缓存
    except csv.Error as e:
        raise RuntimeError(f"CSV解析失败: {e}") from e
    # endregion ---------------------------- 打开文件并读取内容 ----------------------------
    return rows_data
```
