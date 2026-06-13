# 奥卡姆剃刀原则 — 实现计划

> **面向 AI 代理的工作者：** 必需子技能：使用 superpowers:subagent-driven-development（推荐）或 superpowers:executing-plans 逐任务实现此计划。步骤使用复选框（`- [ ]`）语法来跟踪进度。

**目标：** 在 `python-code-format/SKILL.md` 中新增"奥卡姆剃刀原则"独立章节

**架构：** 在"函数长度与复杂度参考"和"代码质量检查规则"之间插入新章节，包含原则声明 + 四个维度的对比示例（数据结构、算法、抽象层级、代码组织）

**技术栈：** Markdown

---

### 任务 1：在 SKILL.md 中插入奥卡姆剃刀原则章节

**文件：**
- 修改：`python-code-format/SKILL.md`

- [ ] **步骤 1：在 "函数长度与复杂度参考" 和 "代码质量检查规则" 之间插入新章节**

定位到 `### 代码质量检查规则` 之前，在 `### 函数长度与复杂度参考` 整节之后（两点之间的空行位置），插入以下内容：

```markdown
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
```

插入位置：在 `### 函数长度与复杂度参考` 整节结束后、`### 代码质量检查规则` 开始前，两个 `###` 标题之间留两个空行作为章节间距。

- [ ] **步骤 2：验证文件结构完整**

确认新章节前后与相邻章节间各有两个空行分隔（遵循文档内 `###` 标题间距习惯）。

- [ ] **步骤 3：Commit**

```bash
git add python-code-format/SKILL.md
git commit -m "docs: 新增奥卡姆剃刀原则章节"
```
