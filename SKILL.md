---
name: weread-report
description: "Generate a personal WeRead reading analysis report as a beautiful single-file HTML page (visionOS glass style). Invoke when the user asks for a WeRead/reading report, reading data analysis, or a personal reading profile (e.g. 'generate my reading report', 'analyze my WeRead data')."
---

# WeRead 阅读分析报告生成器

通过微信读书 Agent API Gateway 采集用户的阅读数据，经过计算分析，最终生成一个**美观、简洁、内容丰富、有价值的单文件 HTML 阅读分析报告**（Apple Vision Pro 玻璃态风格）。

## 总体流程（必须按顺序执行）

```
阶段一：凭据与范围确认 → 阶段二：数据采集 → 阶段三：分析计算 → 阶段四：HTML 报告生成
```

## 分阶段执行文件（进入某阶段前，先完整阅读对应文件）

| 阶段   | 文件                                   | 职责                                                                                                                           |
| ------ | -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| 阶段一 | [01-credential.md](./01-credential.md) | 获取并校验 API Key、确认报告范围                                                                                               |
| 阶段二 | [02-collect.md](./02-collect.md)       | 接口调用规范、9 步采集清单、数据口径、原始数据缓存                                                                             |
| 阶段三 | [03-analyze.md](./03-analyze.md)       | 18 项指标计算 + 深度洞察（笔记洞察/动物人格/历年迁移/知识版图/年度之句与人格称号）+ 洞察写作规范（让人感到"被看见"）+ 趣味标签 |
| 阶段四 | [04-report.md](./04-report.md)         | visionOS 玻璃态设计规范、页面结构、输出要求、质量红线                                                                          |

## 执行纪律

1. **逐阶段推进**：每个阶段只读并只执行对应文件中的内容，完成后再进入下一阶段，避免任务发散。
2. **进度可见**：每个阶段完成后用一句话向用户汇报进展（如"已获取书架数据，共 42 本书"）。
3. **禁止编造**：所有数字、引文、推荐书目必须来自接口返回；缺失数据时跳过并标注"暂无数据"。
4. **口径优先**：解释回包字段时以 `02-collect.md` 的数据口径表为准，禁止凭字段名猜测。
