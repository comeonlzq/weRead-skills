<div align="center">

# weread-report 📖

**微信读书个人阅读分析报告生成器 — 一个让 AI "读懂你" 的 Agent Skill**

通过微信读书 Agent API Gateway 采集你的真实阅读数据，经过 18 项指标计算与深度洞察分析，最终生成一份 **Apple Vision Pro 玻璃态风格的单文件 HTML 阅读报告**。

[![Version](https://img.shields.io/badge/skill__version-1.0.4-blue?style=for-the-badge)](#)
[![Stages](https://img.shields.io/badge/工作流-4%20阶段-green?style=for-the-badge)](#-工作流程)
[![Metrics](https://img.shields.io/badge/分析指标-18%20项-purple?style=for-the-badge)](#-工作流程)
[![Style](https://img.shields.io/badge/设计-visionOS%20玻璃态-orange?style=for-the-badge)](#-报告长什么样)
[![Dependencies](https://img.shields.io/badge/依赖-零-red?style=for-the-badge)](#-报告长什么样)

*参考微信读书官方开放 Skills 设计 · 设计规范参考 [ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill)*

</div>

---

## ✨ 它能做什么

不只是罗列数据，而是把数据翻译成 **"你的人生切片"**：

- 📊 **18 项核心指标** — 阅读总量、日均对比、作息画像、读听比例、分类偏好、作者忠诚度、书架健康度……
- 🧠 **深度洞察** — 跨书主题归并提炼你最关心的核心问题、人物画像白描、动物人格预测、历年偏好迁移、知识版图优势与盲区
- 💬 **年度之句与阅读人格称号** — 从你自己的划线中挑出最能代表你精神底色的那句话
- 🔒 **隐私优先** — 内置敏感内容过滤，涉及健康、亲密关系、职场财务的笔记静默跳过，不会出现在任何展示位
- 🎨 **单文件 HTML** — 零依赖、不引 CDN、离线可打开，双击即看

<img width="1356" height="1119" alt="151f6fe66e4ca77de3326d668949857a" src="https://github.com/user-attachments/assets/a854aeda-b486-49e7-b6a1-23a04dc0117e" />
<img width="1260" height="39817" alt="30aff1ed6be57788d989ac31c3e925c6" src="https://github.com/user-attachments/assets/b6f6f2e4-a222-4d5b-8d65-429224d39eed" />
<img width="687" height="1050" alt="243a3c18e6bd04ba8316d43f6a0b246c" src="https://github.com/user-attachments/assets/c8b46ef4-f9c1-46e8-b7fb-a17d57053b8b" />


## 🔄 工作流程

Skill 采用 **四阶段流水线**，逐阶段推进、进度可见、禁止编造：

```
┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐
│  阶段一 凭据确认   │ → │  阶段二 数据采集   │ → │  阶段三 分析计算   │ → │  阶段四 报告生成   │
│  01-credential   │   │  02-collect      │   │  03-analyze      │   │  04-report       │
│                  │   │                  │   │                  │   │                  │
│ · API Key 校验    │   │ · 9 步采集清单     │   │ · 18 项指标计算    │   │ · visionOS 玻璃态  │
│ · 报告范围确认     │   │ · 数据口径校准     │   │ · 深度洞察 5 项    │   │ · 单文件零依赖     │
│                  │   │ · 本地缓存 JSON   │   │ · 敏感内容过滤     │   │ · 质量红线自检     │
└──────────────────┘   └──────────────────┘   └──────────────────┘   └──────────────────┘
```

**采集清单（9 步）**：逐年/总计/月度阅读数据 → 书架全貌 → 笔记全景 → 最近在读进度 → 热门划线对比 → 划线原文 → 个人想法全文。所有原始数据缓存到本地 `weread_report_data.json`，调整报告样式时**无需重新请求 API**。

## 📦 安装

### Trae

将本仓库克隆（或下载）到 Trae 的 skills 目录：

```bash
# 全局安装（所有项目可用）
git clone https://github.com/<your-username>/weRead-skills.git ~/.trae-cn/skills/weread-report

# 或项目级安装
git clone https://github.com/<your-username>/weRead-skills.git /path/to/your/project/.trae/skills/weread-report
```

重启 Trae 后，Skill 会在你提出相关请求时**自动激活**。

### 其他支持 Agent Skills 的工具（Claude Code / Cursor 等）

本 Skill 为纯 Markdown 指令集（无脚本依赖），将其中的 `SKILL.md` 与 4 个阶段文件放入对应工具的 skills 目录即可，例如：

```bash
git clone https://github.com/<your-username>/weRead-skills.git ~/.claude/skills/weread-report
```

## 🔑 准备 API Key

1. 获取微信读书 Agent API Key（格式 `wrk-xxxxxxxx`）
2. 设置环境变量，或在使用时直接提供给 AI：

```bash
# Windows (PowerShell)
$env:WEREAD_API_KEY = "wrk-xxxxxxxx"

# macOS / Linux
export WEREAD_API_KEY="wrk-xxxxxxxx"
```

> Skill 启动后会先发送轻量请求校验 Key 有效性，并一次性确认统计范围（本年 / 总计 / 指定年份）与文案风格（客观图表型 / 个性化趣味文案型）。

## 🚀 使用

安装后直接用自然语言对话，Skill 会自动触发：

```
生成我的微信读书阅读报告

分析一下我的微信读书数据

我想看 2025 年度阅读报告
```

执行完成后，会在工作目录生成 `weread_report.html`，直接双击在浏览器打开即可。

## 🎨 报告长什么样


报告采用 **Apple Vision Pro 玻璃态设计语言** — 景深（Depth）+ 玻璃态（Glass）+ 光影（Light），页面自上而下包含 10 个章节：

| # | 章节 | 亮点 |
|---|------|------|
| 1 | **Hero 头部** | 阅读人格称号 + 年度之句引言 + 趣味标签胶囊 + 三个核心大数字 |
| 2 | **历年阅读趋势** | 多年柱状图 + 偏好迁移叙事 |
| 3 | **阅读作息** | 24 小时热力条 + 时段人格标签 + 黄金阅读时段 |
| 4 | **口味画像** | 分类偏好 Top5 条形图 + 读听比例环形图 |
| 5 | **笔记洞察** | 3-5 个核心问题卡片，每条引用均为你的真实划线原文 |
| 6 | **人物画像与动物人格** | 150 字画像白描 + 🦉 动物人格徽章 + 数据依据 |
| 7 | **知识版图** | 优势领域 vs 盲区领域左右对照 |
| 8 | **年度书榜与最爱作者** | 时长 Top10（含真实封面图） |
| 9 | **书架体检** | 读完率 / 囤积指数 / 最近在读进度条 |
| 10 | **结语** | "现象 → 解读 → 照见" 三层结构的个性化总结 |

**技术细节**：

- 深空底色 + 彩色光斑营造空间纵深，玻璃卡片 `backdrop-filter: blur(24px)` 磨砂质感
- 图表全部由纯 CSS / SVG 实现（柱状图、热力条、环形图），**零代码依赖**
- 书籍封面直接引用接口返回的 URL，带 `onerror` 占位降级，永不破图
- 文字对比度 ≥ 4.5:1，适配 `prefers-reduced-motion`，响应式覆盖 375 / 768 / 1024 / 1440px

## 🔒 隐私与数据安全

- **本地运行**：所有数据采集、分析、报告生成均在本地对话中完成，报告为本地 HTML 文件
- **敏感内容过滤**：涉及健康与心理困境、亲密关系与家庭、职业与财务、强烈负面宣泄的笔记，一律不进入报告展示位（静默过滤，仅参与统计）
- **禁止编造**：所有数字、引文、推荐书目必须来自接口返回，笔记引用逐字保留、不得改写
- **推测需标注**：人物画像与动物人格明确标注"娱乐性推测"，每条论断附数据依据

## 📁 项目结构

```
weRead-skills/
├── SKILL.md            # Skill 入口：总体流程与执行纪律
├── 01-credential.md    # 阶段一：API Key 校验与报告范围确认
├── 02-collect.md       # 阶段二：接口调用规范、9 步采集清单、数据口径
├── 03-analyze.md       # 阶段三：18 项指标计算 + 深度洞察 + 写作规范
├── 04-report.md        # 阶段四：visionOS 玻璃态设计规范与质量红线
└── README.md
```

## 🙏 致谢

- 设计思路参考了 **微信读书官方开放的 Agent Skills**（接口与 `skill_version` 协议）
- 视觉与文档结构参考了 [ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) 的设计理念与 README 组织方式

## ⚠️ 免责声明

本项目为个人学习与效率工具，与微信读书官方无关。接口能力依赖官方开放的 Agent API Gateway，请合理使用；数据仅供个人分析展示，请勿用于商业用途。

## 📄 License

[MIT](LICENSE) © 2026
