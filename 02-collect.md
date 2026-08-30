# 阶段二：数据采集

## 接口调用规范

```
POST https://i.weread.qq.com/api/agent/gateway
Headers:
  Authorization: Bearer <WEREAD_API_KEY>
  Content-Type: application/json
Body: JSON，api_name + 业务参数平铺在顶层 + skill_version
```

- **每次请求必须带 `"skill_version": "1.0.4"`**。
- 业务参数必须与 `api_name` 同层平铺，**禁止**包在 `params`/`data` 对象里。
- 回包 `errcode` 非 0 时给出中文提示并停止采集；若出现 `upgrade_info` 字段，按其 message 指引处理后重试。
- 所有 Unix 时间戳记录时保留原始值，展示阶段再转 `YYYY-MM-DD`。
- 发送 `{"api_name": "/_list"}` 可查看所有接口定义。

## 采集清单（按顺序执行）

| #   | 接口                  | 参数                                                                                                                   | 用途                                              |
| --- | --------------------- | ---------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------- |
| 1   | `/readdata/detail`    | `mode=annually`：**报告目标年 + 近 3-5 年逐年调用**（每年 `baseTime` 取该年内任一时间戳），用于核心统计 + 历年趋势对比 | 每年：总时长/天数/偏好分类/时段/作者              |
| 2   | `/readdata/detail`    | `mode=overall`                                                                                                         | 全部历史：注册时间、总时长、总笔记、勋章          |
| 3   | `/readdata/detail`    | `mode=monthly`, `baseTime`=本月内                                                                                      | 本月数据 + `compare` 环比                         |
| 4   | `/shelf/sync`         | 无                                                                                                                     | 书架全貌：电子书/有声书/读完/私密/书单            |
| 5   | `/user/notebooks`     | `count=100`，`hasMore=1` 时用最后一条 `sort` 作 `lastSort` 翻页直到拉完                                                | 笔记全景                                          |
| 6   | `/book/getprogress`   | 从书架 `books[]` 按 `readUpdateTime` 降序取前 5 本，逐本调用                                                           | 最近在读进度                                      |
| 7   | `/book/bestbookmarks` | 选 1-3 本最有笔记的书，`bookId` + `chapterUid=0`                                                                       | 全书热门划线（用于共鸣度对比）                    |
| 8   | `/book/bookmarklist`  | 笔记数最多的 3-5 本书，逐本传 `bookId`                                                                                 | 划线原文（笔记洞察素材）                          |
| 9   | `/review/list/mine`   | 同上几本书，传 `bookid`；`hasMore=1` 时按 `synckey` 翻页拉完                                                           | **用户自己的想法/点评全文（笔记洞察的核心素材）** |

> 范围为"指定年份"时，第 3 项改为该年 12 个月逐月调用（用于月度趋势图）。

## 笔记取样策略（重要）

书目的划线/想法可能多达数百条，**禁止只取前几条**，否则洞察会偏向书的开头：

1. **先拉全量**：按接口翻页规则拉完该书所有笔记，不要中途截断。
2. **再分散取样**：按章节或时间**等距抽样** 20-40 条/本（全书开头、中部、结尾都要覆盖），热门章节可适当多取，但长尾章节必须留有代表。
3. **保留出处字段**：每条样本需保留 `chapterUid`、`createTime`、书名，供报告标注引用出处与时间。
4. 划线（`bookmarklist`）与想法（`review/list/mine`）分别取样，二者数量都多时按比例分配名额。

## 数据口径（必须遵守，禁止凭字段名猜测）

| 规则                                                 | 说明                                                                                         |
| ---------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| 时长单位全部是**秒**                                 | `totalReadTime`、`dayAverageReadTime`、`readTimes` 值、`preferTime` 值等均为秒，禁止误当分钟 |
| 书架总数                                             | `books.length + albums.length + (mp 非空 ? 1 : 0)`，专辑/有声书必须计入                      |
| 笔记总数 = `reviewCount + noteCount + bookmarkCount` | `noteCount` 只是划线数，不是单书总笔记数                                                     |
| 阅读进度 `progress` 是 0-100 整数                    | `1` = 1% 不是 100%；只有 `progress=100` 才算读完                                             |
| `preferTime` 数组顺序                                | 从 **6 点**开始到次日 5 点，共 24 项，`preferTime[i]` 对应小时 `(6+i)%24`                    |
| `readDays`                                           | 单日阅读满 1 分钟才计为有效阅读天                                                            |
| `dayAverageReadTime`                                 | 分母是自然日，不是阅读天；"阅读日均"需自算 `totalReadTime / readDays`                        |
| `readStat[].counts`                                  | 是文案字符串（如"12本"），解析数字时注意提取                                                 |
| 年度模式 `readTimes`                                 | 按月分桶，key 为月起始时间戳                                                                 |
| 书籍封面 `cover`                                     | 接口返回的 `cover` 是封面图片 URL（书架 `books[]`、notebooks 等均含），**必须原样保留**到缓存，供报告直接引用 |

## 原始数据缓存

所有采集结果合并保存到工作目录 `weread_report_data.json`（一个 JSON，按接口名分 key）。这样后续调整报告样式时**直接读缓存重新生成，不必重新请求 API**。文件存在且未过期（同一天）时先询问用户是否复用。

## 错误处理

- API Key 无效 / 过期：提示"API Key 校验失败，请检查是否正确或重新生成"。
- 单个接口失败：跳过该模块继续采集，报告对应章节显示"数据获取失败"，最后汇总告知用户哪些模块缺失。
- 数据为空（如新书架）：正常进入下一阶段，对应模块在报告中显示引导文案。

## 完成标志

- ✅ 采集清单 9 项全部执行完毕（或明确记录缺失项）
- ✅ `weread_report_data.json` 已保存

完成后向用户汇报一句话，然后进入 [03-analyze.md](./03-analyze.md)。
