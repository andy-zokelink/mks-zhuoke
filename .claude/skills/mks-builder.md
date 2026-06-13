---
name: mks-builder
description: "最小知识集（MKS）构建器 — 涵盖全部场景：单个MKS提取、大规模批处理流水线、GitHub Pages 发布、飞书交付。基于刘雪峰最小知识集理论。"
version: 2.43.0
metadata:
  hermes:
    tags: [mks, 最小知识集, 学习, 知识管理, 框架, html, 批处理, 创意项目, 播客, 音频]
    changelog: |
      v2.40.0 (2026-06-15): 🔴 叙事驱动创作铁律——新增优先于所有场景的叙事创作原则。MKS不是文档目录是叙事艺术品：故事性串联/内容决定形式/寓意扎根内容/读→思→问→用叙事弧线/宁缺毋滥/创意图讲故事。含CC叙事创作Brief模板和反模式清单。卓老板聊科技v3三次迭代验证：v1=无框架全自主→文章dump被驳回；v2=MKS模板填充→能用但平庸；v3=skill框架+叙事目标→创作有灵魂。
      v2.39.0 (2026-06-15): 🔴 全自主模式重大修订——新增"放权陷阱"警告。给CC全自主必须同步提供skill+框架约束，否则产出=文章dump。卓老板聊科技v1失败案例全程记录。修复full-autonomy-mode.md的prompt模板加入MKS铁律。Hub Wing C最佳实践沉淀（nav dropdown > body section）。
---

# mks-builder — 最小知识集构建器（全场景统一版）

基于刘雪峰「最小知识集」理论：一个领域 80% 的价值集中在 20% 的核心概念中。

本 skill 是 MKS 相关所有操作的唯一入口。覆盖六大场景 + 元文档体系：
- 场景 A：单个领域 MKS 提取
- 场景 B：大规模笔记库批量 MKS（流水线）
- 场景 D：从旧版 HTML 重生成（技能升级后）
- 场景 E：跨课程合并 MKS（多课程→单专题）
- 场景 F：Meta-MKS / 跨课思想体系重组（多课程→统一框架）
- 场景 G：播客制作（MKS 项目交付后→Coze TTS 音频）

**📚 元文档体系**（线上可访问）：
- 创意模式库：[creative-patterns.html](https://andy-zokelink.github.io/mks-knowledge/references/creative-patterns.html) — 8章可复用创作方法论
- 版本历史库：[versions/](https://andy-zokelink.github.io/mks-knowledge/references/versions/) — v1.0/v2.0/v2.20/v2.36 四个主版本独立页
- 演化全史：[evolution.html](https://andy-zokelink.github.io/mks-knowledge/references/versions/evolution.html) — 时间线+教训+哲学演变
- 以上均在 Hub 总纲页「工具与方法论」section 可直达

---

## 🔴 品控铁律：规则 + 复核 = 质量（最优先）

**规则定义 ≠ 品控。规则只是靶子，复核才是弓箭。有靶子没弓箭，靶子再清楚也打不中。**

问题清单 + 自检步骤 + 多级复核，三者缺一不可：

```
Claude Code 修完 → 自检（按问题清单逐条确认，修完即验）
  ↓ 自检通过
Hermes 复核（浏览器渲染 + validator + grep，不只 grep）
  ↓ 复核通过
交付用户
```

- **执行者自检是底线**：Claude Code 没有内置 post-fix verification——它修完就报「搞定」，必须通过问题清单末尾的「自检步骤」+「验收闸门」强制它逐条验证。
- **复核者不能只 grep**：grep 验「有没有」，浏览器渲染验「对不对」。JS 生成的 SVG 在源码里是模板字符串，grep 搜不到 `<path>` 元素却能在浏览器正常渲染；反过来空 `<svg></svg>` 和空 `<div id="svgMap">` 都有标签却无内容。**必须逐文件打开浏览器看一眼**。
- **多轮迭代后仍漏到用户手里的问题，不是规则不清，是复核失效**——规则早已定义了缺陷点，但执行者和复核者都没在交付前实际打开页面看过。

## 🔴 Hermes 角色边界（最优先）

**Hermes（阿森）= 军师，不是匠人。只分析、判断、发派、复核——不亲自改代码。**

| 任务类型 | 谁做 | 怎么做 |
|---------|------|--------|
| MKS HTML **生成** | Claude Code | `terminal(background=true, notify_on_complete=true)` + prompt 中含全部规范 |
| MKS 文件 **调试/修复** | Claude Code | 同上。给问题描述 + 目标 + 金标准文件路径，让它自己 diff 对比、批量修复、跑 validator、push |
| MKS 结构 **分析/分类** | Hermes | 读笔记、聚类主题、输出 topics.json |
| MKS 结果 **复核** | Hermes | Claude Code 交付后逐文件跑 validator + grep 弯引号/ID 对齐，确认无误再通知用户 |

**用户明确要求**：「这种编码的问题还是给 Claude Code 改，这是他的强项。你把问题点交给他，告诉他修改的目标，让他改完验证一遍，交付给你后你再复核一次，没问题再跟我说。」

**Hermes 手动 patch MKS 文件的后果**（本 session 实况）：
- 改了 10+ 轮，线上始终不生效
- 根因：改的是 `硅谷来信1/`，线上用的是 `硅谷来信1_v2/`——目录都搞错了
- Hermes 的 `search_files` 工具在文件被修改后返回缓存旧结果，反复误诊
- 用户直接吐槽「现在的 agent 效率太低」
- Claude Code 接手后 5 分钟定位根因（`init()` 调用已删除的 `renderCard()`），一次修好

**唯一例外**：单行单点的简单 patch（如改一个字面量、删一行死代码），可用 Hermes `patch` 直接操作。但凡涉及跨文件对比、CSS/JS/HTML 三层对齐、或需要跑 validator 验证——立刻转 Claude Code。

---

## 🔴 叙事驱动创作铁律（2026-06-14 定，优先于所有场景）

**MKS 不是文档目录，是叙事艺术品。**

### 核心原则

1. **故事性串联** — 每个专题必须有一条叙事主线，像一部迷你纪录片。概念不是孤立卡片，是故事中的人物/节点。读者从头看到尾应该有"旅程感"而非"翻阅感"。

2. **内容决定形式** — 排版构图、隐喻选择、视觉配色必须从文章内容本身生长出来，不能套用统一模板。宇宙专题的视觉语言和食品专题的视觉语言应该完全不同——不是因为规定不同，而是因为内容气质不同。

3. **寓意扎根内容** — 每个设计决策（为什么用这个隐喻？为什么这个配色？为什么这个布局？）背后必须有内容理由。不能说"因为好看"，只能说"因为这批文章的核心是X，而X的视觉表达是Y"。

4. **读→思→问→用的叙事弧线** — 每个专题的读者体验必须遵循四段结构：
   - **读**：从真实文章片段引入，给读者第一手接触感
   - **思**：提炼核心概念，展示概念间的深层关系（概念关系图+思维导图）
   - **问**：用苏格拉底追问和案例分析挑战读者的理解
   - **用**：实战决策让读者把概念应用到真实场景

5. **宁缺毋滥** — 每个专题至少4-6种完全不同的内容呈现方式（时间线、剖面图、对比巨幕、闯关游戏、地图标注、分层折叠、决策树、叙事SVG等），让读者有节奏变化。禁止任何形式的卡片罗列。

6. **创意图必须讲故事** — 每个专题至少一张叙事性SVG创意图，不是知识点图解，是能让人停留3秒产生好奇心的艺术品。从内容的核心矛盾/张力出发构图。

### Claude Code 叙事创作 Brief

向 CC 派发创作任务时，必须提供：

```
## 本质洞察
{一句话——这个专题在回答什么根本问题？}

## 情感基调
{读者看完应该感受到什么？敬畏/焦虑解消/恍然大悟/行动冲动}

## 叙事锚点
{核心意象——什么物理/生物/社会场景能承载这组概念的叙事？}

## 禁止
- 禁止卡片罗列
- 禁止统一模板
- 禁止教科书式插图
- 禁止从其他专题复制设计
```

### CC 叙事 prompt 模板

```
你正在为一个专题页面做设计创作。这不是MKS模板填充——这是一次叙事设计。

## 专题本质
{根本问题}

## 情感目标  
读者离开这个页面时，应该带着{情感}——而不是带着{信息列表}。

## 叙事要求
从这个专题的{N}篇文章中，找到一条故事主线。用至少4-6种不同的呈现方式，
创造节奏变化。不要用统一卡片模板——让每种概念用最适合它的方式呈现。

## 创意图要求
基于内容的核心张力创作一张叙事SVG图。不要知识点罗列图，不要教科书式图解。
这张图应该让人停留3秒产生好奇心。

## 质量标杆
每个模块、每个设计选择都必须有内容理由。问自己：为什么这个排版最适合这篇文章？
为什么这个配色最能传达这个情绪？

## 验收
- 4-6种以上不同呈现方式 ✓
- 叙事主线贯穿全页 ✓
- 创意图有故事性非图解 ✓
- 没有卡片罗列 ✓
- 设计决策有内容理由 ✓
```

### 反模式（禁止）

- ❌ 7个专题用同样的布局只换颜色和文字
- ❌ 概念=卡片正面名字背面定义（机械罗列）
- ❌ SVG图=圆圈+箭头+文字标签（教科书图解）
- ❌ "这个排版好看所以用这个"（无内容理由的设计）
- ❌ 专题间无叙事差异（宇宙=深蓝卡片，食品=橙色卡片——本质还是卡片）

### 🔴 创作自主权光谱（2026-06-14 卓老板聊科技三版验证）

同一批 298 篇文章，三次不同力度的指导 → 三种质量层级的产出。这个光谱定义了 CC 创作任务的指导公式：

| 层级 | 指导力度 | CC 产出 | 案例 |
|------|---------|---------|------|
| **放任** | "从零开始，全部由你决定" | 文档罗列，零概念提取 | 卓老板 v1：279KB/页原文搬运 |
| **框架** | "读 skill，按 9 标签做" | 模板克隆，70 概念但 7 专题雷同 | 卓老板 v2：65KB/页统一暖色 |
| **叙事** | "读 skill，但每个专题有自己的情感基调+设计语言+故事主线" | 7 种视觉系统，每 KB 都有内容理由 | 卓老板 v3：28-83KB/页叙事驱动 |
| **对标放权** | "对标{成功页面}品质 + 叙事锚点一句话 + 情感目标一句话 + 工具清单不强制 + 其余放手" | CC 从数据中发现故事，不过度剧本化 | 卓老板 v4 prompt：对标AI页深空风，叙事锚点"仰望星空一层层揭开面纱" |

**铁律**：永远不给"完全自由"——给对方向后放手。CC 的创造力需要框定在内容本身的引力场内才能释放。

**🔴 对标放权关键教训（2026-06-15 卓老板 v4）：**
不要给 CC 写完整的叙事剧本（角色设定、幕结构、情节走线）。这会把 CC 锁死在预设的故事框架里，而不是让它从文章数据中发现故事。正确做法：对标一个成功页面作为品质参照 + 叙事锚点（1-2句） + 情感目标（1句） + 工具清单（不强制） + 放手。科技参考3 的 AI 页（硅基思维的N层跃迁）是经过验证的最佳参照。

**错误 prompt（被 Andy 叫停）**：8个概念的详细角色人设 + 完整五幕剧本 → 太生硬，像给 CC 布置小说作业而不是设计任务
**正确 prompt（Andy 指导方向）**：对标科技参考3 AI页 + "从仰望星空开始" + "渺小又珍贵" + 工具清单 → CC 自己通读45篇文章后找到最适合的叙事结构

**铁律**：永远不给"完全自由"——给对方向后放手。CC 的创造力需要框定在内容本身的引力场内才能释放。

**指导公式**：

```
successful_prompt = 框架(skill) + 情感基调(per-topic) + 禁止清单(anti-patterns) + 验收标准(gates)
```

缺少框架 → 文档罗列。缺少情感基调 → 模板填充。缺少禁止清单 → 创意跑偏。缺少验收 → 零 quality。

详见 `references/creative-autonomy-spectrum.md`。

---

## 🔴 宏大叙事统领铁律（2026-06-14 定，最高优先级）

**割裂的标签页是反叙事的。伟大的主题需要伟大的叙事容器。**

当主题宏大（宇宙、文明、生命演化等），禁止用 9 标签页将它切碎。用一个统领全局的大叙事框架将所有内容编织在一起。

### 核心转型：从「信息结构」到「叙事体验」

| 旧范式（禁止） | 新范式（要求） |
|---|---|
| 概念网络 → 一个 tab | 概念是故事中的人物/角色 |
| 层次演化 → 一个 tab | 层次是故事的章节/幕 |
| 思维导图 → 一个 tab | 思维导图是故事的地图/指南 |
| 边界知识 → 一个表 | 边界是故事的留白/悬念 |
| 题库 → 独立的 tab | 问题是故事中的试炼/挑战 |
| 案例分析 → 独立的 tab | 案例是故事中的真实事件插叙 |
| 实战决策 → 独立的 tab | 决策是故事的最终高潮/抉择 |

### 叙事形式可选

**英雄之旅 (Hero's Journey)**
- 启程：从日常世界进入未知领域（从牛顿力学进入量子世界）
- 试炼：遇到挑战和导师（不确定性原理、波粒二象性作为"守门人"）
- 深渊：面对最大的恐惧（黑洞、奇点）
- 回归：带着宝物返回（新的宇宙认知框架）

**拟人化/具象化 (Personification)**
- 把每个概念变成有性格的角色
- 「不确定性原理」是一位神秘的占卜师——她知道一切概率，但拒绝给出确定答案
- 「黑洞」是一位沉默的巨人——他吞噬一切，却通过霍金辐射低声耳语
- 「时空」是一张被重物压弯的弹力布——不是"力"在拉你，是路在弯

**史诗叙事 (Epic Narrative)**
- 从宇宙诞生（大爆炸）到元素锻造（恒星核合成）到生命出现
- 三幕结构：混沌→秩序→觉醒
- 每个概念是史诗中的一个章节

### 视觉统一原则

- **一个叙事 = 一个完整的视觉世界**
- 配色、构图、排版、SVG 图形全部服务于这个大叙事
- 不能有"看起来像另一个页面的部分"
- 滚动应该是连续的旅程，不是 tab 切换

### 向 CC 派发宏大叙事任务

```
你正在为一个宏大主题创作叙事体验。忘记 MKS 模板——这是一个故事。

## 主题
{宇宙/文明/生命/...}

## 叙事要求
选择一种叙事形式（英雄之旅/拟人化/史诗叙事），将以下所有元素编织进一个完整的故事：
- {N} 个核心概念 → 故事中的角色/关卡/章节
- 概念间关系 → 故事中的冲突/联盟/因果
- SVG 创意图 → 故事的关键场景插图
- 思维导图 → 故事地图
- 边界知识 → 故事中的旁白/注释
- MCQ 题库 → 故事中的试炼/挑战（可选，不是必须）
- 案例分析 → 故事中的真实事件闪回
- 实战决策 → 故事的最终抉择

## 视觉要求
- 一个统领全局的视觉主题（配色/构图/字体全部服务于叙事）
- 滚动即旅程——每个 scroll 位置对应故事的一个节点
- SVG 插图不是装饰，是叙事的关键帧
- 禁止 tab 切换——一切在一个连续的页面中展开

## 禁止
- 禁止 9 标签页结构
- 禁止"概念网络"→"思维导图"→"边界知识"的割裂排列
- 禁止任何看起来像"从其他专题复制过来"的模块
- 禁止卡片罗列

## 验收
- 读者从头滚到尾像看了一部短片 ✓
- 所有概念自然地出现在故事中 ✓
- 视觉设计有统一的世界观 ✓
- 没有 tab 切换 ✓
```

### 质量判断

**不合格**：9 标签页换了颜色和顺序 → 本质还是切碎的内容。
**合格**：读者滚动页面时，不会意识到"现在在看概念关系图，现在在看思维导图"——一切就是一个完整的故事。

### 🔴 反模式：过度剧本化（2026-06-15 教训）

向 CC 派发宏大叙事任务时，最容易犯的错误是写完整的叙事剧本——给每个概念写详细角色人设，规划每一幕的情节走向。这在本质上和"9标签模板填充"是同一个错误：**你替 CC 做了创作，CC 变成执行你指令的脚本，而不是从数据中发现故事的创作者。**

**错误**：写8个概念角色（"不确定性原理是一位蒙面的先知，她知道所有可能的未来..."），规划五幕结构（序幕→量子王国→时空汪洋→星辰熔炉→归来）
**正确**：叙事锚点一句话 + 对标成功页面 + 放手。让 CC 自己通读文章后找到最适合的叙事形式。英雄之旅只是一种可能——拟人化、史诗叙事、时间线探险、层级递进...CC 可能找到更合适的。

**判断你写的 prompt 是否过度剧本化**：如果 prompt 中具体的叙事细节超过 200 字，你就在替 CC 创作了。退回叙事锚点（1-2句）+ 情感目标（1句）。

---

## 🔴 沉浸叙事法 · MKS v4（2026-06-15 定，当前最佳实践）

**经过四版迭代验证的唯一有效创作公式。**

### 四要素 prompt 模板

```
## 任务：为「{专题名}」创建沉浸式交互页面
对标品质：{成功的标杆页面URL}

### 数据
{数据路径}

### 设计要求
1. 抛弃MKS模板——不要9标签页、不要标准化知识卡片
2. 禁止卡片罗列——至少4-6种完全不同的内容呈现方式
3. {专题专属的视觉方向——不是统一规定，是从内容气质推导}
4. 移动端响应式
5. 返回首页链接

### 核心叙事锚点
{1-2句话——这个专题的独特视角是什么？}

### 情感目标
{1句话——读者离开时应该感受到什么？}

### 输出
{文件路径}
```

### 成功案例：宇宙的秩序 v4

**prompt 四要素**：
- **对标**：科技参考3 AI页（硅基思维的N层跃迁）
- **锚点**：从「仰望星空」开始——人类对宇宙的理解是一层层揭开面纱的过程
- **情感**：既渺小（宇宙尺度）又珍贵（我们是星尘），既有已知的震撼又有未知的敬畏
- **放手**：其余全部由 CC 自主决定

**CC 自主产出**（全部来自 prompt 之外的创造）：
- Canvas 粒子星空 Hero
- 波粒二象性互动（波动/粒子/叠加/坍缩四种模式 Canvas 切换）
- 时空弯曲叙事 SVG + 4 信息卡
- 黑洞吸积盘 SVG（多普勒蓝移/红移）+ 翻转卡片
- 恒星元素锻造 5 阶段点击互动
- 科学家群像 6 人物卡
- 苏格拉底折叠追问 4 组 Q&A
- 知识边界网格 6 未解之谜
- 探索进度指示器（IntersectionObserver）
- 9 章连续滚动叙事（仰望→量子深渊→时空弯曲→黑洞→恒星熔炉→人物→追问→边界→渺小又珍贵）

**关键**：prompt 只有 ~200 字，CC 产出了 9 种交互形式 + 完整叙事弧线。不是你设计得好——是你给了对的锚点后，CC 自己从 45 篇文章中发现了这个故事。

### 反例：过度剧本化的 v4-draft

给 CC 写 8 个角色详细人设 + 完整五幕剧本 → CC 在 plan mode 卡死，产出零。因为你在替 CC 创作，CC 变成了执行脚本。

### 铁律

**沉浸叙事法 = 对标标杆 + 叙事锚点(≤2句) + 情感目标(1句) + 放手**

如果 prompt 中叙事细节 > 200 字 → 过度剧本化，退回四要素。

### 版本命名

本方法论产出的页面标记为 **MKS v4**。skill v2.43.0+ 对应 MKS v4 沉浸叙事版。

---

## 场景 A：单个领域 MKS 提取

### 触发条件
- 「帮我搭一下 XXX 的最小知识集」
- 「用 MKS 方法分析这篇文章」
- 提供一篇笔记/文章/课程名，要求结构化提炼

### 步骤 0：增量还是初版？

搜材料前先查是否已有 MKS：

```bash
getnote search "MKS 最小知识集 {领域名}" --kb {KB_ID} --limit 3 -o json
```

- **初版**：从未整理过 → 走步骤 1-3
- **增量**：已有前版 → 先读前版，找未被覆盖的角度。末尾加「与前版的关系」对照表

### 步骤 1：信息搜集
- 给了具体笔记 → `getnote search` 在知识库中搜索
- 给了课程名/领域名 → 结合已有知识和搜索
- **「今天进来一批笔记」** → `getnote notes --since-id` + `getnote note <id>` 逐条读
- 直接粘贴了文章 → 直接分析
- **大知识库（>100条）**：拆成 5-8 个主题方向分别搜索，每方向 top 3-5 条

### 步骤 2：输出 MKS 卡片

```
【领域/对象】{领域名称或文章标题}

【核心目标】
一句话：这个领域是干什么的？解决什么问题？

【核心概念（5-10 个）】
① 概念名 — 一句话解释
...

【概念关系】
逻辑关系（→ ↑↓ 因果/层级/并列）
⚠️ 从具体对象本身提取，不套用预设框架

【最小知识集（3-5 个，必须深刻理解）】
★ 概念A — 为什么它是骨架中的骨架
...

【边界性知识】
只需知道存在、知道什么场景会用到：
- 知识点X（场景：...）

【学习路径】
基于 MKS 的学习顺序建议
```

### 步骤 3：用户确认
输出后问：「这个骨架准不准？有没有需要增减的概念？」

### 第二阶段：互动拆解
确认后提供五种模式：知识卡片、思维导图、类比教学、苏格拉底问答、考试模式。

### 第三阶段：任务驱动
设计 2 个真实任务（结构化输出 + 创造/解决问题）。

### 第四阶段：沉淀
输出最终版卡片，询问是否保存。

---

## 场景 B：大规模笔记库批量 MKS（流水线）

### 触发条件
Get笔记中知识库有大量笔记（>200篇）带统一标签，需拆分为多个 MKS 专题 + 统一首页。

### 流水线概览

```
全量KB JSON → 标签筛选 → 主题分类 → 并行Claude Code(N专题) → 校验+修复 → 首页生成 → GitHub Pages 发布 + 飞书云盘存档
```

### 步骤 B1：提取与分类（🔴 2026-06-06 改订：正文校验 + 系列增量去重）

大规模笔记库先分类再生成。Hermes 必须做内容级分类，不能只看标题。

#### B1.0：系列增量检查（连载内容必做）

当内容属于连载系列（吴军来信、硅谷来信等有多个季/版本），拆题前必须先检查已有 MKS 项目覆盖了什么：

```bash
# 1. 列出已有吴军相关 MKS 专题
ls /home/admin/mks-knowledge/硅谷来信1_v2/*.html /home/admin/mks-knowledge/硅谷来信3/*.html /home/admin/mks-knowledge/谷歌方法论/*.html

# 2. 提取已有项目的专题名和核心概念
for f in /home/admin/mks-knowledge/项目名/*.html; do
  echo "=== $(basename $f) ==="
  python3 -c "
import re
with open('$f') as fh:
    c = fh.read()
    title = re.search(r'<title>(.*?)</title>', c)
    concepts = re.findall(r'\"name\":\"([^\"]+)\"', c)[:8]
    print(f'  {title.group(1) if title else \"?\"}')
    print(f'  概念: {concepts}')
"
done
```

**增量定位原则**：
- **不跳过同类内容**：即使新项目覆盖的领域与前作重叠（如财富、教育、历史），也不跳过——而是用新的角度或新的归类方式重组
- 核心问题：「这批内容的本项目独特框架是什么？」——找到串联这批笔记的独有线索（如吴军来信2 的「底层逻辑」元框架：不讲财富怎么做，而是讲如何思考财富问题。同是财富内容，硅谷来信1 讲实操、吴军来信2 讲思维方式）
- 在专题描述中注明「与 X 项目的 Y 专题形成互补，新角度为 Z」
- 移交 Claude Code 时，在 prompt 中说明已有项目的覆盖范围和新项目的独特角度，要求它基于新角度提取概念，而不是重复前作已充分展开的内容

#### B1.1：正文级分类（必须）

标题+标签分类不够。Hermes 必须读每篇笔记的正文（至少前 300 字）做内容级校验：

```
1. 先按标题+标签粗分（关键词匹配）
2. 逐专题抽取 20% 样本读正文（均匀间隔采样），验证归属是否合理
3. 发现边界模糊的笔记（标题歧义、跨专题关键词）→ 读正文全文判定归属
4. 修正粗分结果，输出最终 topics.json
```

**判断标准**：如果一篇笔记的标题看起来属于 A 专题但正文核心论点在 B 专题 → 以正文为准。

🔴 **源数据标签验证（必做，2026-06-13 科技参考3航天专题事故教训）**：当源数据自带分类标签（如 JSON 的 `chapter_id`、Get 笔记的 `tags`），不能假设标签正确。派 CC 前必须对每批 JSON 做 **10% 随机抽样**——读标题+正文首段确认实际主题与标签名一致。科技参考3的「2-航天与太空探索」JSON 20篇文章标签为航天，但内容全是消费电子/FDA/芯片——CC 发现后编造了贴合标签的虚假航天内容。**抽样命令**：`python3 -c "import json,random; d=json.load(open('file')); [print(n['title'][:80], n.get('content_text','')[:120]) for n in random.sample(d, min(10,len(d)))]"`

**常见误分类场景**：
- 「钱从来不是大风刮来的」→ 标题像财富，正文实际讲政府与市场边界（经济/政治）
- 「好撒玛利亚人法」→ 标题像法律/逻辑，正文实际讲人性与救助者的社会困境
- 「商品定价的底层逻辑是什么」→ 标题含「底层逻辑」关键词，本质是经济学内容

```bash
# Hermes 正文校验（execute_code 内采样分析）
# 输出: 边界笔记清单 + 重新归类建议
```

#### B1.2：分类方式

两种方式：

- **方式 A：Hermes 关键词+正文分类（推荐）** — 先粗分 → 正文采样校验 → 修正 → 输出 topics.json。比 delegate_task 更快更可靠。
- **方式 B：delegate_task 自动分类** — 见 `references/batch-classification.md`。适合标题无统一模式的场景，但 >100 篇可能超时。

```bash
# 拉取全量（大KB约需2-3分钟）
getnote kb <KB_ID> --all --output json > /tmp/kb_all.json

# Hermes 关键词+正文分类（execute_code 内分析 JSON）
# 输出 topics.json: [{topic_id, topic_name, topic_desc, count, notes}, ...]
```

#### 🔴 大课程拆分铁律（>200 篇必修，2026-06-11 定）

课程正文 >200 篇必须拆成 5-7 个子专题，每个独立 MKS HTML + 一个导航页。严禁单页塞一切——精华被压缩丢失。

参考吴军课程组织方式：硅谷来信1_v2（7专题+导航页）、世界文明史（5专题+时间线导航页）、谷歌方法论（多专题+导航页）。334 篇科学思维课单页 → 被用户否决重做。

**拆分原则**：按内容自然边界拆，Claude Code 通读全量后自行判断。每子专题 40-60 篇，有自己的核心概念群。导航页由 Claude Code 创意设计——说「创造独特视觉隐喻呼应课程精神」，不说「用网格模式」。

#### 单课程快捷路径（<80 篇的结构化课程）

当笔记来自单一结构化课程（如「逻辑思维训练50讲」57 篇），且标题有清晰的序号/模块标记时，跳过 B1 正文分类——直接导出全量 JSON，派 Claude Code 生成单专题 HTML。

**判断条件**：
- 所有笔记标题含同一课程标签（如「| 吴军·逻辑思维训练50讲」）
- 篇数 <80
- 标题有清晰的课时序号或模块结构

**操作**：
```bash
# 直接从 SQLite 导出 → Claude Code
python3 -c "..." > /home/admin/course_notes.json
# 单专题，直接派 Claude Code
```

> 此路径跳过 Hermes 分类环节，因为课程本身的结构就是最好的主题划分。

#### 🔴 会话中断恢复（2026-06-05 定）

MKS 项目常跨多个会话完成。当用户说"重新开始""不要参考之前"时：

1. **先查本地库，不盲目重拉**：`sqlite3 ~/.hermes/data/getnote_kb.db "SELECT COUNT(*) FROM kb_notes WHERE tags LIKE '%课程标签%'"`
2. **数据质量检查**：导出后检查空标题/空 content 比例。链接笔记（type=link）可能只有标题和标签，content 为空——这些笔记无法用于 MKS 内容提取，需从有效计数中剔除。
3. **有效笔记数决定专题数**：按**有标题有 content** 的笔记数做分类，不是按标签匹配总数。
4. **本地已有数据足够时，直接从 SQLite 开始**，不必等 Get 笔记同步（上次会话的后台进程可能已失败）。

**典型数据质量检查结果**：
- 标签匹配 367 条 → 有效（有标题有 content）317 条 → 空壳 50 条（13.6%）
- 按 317 条有效笔记分 5 专题（60/62/82/59/54），而非按 367 条分

### 步骤 B2：主题划分原则

- 每专题按内容内聚划分，不硬限篇数
- `--max-turns 1000` 兜底，正常任务远不到上限
- 拆分阈值：单专题 JSON > 500KB 才需拆分（不管多少篇）
- 优先按内容自然边界，不强行均分

#### 🔴 专题大小限度的真实理由

**不是 token 窗口问题**。DeepSeek v4 上下文 ≥128K tokens，60 篇笔记（~20K tokens）+ skill 规范（~30K tokens）+ prompt ≈ 50-80K tokens，窗口充裕。

**真正的瓶颈是 Claude Code 的 turn 预算**（`--max-turns`）。笔记越多 → 逐篇阅读分析消耗的 turn 越多 → 留给 HTML 生成的 turn 不够。现在设为 1000 兜底，正常任务远不到这个数。

#### 🔴 膨胀专题拆分（由 Hermes 执行）

**判断标准**：单专题 JSON 文件 > 500KB → 拆分（不论多少篇）

**拆分流程**：
1. 读该专题笔记正文，按子主题聚类
2. 每子专题 JSON 控制在 300-500KB
3. 更新 topics.json

**拆分后验证**：
- 每个子专题有独立的核心概念群
- 子专题间有清晰边界（核对 10% 样本文本确认无交叉）

### 步骤 B3：并行启动 Claude Code（生成专题 HTML）

每个专题生成独立 HTML 文件。所有文件部署到 GitHub Pages 后，手机和电脑浏览器均可直接访问，无需生成合并版。

⚠️ **并行策略分两种场景**：
- **笔记 MKS**：专题间内容独立，可分专题并行 Claude Code（每专题处理不同笔记子集）
- **课程正文 MKS**：概念抽象需要全文理解，必须一门一课串行。并行导致概念浅层化（V1 三课并行全部被拒）。详见 `references/course-mks-prompt-template.md`
- **目标导向 prompt**：给 Claude Code 目标和约束，不给微操步骤。让 Claude Code 自己决定概念体系（V4 自建三层架构优于 V3 Hermes 强制的"十个概念"）。详见 `references/course-mks-prompt-template.md`

```bash
cd /home/admin && claude --max-turns 1000 --effort max --dangerously-skip-permissions \
  -p "基于 {专题文件} 中{N}篇笔记，生成最小知识集交互式HTML。

## 规范
先读取 .claude/skills/mks-builder.md，严格按照该规范执行。

## 专题定位
母隐喻：{项目级母隐喻——串联所有专题的核心框架}
本专题：{专题名}的{组件}模块——{一句话描述}

## 已有项目覆盖（连载内容必带，防重复）
- {项目A}：{已覆盖的专题和角度}
- {项目B}：{已覆盖的专题和角度}
- 本项目新角度：{独特框架，与其他项目的本质区别}

## 关键要求
- SVG概念关系图根节点从内容本身提取
- 选择题正确选项索引0-3严格均匀分布 {0:5,1:5,2:5,3:5}
- 9标签页、暖色系、localStorage、响应式
- 知识卡片移动端高度≤视口60%，翻页按钮不被遮挡
- 预留返回首页链接（<a href=\\\"index.html\\\">）

## 验收闸门
HTML文件已生成，且所有JavaScript语法验证通过。

## 输出
{输出路径}\" 2>&1
```

⚠️ Claude Code 调度铁律：
- `--dangerously-skip-permissions` 必带
- `background=true + notify_on_complete=true`
- 不给微操指令，给目标和资源
- 笔记素材须在 `/home/admin/` 下（`/tmp` 被沙箱拦截）
- 可在 prompt 中嵌入 `/goal <验收条件>` 防提前退出
- 🔴 **MKS 文件调试/修复一律用 Claude Code，不用 Hermes 手动 patch（单行单点例外）**：Hermes 手动逐文件 patch 效率极低——修改了文件但线上不生效，反复排查才发现改错了目录。Claude Code 有自己的文件读写能力，能 diff 对比、读标准模板、批量替换、跑 validator，且不依赖 Hermes 的 search_files 工具（后者在文件被修改后返回缓存旧结果）。用户明确吐槽「现在的 agent 效率太低」时，说明任务已超出 Hermes 直接操作的能力边界——立刻转交 Claude Code。
- 🔴 **Hermes 手动替换 MKS SVG 导致 JS 语法错误（2026-06-05 实况）**：Hermes 用 Python 脚本替换 renderSVG 函数中的 innerHTML 赋值行时，丢失了函数闭合的 `})();` 行，导致浏览器报 `Unexpected end of input`——全部 tab 切换、卡片翻转、题库等交互失效。根因：① 只 grep 了 viewBox 确认 SVG 替换，未在浏览器中打开验证 JS 是否有报错；② 手动行级替换难以保证不破坏周围的 JS 结构。教训：任何涉及 MKS HTML 文件内 JS 代码修改的操作（含 SVG 替换、DOM 结构调整、函数修改），一律写问题清单交给 Claude Code 执行，Hermes 只负责浏览器最终复核。

### 步骤 B4：校验

```bash
node ~/.hermes/skills/note-taking/mks-builder/scripts/validate_mks_html.js {file}
```

关键检查项：JS语法、标签页≥9、MCQ 15-20题、答案0-3分布≤50%、暖色系、localStorage。

### 步骤 B5：修复

#### 🔴 第一轮生成后的质量审查（2026-06-06 实战教训）

Claude Code 首次生成的 MKS HTML 几乎必然有合规性问题。不要假设第一轮产出就是交付品。

**常见首轮缺陷模式**：
- flip 卡片：`.flip-inner` 用了 `position:absolute` 而非 `relative`、`.flip-back` 缺失 `rotateY(180deg)`
- tab 系统：使用 `.show` 类切换而非 `.active`、tab-bar 非 sticky
- 按钮：使用 `btn-primary` 等非标准 class
- 旧标签残留：文案中出现"选择题""简答题""关系图""决策模拟"等旧标签词
- 思维导图：使用过时的 `node-header`/`node-body` 结构而非 `.mindmap .node` 模式
- 追问/案例分析：复用思维导图 CSS 类而非专用样式
- CSS 变量：缺少 `--accent2`/`--gold`/`--radius` 等规范变量

**推荐修复流程（经验证最高效）**：

| 轮次 | 谁 | 做什么 |
|------|-----|------|
| 第1轮 | Claude Code | 生成 HTML（初始版本） |
| 第2轮 | Claude Code | **自检自修**——浏览器逐 tab 打开，对照黄金模板找差异，修复所有问题，跑 validator（推荐 `/goal` 模式，只给目标和标准，不列具体问题清单） |
| 第3轮 | Hermes | 复核——代码级检查 + 浏览器验证 |

**第2轮 prompt 模板**：
```
/goal 打开详情页，逐tab对照黄金模板，自己发现所有不符合规范的问题，修复，自测，确保完美交付。

黄金模板：
- 翻转卡片：硅谷来信1_v2/4-历史与文明对话.html
- CSS命名：谷歌方法论/1-世界名校.html
- 题库交互：硅谷来信1_v2/7-职场与人生智慧.html
- 思维导图：谷歌方法论/5-文明与全球化.html

重点关注：知识卡片大小/翻转效果、思维导图可折叠/完整性、所有9个tab交互
验收闸门：逐tab在浏览器确认无误后交付
```

此模式在逻辑思维训练50讲验证：Claude Code 自检发现 9 类问题（含 150+ 处弯引号），修复率 100%，Hermes 复核零新增问题。
- **答案偏斜（最常见）**：某位置>50% → 修复 prompt 写死 `{0:5,1:5,2:5,3:5}`，重跑。不要手动改 HTML
- **MCQ 格式不匹配**：`sed 's/\"ans\":/\"answer\":/g'` 一键修复（校验器不认 `\"ans\":`）
- **JS 弯引号**：`python3 -c` 替换 `<script>` 块内 `\\u201c→\\\"` `\\u201d→\\\"`
- **顽固个案（同一专题失败 3 次）**：**模板替换法**——取已通过的同类专题 HTML 做模板，替换标题/概念/文件名 → 5 分钟出结果。比无限重跑更可靠
- 🔴 **Claude Code 静默卡死（最常见根因：JSON 太大）**：10+ 分钟无输出且文件 mtime 未变 → kill。首要诊断：`ls -lh <data.json>`——超过 1MB 的 JSON 是 CC 静默死亡的第一嫌疑人。修复：Python 预抽取 `title + content[:400]` 瘦身到 <100KB，再让 CC 读完整 JSON 做深度出题。科技参考3 医学 2MB→75KB 后秒过。完整故障模式手册见 `references/cc-silent-failures.md`
- 🔴 **两阶段生成恢复策略**：CC 多次静默失败后，拆成 Phase 1（HTML结构+CSS+SVG图形，30t）+ Phase 2（JS逻辑+数据，30t）。Phase 1 即使超时也能产出可用的半成品（HTML结构完整，仅缺 JS），比全部丢失好。科技参考3 医学专题用此模式抢救成功
- 🔴 **大 JSON 静默死亡（>1MB）**：Claude Code 在读取 >1MB JSON 时 token 消耗在加载阶段就耗尽 turn 预算，exit 0 但零输出、零文件修改。必须先预精简 JSON（保留 title + 前 400 字符，2.1MB→75KB），prompt 中同时引用精简版和完整版路径。后备策略：两阶段生成（阶段1 HTML+SVG→commit→阶段2 补JS）。详见 `references/large-json-claude-code.md`。
- 🔴 **Claude Code 部分成功部分静默失败**：并行 7 个 Claude Code 中通常 3-5 个成功，1-2 个静默退出或生成破损文件（无报错、validator 失败）——不是 prompt 或 API 问题，是并发资源竞争。**不要重跑全部**：先 `ls -la` 检查哪些文件缺失或过小（<20KB 肯定破损），只对问题专题单独重跑 Claude Code。重跑时用 `process(action="poll")` 监控进程，超过 180s 无输出且无文件 → kill 并再重跑。单专题重跑成功率接近 100%。
- 🔴 **delegate_task 不适合长任务**：delegate_task 子 agent 是同步执行且依附于父会话——用户发消息会中断父 agent，子 agent 跟着被 kill（`Parent agent interrupted`）。MKS HTML 生成这类 5-15 分钟任务**只能用 Claude Code 后台进程**（`terminal(background=true, notify_on_complete=true)`），不能用 delegate_task。
- 🔴 **Claude Code `--output-format stream-json` 必须配 `--verbose`**：单独使用 `--output-format stream-json` 会直接报错退出（exit 0 但有 `Error: When using --print, --output-format=stream-json requires --verbose`），任务零进度。非交互模式下不需要指定 output-format，Claude Code 默认输出即可。已验证：去掉 `--output-format` 参数后进程正常运行。Claude Code 启动后 10-40+ 分钟零输出、目标目录无文件——**先查 API key 是否有效**（curl 测试），不要断定"模型扛不住"或拆分任务。完整排查路径见 references/claude-code-troubleshooting.md。关键词：401、Authentication Fails、paste-cache。

#### 批量结构修复（多个文件导航/总览不统一）

当多个专题文件的导航标签或知识集总览结构与「专题页面统一规范」不一致时，用以下并行修复协议：

1. **选定黄金模板**：从已合规的文件中选一个（如 `1-科技与商业逻辑.html`），它是结构和样式的标准参照
2. **并行派发**：用 `delegate_task` 同时派发 2-3 个子 agent，每个负责 1-2 个文件
3. **每个子 agent 的指令模板**：
   ```
   黄金模板: /path/to/1-科技与商业逻辑.html
   目标文件: /path/to/N-专题名.html
   要求:
   - 以黄金模板的 HTML/CSS/JS 框架为基础
   - 从目标文件提取独特内容（概念名、MCQ、SVG、案例等）
   - 导航改为标准 9 标签，知识集总览补全 7 子项
   - 选择题+简答题合并为「题库系统」
   - 运行 validate_mks_html.js 验证
   ```
4. **汇总验证**：子 agent 完成后，Hermes 逐文件验证 9 标签 + 7 总览子项齐全

> 此协议已在硅谷来信 1 验证：2/6 号文件完全重写，3/5 号文件补全，4 个并行子 agent 全部一次通过。

### 步骤 B6：首页（项目级导航页）

每个 MKS 项目必须有独立的 `index.html` 导航页，放在项目子目录下（如 `世界文明史/index.html`）。根 `index.html` 链接到项目子目录而非直接链到专题 1。

**布局选择**：专题间有先后演进关系 → 时间线模式；专题间并列独立 → 网格模式。详见 `references/navigation-page-design.md`。

**生成方式**：统一用 Claude Code `/goal` 模式——只给内容主题和情感基调，不给具体设计指令。让它读专题内容后自己判断视觉隐喻。经验：说「用XX模式」产出平庸，说「创造独特视觉隐喻，呼应课程精神」产出惊艳。

**不要做**：Hermes 手写导航页（2026-06-06 逻辑思维训练50讲首版导航页太敷衍，阿森做的简单跳转页被用户驳回，Claude Code 重新创作「推理齿轮」隐喻后通过）。

### 🔴 多作者总导航页（Hub 模式，2026-06-13 新增，2026-06-15 扩展）

当 MKS 覆盖多位作者时，需要一个**跨作者总导航页**（Hub）串联各系列入口。标准三翼架构：

```
Hub 总纲（hub.html）
├── Wing A: 学者系列 → 吴军/卓克等作者入口
├── Wing B: 学者系列 → 同上
├── Wing C: 工具与方法论 → 创意模式库 / 版本历史 / 演化全史
└── Wing D: 即将到来 → 占位预留
```

**核心原则**：
1. **系列级导航，不展开子项**：Hub 页每个系列为单一入口大门，不罗列课程卡片——细节留给各系列自有导航页
2. **不覆盖原有导航页**：吴军的 index.html 保持原样，Hub 只做跳转
3. **跨 repo 用绝对 URL**：卓克在 mks-zhuoke repo，链接必须用 `https://andy-zokelink.github.io/mks-zhuoke/`
4. **预留扩展区**：新老师用虚线占位卡片，保持设计一致
5. **深色科技风 + 档案馆隐喻**：与 MKS 页面风格一致，知识沉淀的厚重感

#### 🔴 Wing C：工具与方法论（标准组件，2026-06-15 新增）

Hub 页必须包含「工具与方法论」入口，链接 MKS 体系的元文档。**推荐放在导航栏下拉菜单中**（而非正文 section），保持 Hub 页正文简洁，只保留学者入口和即将到来。

下拉菜单内容：
| 菜单项 | 链接 | 说明 |
|--------|------|------|
| 🎨 创意模式库 | `references/creative-patterns.html` | 隐喻选择矩阵 / 5大叙事模式 / 13种交互形式 / 视觉系统规范 |
| 📜 技能演化全史 | `references/versions/evolution.html` | MKS Builder 完整演化历程 |
| 版本历史（分隔线下） | | |
| MKS 1.0 · 单页提取 | `references/versions/v1.0.html` | |
| MKS 2.0 · 批处理流水线 | `references/versions/v2.0.html` | |
| MKS 2.20 · 品控体系 | `references/versions/v2.20.html` | |
| MKS 2.36 · 创意模式库 | `references/versions/v2.36.html` | |

**实现细节**：导航栏 `.nav-links` 内添加 `<span class="nav-dropdown">`，CSS 用 `position:absolute` 下拉面板 + `opacity/visibility` 过渡动画。hover 展开，银色系配色（`--method-silver`）。

**历史**：v1 版 Wing C 是正文 section（三个 portal-card 横排），2026-06-15 Andy 要求移到导航栏下拉菜单——"不要放在主页面，把它放到右上角导航栏的下拉菜单里"。

**版本页面规范**：每个主版本一个独立 HTML 页（深色档案馆主题，与 Hub 配色一致），包含版本导航条（可在版本间跳转）、版本能力矩阵、代表作卡片、底部返回总纲链接。详见线上实例：[v1.0](https://andy-zokelink.github.io/mks-knowledge/references/versions/v1.0.html) / [v2.0](https://andy-zokelink.github.io/mks-knowledge/references/versions/v2.0.html) / [v2.20](https://andy-zokelink.github.io/mks-knowledge/references/versions/v2.20.html) / [v2.36](https://andy-zokelink.github.io/mks-knowledge/references/versions/v2.36.html)。

**最小交付物**：
- 项目标题 + 篇数/专题数概述
- 每个专题的入口链接（含章节号、emoji、描述）
- 返回知识主板链接（`../index.html`）
- 响应式适配

### 步骤 B7：发布与推送（🔴 2026-06-14 改订：必须验证远端）

#### 🔴 多作者体系隔离（2026-06-11 定）

**不同作者的 MKS 必须部署到独立的 GitHub Pages 仓库，禁止混入同一 repo。** 吴军课程 → `andy-zokelink/mks-knowledge`，卓克课程 → `andy-zokelink/mks-zhuoke`，其他作者类推。

违例：6/11 将卓克 Phase 1 产出直接 push 到吴军的 mks-knowledge 仓库，被用户当场纠正。根因：把「MKS 发布」等同于「mks-knowledge 仓库」——没有检查项目归属。

**规则**：新建作者体系前，先 `gh repo create andy-zokelink/mks-<author> --public` 创建独立仓库，`gh api repos/andy-zokelink/mks-<author>/pages -X POST` 启用 Pages。所有该作者的课程 HTML 只进该仓库。

```bash
# 推送到 GitHub Pages（主力预览平台）
cd /home/admin/mks-knowledge   # 或 mks-zhuoke / mks-<author>
git add -A
git commit -m "项目名: 描述"
git push origin main

# 验收通过后推送到飞书云盘（最终存档）
cd /path/to/project_dir
lark-cli drive +push --folder-token <项目文件夹token> --local-dir . --as user
```

> GitHub Pages 是主力预览平台，手机/电脑浏览器均可直接访问。飞书云盘仅作终版存档。不再生成合并版单文件。

**Blog 自动存档**：cron 触发的简报/周报会自动发布到 GitHub Pages Blog 系统。详见 `references/blog-archive-pipeline.md`。


## 发布前最终检查

- [ ] **项目有独立 `index.html` 导航页**——根 `index.html` 不能直接链到专题 1 的 HTML 文件，必须是项目子目录（`href="项目名/"` 而非 `href="项目名/1-专题名.html"`）
- [ ] **单专题项目例外**：只有一个专题时，可以不创建导航页，但根 `index.html` 必须直接链到 HTML 文件（`href="项目名/1-专题名.html"`）。链到目录而目录无 `index.html` → GitHub Pages 返回 404
- [ ] **导航页布局匹配专题关系**——演进型用时间线、并列型用网格
- [ ] **浏览器验证**——`browser_navigate` 确认每个链接可点、响应式降级正常

---

## 场景 E：跨课程合并 MKS（多课程→单专题）\n\n### 触发条件\n多个相关课程（如「信息论40讲」+「GPT」+「5G」）内容互补，用户要求合并成一个 MKS 专题。\n\n### 数据准备\n\n```bash\n# 1. 查询本地库中各课程的笔记数\nsqlite3 ~/.hermes/data/getnote_kb.db \\\n  \"SELECT title LIKE '%关键词1%' as grp, count(*) FROM kb_notes WHERE kb_name='wujun' AND (title LIKE '%关键词1%' OR title LIKE '%关键词2%') GROUP BY 1\"\n\n# 2. 若本地库缺失某课程，从 Get 笔记 KB 拉取\n# 使用 sync_getnote_kb.py 增量同步（大 KB 3-5 分钟）\npython3 ~/.hermes/scripts/sync_getnote_kb.py --json-file /tmp/kb_dump.json --kb-name wujun\n\n# 3. 导出合并笔记为 JSON\npython3 -c \"\nimport sqlite3, json\nconn = sqlite3.connect('$HOME/.hermes/data/getnote_kb.db')\nrows = conn.execute(\\\"SELECT * FROM kb_notes WHERE kb_name='wujun' AND (title LIKE '%课程1%' OR title LIKE '%课程2%' OR title LIKE '%课程3%')\\\").fetchall()\ncols = ['note_id','kb_name','title','content','note_type','created_at','updated_at','tags','ref_content','source']\ndata = [dict(zip(cols, r)) for r in rows]\nfor d in data: d['_course'] = '课程1' if '课程1标识' in (d.get('title','') or '') else ('课程2' if '课程2标识' in (d.get('title','') or '') else '课程3')\nwith open('/home/admin/merged_notes.json', 'w') as f:\n    json.dump(data, f, ensure_ascii=False)\nprint(f'Exported {len(data)} notes')\n\"\n```\n\n### 专题设计\n- **母隐喻必须是多层架构**：如三圈层（CPU层→应用层→网络层）、三支柱、四象限等。每层对应一门课程\n- **概念从各层提取**：每层 3-4 个核心概念，总数 8-12 个\n- **骨架概念跨层选择**：确保每层至少 1 个骨架概念\n- **SVG 概念图体现层级关系**：用 Graphviz dot 的三圈层/多区域布局\n\n### 派 Claude Code 生成\n\n```bash\nclaude --max-turns 1000 --effort max --dangerously-skip-permissions -p '\n基于 /home/admin/merged_notes.json 中的{N}篇笔记，生成最小知识集交互式HTML。\n笔记来自{N}个课程，用 _course 字段区分。\n\n先读取并严格遵循 .claude/skills/mks-builder.md 中的全部规范。\n\n## 专题定位\n母隐喻：{多层架构隐喻描述}\n\n## 关键要求\n- 9标签页、暖色系、localStorage、响应式\n- SVG概念关系图用Graphviz dot（{圈层}布局）\n- 概念卡片: flip-card + clamp(220px,40vw,280px) + addEventListener翻转\n- 题库: 🎲随机抽题+重置，staticQuiz初始hidden\n- JS中所有中文书名号必须用 \\u201C/\\u201D，禁止ASCII \" 当书名号\n- MCQ均匀分布{0:5,1:5,2:5,3:5}\n- 输出到 /home/admin/mks-knowledge/项目名/1-专题名.html\n\n## 自检步骤\n1. node --check 无语法错误\n2. concepts数组长度 ≥ 8\n3. MCQ 20题，答案0-3各5题\n4. SVG viewBox 存在且非空\n5. 深度追问+案例分析+实战决策有内联HTML内容\n\n## 验收闸门\n提取 <script> 块后用 node -e "new Function(...)" 检查通过 AND 9标签正确 AND SVG非空 AND MCQ均匀分布 AND 深度追问有内容\n' --output-format text 2>&1\n```\n\n### 复核与发布\n- Hermes 浏览器逐标签验证（知识集总览→卡片→题库→...→实战决策）\n- 更新知识主板 index.html 加入新专题入口\n- `git add && git commit && git push`\n\n---\n\n## 场景 D：从旧版 HTML 重生成（技能升级后）

### 触发条件
MKS 技能规范更新后（如新增 Graphviz 图表、卡片设计统一等），需要将已有专题 HTML 用新规范重新生成。**不重新分析笔记**——直接从旧 HTML 中提取数据，套用新模板。

### 步骤 D1：全项目审计

先用审计脚本扫描所有文件，输出问题清单，确认是全量重生成还是局部修复。

```bash
# 7 文件完整审计（Python 脚本见 references/mks-uniformity-audit.md）
python3 -c "..."  # 完整脚本
```

如果 7 个文件全部缺 `.top-bar` / `.flip-card` / `staticQuiz` / 有旧标签 → 全量重生成。
如果只有 2-3 个文件缺某项 → 局部派 Claude Code 修复。

### 步骤 D2：并行重生成

```bash
# 1. 创建新文件夹（不覆盖旧文件）
mkdir -p /home/admin/mks-knowledge/项目名_v2/

# 2. 并行启动 Claude Code（每个专题一个进程）
for i in 1 2 3 4 5 6 7; do
  claude --max-turns 1000 --effort max --dangerously-skip-permissions \
    -p "读取旧版 /home/admin/mks-knowledge/项目名/$i-专题名.html 全部概念数据，
        用 .claude/skills/mks-builder.md 规范重新生成 HTML。
        概念关系图用 Graphviz dot（dot -Tsvg 渲染），禁止手写 SVG 坐标。
        输出到 /home/admin/mks-knowledge/项目名_v2/$i-专题名.html。
        保留原文件全部概念/MCQ/简答/案例/决策数据。标准9标签+暖色系。
        validator确认。" \
    --output-format text &
done
```

### 验收

```bash
# 逐文件验证（结构 + 内容完整性）
for f in /home/admin/mks-knowledge/项目名_v2/*.html; do
  echo "=== $(basename $f) ==="
  # 结构化校验
  node ~/.hermes/skills/note-taking/mks-builder/scripts/validate_mks_html.js "$f"
  # 弯引号
  grep -Pn '[\\x{201c}\\x{201d}]' "$f" && echo "❌ 弯引号" || echo "✅ 弯引号"
  # 概念关系图非空（SVG 有图形元素 或 JS 有渲染函数）
  has_path=$(grep -cP '<(path|polygon|rect|circle|ellipse|text|g\\b)' "$f")
  has_render=$(grep -cP 'renderSVG|\.innerHTML\s*=.*svg' "$f")
  if [ "$has_path" -gt 0 ] || [ "$has_render" -gt 0 ]; then echo "✅ 概念图"; else echo "❌ 概念图为空"; fi
  # 导航条统一
  has_topbar=$(grep -c 'class="top-bar"' "$f")
  has_sticky=$(grep -cP '\.top-bar\s*\{[^}]*position\s*:\s*sticky' "$f")
  if [ "$has_topbar" -gt 0 ] && [ "$has_sticky" -gt 0 ]; then echo "✅ 导航条"; else echo "❌ 导航条不统一"; fi
done
```

### 后续步骤
验收通过后 → 推送到 GitHub Pages + 飞书云盘存档（步骤 B7）。

---

## 场景 F：Meta-MKS / 跨课思想体系重组（多课程→统一框架）

### 触发条件
用户拥有多门互相关联的课程笔记（如吴军知识库 13 门课、2,000+ 篇），要求不是按课拆 MKS 而是按思想线索跨课重组，产出有增量价值（区别于已有单课 MKS）。

### 核心区别 vs 标准 MKS
- 数据源：多门课跨标签混合（不是单课）
- 分类方式：按思想层级聚类（不是按课程主题）
- 页面格式：自由格式思想专题页（不强制 9 标签 MKS 模板）
- Claude Code：最大创作自由（不套模板）
- 导航页：思想建筑全景图（不是项目目录页）

### 步骤 F1：框架设计（Hermes + 用户协作）
**必须先定框架再动手。** 全库摸底（SQLite 各课篇数 + 标签分布）→ 20% 正文抽样 → 输出层级框架草案 → 用户确认。

### 步骤 F2：按层提取数据
按框架层级导出 JSON，标注课程来源。单层 > 2.5MB 需拆分（如 Layer 3 1018篇/4MB → 3A + 3B）。

### 步骤 F3：创意 Brief 设计（最关键的 Hermes 工作）
每个页面一个 brief 文件。**不写规范，写愿景。** 含项目定位、核心洞察（基于抽样的跨课连线）、设计方向建议（给最大自由）、验收闸门（3-5 条硬性条件）。Brief 写作：一句话独特价值 + 具体跨课连线实例 + 鼓励创造新格式。

### 步骤 F4：并行启动 Claude Code
先导航页再层详情页，`--max-turns 1000` 足够（熔断器不是目标值）。`background=true + notify_on_complete=true`。Brief 用文件传参（`"$(cat file.txt)"`）。

### 步骤 F5-F6：导航页链接更新 + 复核交付
多层拆分时更新链接。Hermes 逐页浏览器复核。更新知识主板。push。

### 本 session 成功模式
三层金字塔框架（认知底座→文明方法论→人生操作系统）+ 四条贯穿线索。导航页：Thought OS 隐喻（Claude Code 自主创造）。Layer 分拆：Layer 3 1018篇→3A(668)+3B(350)。4 路并行 Claude Code，全部成功。

完整实战案例见 `references/meta-mks-wujun-case-study.md`。创意项目（历史星河、双镜头、思想河流等）的 brief 写作和预处理模式见 `references/creative-synthesis-projects.md`。

---

## 场景 G：播客制作（MKS 项目交付后）

### 触发条件
- MKS 项目已有完整的导航页 + N 个专题 HTML
- 用户要求"做播客""把内容做成音频"

### 核心流程
MKS 内容 → Claude Code 写稿 → 清洗脚本 → Coze CLI TTS → 嵌入导航页

### G1：生成播客脚本
每模块一期。Claude Code 逐模块通读 HTML，生成口语化中文脚本（吴军播客风格：对话感、有故事有洞察）。目标 3000-4000 字/期（10-12 分钟，Coze ~330 字/分钟）。输出到 `<project>/podcast/ep{N}.md`。

🔴 **6+期批量生成策略**：单次 CC 生成 6 期脚本极易超 max-turns。拆两批并行：
```bash
# 批次1: ep01~ep03（3期×60t），批次2: ep04~ep06（3期×60t）
# 每批独立 prompt 文件，含风格规范 + 3个专题HTML路径 + 字数目标
# 两个 terminal(background=true) 并行启动
```
每批 3 期 × 60 turns = 充足余量。不要一批 6 期（必然超时）。

### G2：清洗脚本
去除 Markdown 标记（`#` `**` `[停顿2秒]`），输出纯文本 `/tmp/ep{N}_cleaned.txt`。

### G3：Coze TTS 生成音频
🔴 stdin 批量陷阱：bash for 循环中 `--stdin` 首次调用消费全部 stdin。每次必须独立 `< /tmp/ep{N}_cleaned.txt`。
🔴 output-path 是目录：需 `find + cp` 提取实际 mp3。

```bash
OUTDIR="<project>/podcast/audio" && rm -rf "$OUTDIR" && mkdir -p "$OUTDIR"
for ep in 01 02 03 04 05 06; do
  TMPOUT="/tmp/podcast_ep${ep}" && rm -rf "$TMPOUT"
  coze generate audio --stdin --output-path "$TMPOUT" < "/tmp/ep${ep}_cleaned.txt"
  MP3=$(find "$TMPOUT" -name "*.mp3" -type f | head -1)
  [ -n "$MP3" ] && cp "$MP3" "$OUTDIR/ep${ep}.mp3"
  rm -rf "$TMPOUT"
done
```

### G4：验证时长
`ffprobe` 检查每期 ≥ 600s（10 分钟）。不达标则扩充脚本重生成。

### G5：嵌入导航页
播客播放器（期数列表 + 播放按钮 + 底部固定播放条）。完整模板见 `podcast-production` skill 的 `references/homepage-player.html`。

```bash
cd <project> && git add podcast/ index.html && git commit -m "feat: 添加播客" && git push
```

---

## 🧠 卓克 MKS 项目实战教训（2026-06-11/14）

> Claude Code批量生成可靠性、大JSON处理、创意图验收等详见 `references/zhuoke-cc-reliability.md`
> 
> **创意重设计 prompt 设计方法论**（2026-06-14 科技参考3 七专题全量重设计）详见 `references/zhuoke-creative-redesign-prompts.md`——核心原则：禁止卡片罗列、根据内容独创、视觉图要有故事性、两轮精炼模式、index 视觉图四版迭代教训。
>
> **🆕 全自主 MKS 模式**（2026-06-15 新增+修订）：用户明确要求「什么都不说，让他自己去做」——但**必须同步给框架**（skill + MKS铁律清单）。v1 失败：2000turns零框架→文章dump被驳回。v2 修正：先装skill再给铁律，CC在框架内自由创作。详见 `references/full-autonomy-mode.md`。
>
> **🆕 创意模式库已沉淀为独立交互网页**：`references/creative-patterns.html`（线上：https://andy-zokelink.github.io/mks-knowledge/references/creative-patterns.html）——8章节深色档案馆主题，左侧固定TOC导航，覆盖隐喻矩阵/叙事模式/交互工具箱/视觉规范/设计反模式/速查表。后续 Claude Code 做创意设计前应直接加载此页面作为参考。\n\n### 视觉重设计\n\n0. 🔴 **禁止卡片罗列（2026-06-13 Andy 反复强调）**：重设计后的页面如果本质上是「各种卡片的罗列」（品类卡片、文章卡片、翻转卡片）——即使换了颜色和排列方式，也是不合格的。**每个专题必须至少有 4-6 种完全不同的内容呈现方式**（时间线、剖面图、对比巨幕、闯关游戏、地图标注、分层折叠、手风琴、3D翻转、决策树等），让页面有节奏变化，读者不会审美疲劳。科技参考3反面案例：演化v1（8卡片罗列）、消费品v1-v2（X光卡片→拆解卡片，换汤不换药）。正面案例：演化v2（时间轴+迁徙地图+DNA可视化+翻转卡+对比面板+分层折叠+手风琴，8种布局交替）、消费品v4-v5（营销vs真相巨幕+成本剖面+智商税闯关+参数训练营+避坑地图+情报档案，6 Mission）。

1. **放权比规范更出质量**：第一轮按 spec 做（蓝图桌/药剂师典籍/侦探墙等），产出中规中矩。第二轮完全不限方向、只说「从内容出发、自由创作」——Claude Code 自主产出远更有想象力的作品（清识实验室、星河学案、意识深潜、理性的锻造厂）。教训：设计师 brief 写愿景不写规范。\n\n2. **重设计必查三大回归**：翻转卡片失效（WebKit 前缀 + 父容器高度塌陷）、题库随机抽题数变为 5（slice(0,5) 未改）、思维导图裸 `<li>` 放 `<div>` 内。每个模块重设计后立即逐项检查，不要等用户反馈。

3. 🔴 **首页视觉图必须有叙事弧线，禁止教科书式图解（2026-06-13 科技参考3五版迭代教训）**：「分光镜/三棱镜折射」是物理课教具，不是科技前沿的视觉语言。首页 hero 视觉图的核心职责是让人停留3秒产生好奇心——不是解释7个领域是什么。**禁止**：棱镜、三棱镜、透镜折射、教科书式图解。**推荐**：信号阵列、射电望远镜、神经网络拓扑、全息投影、科技树根系等有叙事纵深的隐喻。科技参考3反面案例：分光镜 v1-v3（棱镜折射，5次尝试全部未通过）。正面案例：深空信号阵列v5（7面天线碟→八面体处理核心→7道信号光束→底部接收节点，五阶段视觉叙事）。**更进一步的正面案例：EUV光刻机光学系统 v6**——顶部等离子体光源(13.5nm)→3面Bragg反射镜(Mo/Si镀层)→7束彩色曝光光束→7个晶圆曝光端点。光刻机是半导体工业的核心，与7个科技领域的主题高度契合——不再是通用隐喻，而是**内容领域自身的视觉语言**。Andy 明确指示「画成光刻机的，用光源折射这种意象」——领域特有隐喻优于通用科技隐喻。**若首页图为独立 Claude Code 任务，必须给「不要XXX」的负面约束**——只写愿景不够，CC 在无约束下容易滑向教科书式图解。**负向约束优先于正向愿景**：「不要棱镜/三棱镜/分光镜折射」比「创造一个科技感视觉图」更有效。标准模式：愿景句子 + 「不要X，不要Y」+ 可选方向建议（「可以是A/B/C或自创更好」）。只给愿景→CC滑向安全但平庸的模板方案；只给约束→CC僵化。两者结合产出最高。\n\n3. **标杆驱动**：演化论页面质量天然高于其他模块。不要对标杆做无谓重做——用标杆对齐其他模块，而非全线推翻。\n\n### 批处理策略\n\n4. **并行 Claude Code 对独立任务是最高效模式**：6 个模块的翻转/题库/导图修复同时 `background=true` 启动，5/6 成功。但重型任务（5+ 子任务）必须拆分——识破伪科学单次 60 turns 两次超时，拆成机械修复(30t)+创意重绘(40t) 后均成功。🔴 **创意图表/创意图重生成 turn 预算**：6 个文件的创意图差异化 + 概念关系图复核，CC 用 40t 只能完成表层修改（如 hero 精简），创意 SVG 重生成需要 50-60t 才能深入。按文件数估算：每 2 个文件的创意重绘至少需要 20t。🔴 **创意图表/创意图重生成 turn 预算**：6 个文件的创意图差异化 + 概念关系图复核，CC 用 40t 只能完成表层修改（如 hero 精简），创意 SVG 重生成需要 50-60t 才能深入。按文件数估算：每 2 个文件的创意重绘至少需要 20t。\n\n5. **delegate_task 只适合轻量分析，不适合 MKS HTML 生成/修复**：子 agent 同步依附父会话，用户发消息即中断。MKS 重体力活一律用 `terminal(background=true) + claude -p`。

6. **多文件MKS修复 turn 预算指南（2026-06-13 验证于卓克6文件项目）**：
   - 机械修复（命名/Unicode/CSS替换/按钮标准化）：40t 可覆盖 6-8 文件，通常达到 max-turns 但有完整改动未 commit
   - 创意修复（创意图重生成/概念图重绘）：50-60t 才能深入 6 文件，CC 常 report exit 0 但叙事与文件不完全一致
   - CC 达到 max-turns 后三步验证：`git log` → `git status` → `git diff --stat` → 有效改动则手动 commit+push 后继续
   - 不要因为 CC 多次 max-turns 就放弃——commit 已有成果，缩小范围重派，逐步收敛\n\n6. **exit code 1 ≠ 零进度**：Claude Code 达到 max-turns 退出时可能已完成 commit+push。先 `git log --oneline -3` 确认，不要盲目重跑。**同时必须 `git status --short` 检查脏文件**——CC 超时也可能已完成修改但未 commit（症状：本地有修复代码但 GitHub Pages 仍为旧版，2026-06-12 模5实况：85 insertions/92 deletions 但未 push，浏览器 `renderAllQuiz` undefined）。\n\n### 播客制作\n\n7. **Coze CLI stdin 陷阱已模式化**：bash for 循环中 `--stdin` 首次调用消费全部 stdin，后续卡死。每次必须独立 `< /tmp/file`。\n8. **Coze output-path 是目录不是文件**：`--output-path /tmp/foo` 创建 `/tmp/foo/audio_<ts>_0.mp3`，需 `find + cp`。\n9. **播客脚本让 Claude Code 用自己的话讲**：说「用自己的理解、例子、节奏」产出远好于「总结以下内容」。EP2（演化论）和 EP5（文明史）因这个指令质量明显高于其他期。\n\n### 质量闭环\n\n10. **Claude Code 自检自修才是终极质量闸**：让 CC 自己打开浏览器、测试翻转/题库/导图、发现问题、修复、再测——比我逐模块手动检查更彻底（模块 6 发现了我肉眼遗漏的 overflow 边界问题）。Hermes 只需最后抽查。\n\n11. **browser_snapshot 的无障碍树会显示 backface-visibility:hidden 的背面**：不能用 snapshot 判断翻转是否正常——必须用 `browser_vision` 或 `browser_console` 查 computed style。

### 翻转卡片 & 题库修复（2026-06-12 批量修复）

12. **翻转卡片三大常见根因**：缺 WebKit 前缀→Safari翻转无效；`.flip-card` 用 `position:relative` 而非 `display:grid`→前后不重叠；无 `onclick` 在卡片本体→点击无反应。**标准参照**：diff `/home/admin/mks-knowledge/硅谷来信1_v2/4-历史与文明对话.html` 对照修复。

13. **题库只出 1 题根因**：`renderQuizQuestion()` 逐题渲染。修复：重写为一次渲染全部 N 题，每题独立判分。

14. **翻转背面截断**：需要 `min-height:340px` + `overflow-y:auto` + `word-break:break-word` 三件套。

15. **返回首页死法**：`href="#"`→`href="index.html"`；缺 home-link→`.top-bar` 内加 `<a href="index.html" class="home-btn">← 返回课程首页</a>`。

### 🔴 Meta-MKS 专用陷阱

- 🔴 **多层拆分后 Footer 链接未更新**：当一个大层拆分为 A/B 两个子页面时，所有页面的 footer 和交叉链接中原有的单页引用必须逐一替换。Claude Code 生成各个子页面时不知道彼此的存在——footer 里可能残留指向不存在页面的链接。修复：所有页面生成完成后，`grep -rn '旧链接' 项目目录/` 逐处替换。不要用 sed 全局替换（每处上下文不同）。
- 🔴 **Meta-MKS ≠ 标准 MKS 模板**：跨课思想体系重组不强制使用 9 标签标准 MKS 模板。Claude Code 应被鼓励创造自由格式（时间轴、仪表盘、双塔对照、双螺旋动画等）。Brief 中写「不强制用标准 MKS 模板」。
- 🔴 **「放权」Brief 模式**：用户明确要求放权时，brief 写愿景而非规范——给项目定位、核心洞察素材、设计方向建议（可推翻），验收闸门 3-5 条硬性条件。经验：说「用时间线模式」产出平庸，说「创造独特视觉隐喻，呼应课程精神」产出 Thought OS 级别作品。详见 `references/meta-mks-wujun-case-study.md`。

### CC 超时后三步验证（2026-06-12 实战教训）

CC 达到 max-turns 退出时文件可能处于三种状态：①已完成 commit+push（`git log` 可见）②已修改未 commit（`git status` 有脏文件）③零进度。**不要假设 exit code 1 = 失败**。标准验证：

```bash
# 三步验证
git log --oneline -3                    # ① 有没有新 commit
git status --short                      # ② 有没有脏文件未 commit
git diff --stat 2>/dev/null || true     # ③ 改动量多大
```

若状态 ② 且有有效改动 → 手动 `git add && git commit && git push`。跳过此步会导致浏览器中测试旧版（未 push）→ 反复误诊「修复失败」→ 浪费时间。2026-06-12 实况：模5 CC 25 turns 超时，本地 85+/92- 修复完成但未 commit，浏览器 `renderAllQuiz` undefined，反复调试才发现是未 push。

### 诊断：零代码 vs 代码损坏
- 🔴 **'不能点击/样式不对'先区分两类根因**：用户反馈卡片问题时，先 `grep -c 'flip' <file>`。若返回 0，说明文件是**旧模板（无翻转功能）**，不是 bug 而是功能缺失——需完整重生成（场景 D）。若有 flip 匹配但功能异常，才是代码损坏——可用 patch 修复。错判会导致反复修 CSS 但始终无效（本次对话已踩）。
  - 零代码特征：完全无 `.flip-card`/`.flip-inner` CSS、无 `setCard`/`markLearned` JS、无 `flipInner`/`flipFront`/`flipBack` HTML ID
  - 代码损坏特征：有 flip 相关代码但 JS 报错（如 `renderCard is not defined`）、class 名不匹配（如 CSS `.flip-inner` vs HTML `.flipper`）、inline onclick 与 addEventListener 冲突
  - 修复策略：零代码 → 克劳德代码按场景 D 全面重生成；代码损坏 → 克劳德代码按具体报错定点修复

### JS 代码
1. **绝对禁止中文弯引号**：JS 中 `"` `"` `'` `'` → 全部 ASCII 直引号
2. 🔴 **JS 字符串内中文书名号必须用 `\u201C`/`\u201D`，禁止 ASCII `"`**：当 JS 字符串以 `"` 分隔时，内部的中文书名号（如 `"遵循自然"`、`"卡塔西斯"`）若使用 ASCII `"`（0x22），会导致 JS 解析器将其误判为字符串结束符，字符串提前闭合、后续中文变成 identifier → `SyntaxError: Unexpected identifier`。**正确写法**：`short: "卢梭\u201C遵循自然\u201D——教育以天性为基准"`。\n   - **验证命令**：`node -e "const fs=require('fs');const c=fs.readFileSync('<file>','utf8');const m=c.match(/<script>([\\s\\S]*?)<\\/script>/);new Function(m[1]);console.log('OK')"`\n   - **反例（致命）**：`short: "卢梭"遵循自然"——教育以天性为基准"` → JS 解析为 `short: "卢梭"`(字符串结束)+`遵循自然`(identifier!) → 崩溃\n   - **为什么 `\"` 也没用**：HTML `<script>` 中 `\"` 是 JS 转义序列，产生 ASCII `"`(0x22)——与 `\u201C`(U+201C, 0xe2 0x80 0x9c) 是不同的 Unicode 码点。`\"` 在 `"` 分隔的字符串内会闭合字符串，`\u201C` 不会\n   - **自动修复的陷阱**：正则/AST/迭代修复脚本对此类 bug 效果极差——6轮不同方案均失败。中文引号与 JS 字符串分隔符的歧义无法用脚本可靠消解。**唯一可靠方案：Claude Code 全量重生成，prompt 中写死 \u201C/\u201D 规则**
**唯一可靠方案：Claude Code 全量重生成，prompt 中写死 \u201C/\u201D 规则**\n   - 🔴 **修复脚本反噬**：逐字符替换会误将 JS 分隔符也替换为 \u201C/\u201D，导致 `{ name: "概念", scene: "场景" }` 变为 `{ name: "概念\u201C, scene: \u201D场景" }`——name 吞并全部后续属性。症状：scene 全部 undefined、概念标题泄露 JSON 字符。修复：定点替换 `\u201C, ` → `", ` 和 `: \u201D` → `: "`，保留不邻接 `, ` 或 `: ` 的真实中文引号\n3. **单引号转义**：中文内容含单引号必须转义\n4. **禁止 `transform: none !important`**：破坏 3D 翻转\n5. 🔴 **超宽 SVG 只需 CSS 自适应，不必重画布局**：当 SVG viewBox 宽度超过 900px（如 2242×260、1405×248），用 `.svg-wrap svg { max-width: 100%; height: auto; }` 替换 `min-width: 700px` 即可让 SVG 等比缩放适应容器——比让 Claude Code 重画 SVG 布局快 100 倍（重画经常卡死数小时）。同时确保 `.flip-front,.flip-back { backface-visibility: hidden; -webkit-backface-visibility: hidden; }` 双前缀覆盖 Safari。此模式已验证于 5号(2242px) 和 6号(1405px) 的修复
4. **禁止 `transform: none !important`**：破坏 3D 翻转

### MCQ
4. **答案用 `"answer":`**，不用 `"ans":`（校验器不认）
5. **分布写死数字**：`{0:5,1:5,2:5,3:5}`，不说"均匀分布"

### SVG 概念图
6. **根节点从内容提取**：严禁套用预设框架
7. **图例颜色也从对象命名**：不套用"能量线/信息线"

### 版本命名
8. **用 `_YYYYMMDD`**，不用 v1/v2

### Claude Code 调度
9.  `-p` 模式必带 `--dangerously-skip-permissions`
10. 笔记素材在 `/home/admin/`，不在 `/tmp`
11. ⚠️ **DeepSeek 下 Claude Code 成功率约 50%**：失败重跑即可
- 🔴 **长 prompt 用文件传参避免 shell 转义**：当 prompt 含 `\u201C`、`"`、`'` 等特殊字符时，shell 内联引号可能被 bash 误解析导致 Claude Code 收到损坏的 prompt（症状：`bash: script` 报错、`No such file or directory`）。标准做法：先 `write_file` 写入 `/tmp/claude_prompt_N.txt`，再用 `claude -p "$(cat /tmp/claude_prompt_N.txt)"` 启动。已验证：第一次 inline prompt 两个进程均 exit 1，改用文件传参后成功。，不要因为一次失败就换方案。用 `background=true + notify_on_complete=true` 启动，完成后逐个验证。
- 🔴 **Claude Code plan mode 卡死**：复杂 prompt（概念卡片重写、60KB+ HTML 生成等）即使带 `--dangerously-skip-permissions` 也可能进入 plan mode，等待交互式批准后卡死。症状：进程运行 2-3 分钟零输出、目标文件未修改。**修复①（首选）**：prompt 开头加「EXECUTE IMMEDIATELY — DO NOT ENTER PLAN MODE. DO NOT ASK FOR REVIEW. DO NOT OUTPUT A PLAN. DIRECTLY CREATE THE FILE.」——已验证此模式可将 plan mode 发生率从 ~60% 降至接近 0。**修复②**：改用 `delegate_task` 派发子 agent 执行（实测 ~5.5 分钟完成生成+验证），或加 `--permission-mode acceptEdits` 标志。**修复③**：若两轮 `-p` 均卡 plan mode，换用 `terminal(pty=true)` 模式启动 CC 交互式会话。不要反复重试同样的 `-p` 命令——三次相同结果说明 prompt 触发模式本身需要改变，不是运气问题。**🔴 特例：index.html SVG 修改极易触发 plan mode**——CC 面对大型 HTML 文件（140KB+）中的 SVG 替换任务时，plan mode 发生率接近 100%，即使加了「EXECUTE IMMEDIATELY」前缀也常静默退出（exit 0，零文件改动）。科技参考3 实况：index SVG 重设计 5 次 `-p` 全部静默失败。**备用策略**：① 让 CC 生成独立 SVG 文件（`hero.svg`），Hermes 手动集成；② 用 `delegate_task` 派发（子 agent 上下文隔离，plan mode 发生率较低）；③ 降低 max-turns 到 500-800（1000 turns 给 CC 过多「规划空间」反而触发 plan mode）。

- 🔴 **「计划≠执行」——出了 TODO 但没派活（2026-06-11 断层教训）**：在多课并行的 MKS 项目中，Hermes 探查数据库→出实施计划→建 TODO→说「现在启动」——然后会话结束，TODO 全部 pending，Claude Code 进程一个都没启动。根源是把「列出计划」当成了「执行计划」。**规则**：当用户说「后面就交给你了」且任务包含 MKS 生成，最后一轮响应必须**实际调用** `terminal(background=true, notify_on_complete=true)` 启动 Claude Code 进程——不能只输出计划文本和 TODO 列表。**自检**：回应中含「现在启动」但没有实际 terminal/background 调用 → 立刻补上。

- 🔴 **课程正文 MKS ≠ 笔记 MKS（2026-06-11 重大教训）**：从 SQLite content_text 生成 MKS 与从 Get 笔记生成完全不同。课程正文结构完整、语义连贯，**不需要 Hermes 先分类**，而是要求 Claude Code **逐篇通读全部文章**后提炼概念骨架。关键差异：① 必须全文通读（不抽样），分批 20 篇读；② 必须 effort=max（概念抽象需要深度思考）；③ 必须一门一课（并行导致概念浅层化）；④ 概念必须是可迁移思维工具（不是话题标签）。违例：V1 三课并行 + 只抽样 30% + 默认 effort → 产出的是文章摘要目录而非 MKS 概念骨架，被用户当场驳回。**正确做法**：见 `references/course-mks-prompt-template.md`。

- 🔴 **并行 Claude Code 导致 MKS 质量断崖式下降（2026-06-11 验证）**：三门课并行生成 → 每门概念抽象浅、MCQ 泛化、案例不达标。改为逐门生成（一门验收通过再下一门）→ 质量显著提升。**规则**：课程正文 MKS 禁止并行，必须一门一课。笔记 MKS 可分专题并行（不同专题内容独立），但课程 MKS 的概念抽象深度与专注度正相关。

---

## 专题页面统一规范（所有专题必须遵守）

以下规范适用于所有 MKS 专题 HTML。每个专题是独立 HTML 文件，通过 GitHub Pages 的分页面链接导航。

### 导航标签（9 个，固定顺序）

```
知识集总览 → 知识卡片 → 题库系统 → 考试模式 → 复习模式 → 思维导图 → 深度追问 → 案例分析 → 实战决策
```

⚠️ **选择题 + 简答题合并为「题库系统」**——不能分成两个独立 tab。题库系统内可分段展示选择题区和简答题区。

⚠️ **旧标签对照**（生成时严禁出现）：
- `概念卡片` → `知识卡片`
- `选择题` / `简答题` → 合并入 `题库系统`
- `关系图` → 归入 `知识集总览`（概念关系图）
- `苏格拉底问答` → `深度追问`
- `决策模拟` → `实战决策`
- 禁止 emoji 前缀（如 `📋 总览`）→ 用纯文字

### 知识集总览内容（7 个子项，固定顺序）

```
核心目标 → 核心概念 → 最小知识集（骨架中的骨架）→ 概念关系图 → 边界知识表 → 学习路径 → 学习进度
```

每个子项内容要求：
- **核心目标**：一句话概括本专题要解决的问题
- **核心概念**：5-10 个，编号列表，每个一行解释
- **最小知识集**：从核心概念中精选 3-5 个，标注 ★，说明为何是「骨架中的骨架」
- **概念关系图**：用 Graphviz dot 生成 SVG（禁止手写坐标），根节点从专题内容提取。dot 文件描述节点边 → `dot -Tsvg` 渲染 → 嵌入 HTML。

  **🔴 概念关系图质量红线（用户明确禁止交付简单图）**：
  1. **至少 3-4 层深度**——不是扁平圆圈+箭头，要有核心概念→子概念→关联→结果的层次
  2. **每条边带中文关系标签**（如「驱动」「派生」「应用于」「反馈至」）
  3. **颜色编码区分概念类别**：≥3 种颜色，根节点/二级/叶子/结果各不同
  4. **右上角必须带图例**，标注颜色含义
  5. **交叉连接用虚线弱化**，`constraint=false` 不参与层级计算
  6. **`.svg-wrap{overflow-x:auto}`** 防溢出，`max-width:100%;height:auto` 响应式缩放
  7. **形状编码区分节点类型**：`box`（概念节点）、`note`（结果/产物）、`ellipse`（外部因素）
  8. **回环反馈线**用 `style=dashed` 表现闭环

  专业交付标准：分层着色+形状编码+关系标签+图例+回环，四者缺一不可。扁平圆圈箭头=不合格，直接打回重做。

  排版规范见 `references/graphviz-concept-diagram.md`。若图太简单被拒，用 `references/concept-diagram-redraw-spec.md`。
- **边界知识表**：10-15 条，格式「知识点 → 使用场景」
- **学习路径**：3-5 阶段递进学习计划
- **学习进度**：localStorage 驱动的进度条

### 配色方案（暖色系，禁止偏离）

**CSS 类名以 `1-世界名校.html` 为金标准**——所有 MKS 专题 HTML 的 CSS class 命名必须与此文件一致。生成新专题时，直接复制世界名校的 `<style>` 块作为起点，不要发明新类名。

```css
--bg: #fdf6ec;
--card: #fffaf2;
--accent: #c0392b;
--text: #3d2f2f;
--text-light: #6b5b5b;
--border: #e8d5c4;
```

---

## 交付规范

每个 MKS 项目产出独立 HTML 文件，部署到 GitHub Pages：

```
项目文件夹/
├── index.html              ← 知识主板首页（链接到各专题）
├── 1-专题名.html
├── 2-专题名.html
└── ...
```

- 首页通过 `<a href="1-专题名.html">` 链接到各分页
- 每个分页有「返回首页」链接
- 文件名不含日期后缀（网站引用需要稳定路径）
- 响应式设计：桌面端宽屏布局 + 移动端汉堡菜单，手机/电脑浏览器均可直接访问
- 移动端适配要点：375px 全功能、翻转卡片高度≤视口60%、翻页按钮不遮挡、思维导图 `overflow-x: auto`、按钮触控目标 ≥ 44px

---

## 发布与预览（GitHub Pages + 飞书云盘）

三阶段工作流：

```
改代码 → git push → GitHub Pages 预览（主力）→ 验收通过 → 飞书云盘存档
```

### GitHub Pages（官方网站 + 预览平台）

仓库 `andy-zokelink/mks-knowledge` 已启用 GitHub Pages，官方入口：

> **https://andy-zokelink.github.io/mks-knowledge/**

- 根目录 `index.html` 是导航页，所有 MKS 项目在此汇总
- 新增项目只需在导航页加一行链接
- 零成本、自带 CDN、无需额外搭服务器
- 手机和电脑浏览器均可直接访问，无需下载文件

### 仓库结构

```
/home/admin/mks-knowledge/
├── index.html              ← GitHub Pages 导航页（汇总所有项目）
├── 硅谷来信1_v2/
│   ├── index.html
│   ├── 1-科技与商业逻辑.html
│   └── ...
├── 谷歌方法论/
│   ├── index.html
│   ├── 1-世界名校.html
│   └── ...
└── 硅谷来信3/
    ├── index.html
    ├── 1-哲学与思想.html
    └── ...
```

### 提交流程

```bash
cd /home/admin/mks-knowledge

# 新增或修改文件
cp /path/to/new_file.html 项目名/

# 提交并推送
git add -A
git commit -m "项目名: 简短描述"
git push origin main
```

- credential 已存 `~/.git-credentials`，无需每次认证
- 文件名包含中文不转义（git 默认 UTF-8）
- **推送后 GitHub Pages 自动部署**，约 1-2 分钟后刷新即可预览

### 飞书云盘（最终版存档）

GitHub Pages 验收通过后，推送终版到飞书云盘：

```bash
cd /path/to/project_dir
lark-cli drive +push --folder-token <项目文件夹token> --local-dir . --as user
```

飞书云盘仅存档，日常预览走 GitHub Pages。

### Git 认证

- **优先用 PAT**：`gh auth login --with-token` 比 device flow 更可靠（device flow 在此服务器常 OAuth 超时）
- token 已存 `~/.git-credentials`，格式 `https://user:token@github.com`
- 若 `gh auth status` 显示未登录，直接用 git+token 操作，跳过 gh CLI

---

## 🔴 交付前自查清单（逐条验证，缺一不可）

两个大项目（谷歌方法论 + 硅谷来信1）踩坑全集。生成 HTML 后逐条检查。

> **详细样式规范**：https://andy-zokelink.github.io/mks-knowledge/STYLE_SPEC.md — 14 章全覆盖：全局基础、页头、标签导航、知识总览 7 子项、概念卡片、翻转卡片（含尺寸/动画/验收）、题库系统、考试模式、深度追问、实战决策、按钮系统、知识主板 PCB、14 项禁止项、14 项验收清单。生成前先读取全文做自检。

### 代码质量
- [ ] **JS 无中文弯引号** `\u201c\u201d`
- [ ] **JS 无语法错误**（提取 `<script>` 块后用 `node -e "new Function(...)"` 检查——Node v24 不支持 `node --check file.html`）
- [ ] **禁止 `transform: none !important`**
- [ ] **全局变量放脚本最前面**（避 TDZ）

### MCQ
- [ ] **答案 {0:5,1:5,2:5,3:5}** 代码验证
- [ ] **格式 `"answer":`** 不用 `"ans":`
- [ ] **选项可点击** `.quiz-option` 有事件绑定

### 知识卡片（统一设计规范）

卡片采用 3D 翻转，正面仅显示概念名，背面显示详细内容。按钮在卡片下方。

**标准模板以 `4-历史与文明对话.html` 为金标准**（不是 1-科技与商业逻辑）。以下 CSS/HTML/JS 为强制规范，所有专题必须对齐。

**CSS（固定，不可偏离）**：
```css
.flip-card{perspective:1000px;height:clamp(220px,40vw,280px);margin-bottom:14px}
.flip-inner{position:relative;width:100%;height:100%;transition:transform .6s;transform-style:preserve-3d;cursor:pointer}
.flip-inner.flipped{transform:rotateY(180deg)}
.flip-front,.flip-back{position:absolute;width:100%;height:100%;backface-visibility:hidden;border-radius:10px;padding:clamp(16px,3vw,24px);display:flex;flex-direction:column;justify-content:center;align-items:center;text-align:center;box-shadow:var(--shadow);border:1px solid var(--border)}
.flip-front{background:var(--card)}
.flip-back{background:linear-gradient(135deg,#fffaf2,#fef5e7);transform:rotateY(180deg);overflow-y:auto;align-items:flex-start;text-align:left;font-size:clamp(.8rem,1.6vw,.9rem)}
.flip-nav{display:flex;justify-content:center;align-items:center;gap:16px;margin:12px 0}
.flip-nav .btn{min-width:80px}
```

尺寸用 `clamp()` 响应式——桌面端约 280px，移动端自动适配。**禁止**使用 `min-height`、`position: relative`、`width: 280px` 等固定值。

**HTML（固定结构）**：
```html
<div class="flip-card"><div class="flip-inner" id="flipInner"><div class="flip-front" id="flipFront"></div><div class="flip-back" id="flipBack"></div></div></div>
<div class="flip-nav">
  <button class="btn btn-outline btn-sm" onclick="prevCard()">← 上一张</button>
  <span style="font-size:.85rem;color:var(--text-light)" id="cardIndex">1 / 10</span>
  <button class="btn btn-outline btn-sm" onclick="nextCard()">下一张 →</button>
</div>
<div style="text-align:center;margin:8px 0">
  <button class="btn btn-success btn-sm" onclick="markLearned()" id="markLearnedBtn">✓ 标记已学习</button>
</div>
<div class="progress-bar"><div class="progress-fill" id="cardProgress" style="width:0%"></div></div>
<p style="text-align:center;font-size:.78rem;color:var(--text-light)">已学习 <span id="cardLearned">0</span> / 10 个概念</p>
```

**JS（固定函数名和 ID 引用）**：
```js
let currentCardIdx = 0;
let cardFlipped = false;

function setCard(idx){
  currentCardIdx=idx;
  cardFlipped=false;
  const c=concepts[idx];
  document.getElementById('flipFront').innerHTML=`<h3 style="font-size:clamp(1.1rem,2.5vw,1.5rem);color:var(--accent)">${c.name}</h3><p style="color:var(--text-light);margin-top:8px;font-size:clamp(.85rem,1.8vw,.95rem)">点击翻转卡片查看详情</p>${learnedCards.includes(c.id)?'<span class="tag" style="margin-top:8px">✓ 已学习</span>':''}`;
  document.getElementById('flipBack').innerHTML=`<div style="margin-bottom:6px"><strong style="color:var(--accent)">定义：</strong>${c.def}</div><div style="margin-bottom:6px"><strong style="color:var(--accent)">类比（商业场景）：</strong>${c.analogy}</div><div style="margin-bottom:6px"><strong style="color:var(--success)">示例：</strong>${c.example}</div><div><strong style="color:var(--accent)">反例/边界：</strong>${c.counter}</div>`;
  document.getElementById('flipInner').classList.remove('flipped');
  document.getElementById('cardIndex').textContent=`${idx+1} / ${concepts.length}`;
  document.getElementById('markLearnedBtn').textContent=learnedCards.includes(c.id)?'✓ 已学习':'✓ 标记已学习';
}
setCard(0);

function prevCard(){if(currentCardIdx>0)setCard(currentCardIdx-1)}
function nextCard(){if(currentCardIdx<concepts.length-1)setCard(currentCardIdx+1)}
function markLearned(){
  const id=concepts[currentCardIdx].id;
  if(learnedCards.includes(id)){learnedCards=learnedCards.filter(x=>x!==id)}else{learnedCards.push(id)}
  save();updateProgress();setCard(currentCardIdx);
}
document.getElementById('flipInner').addEventListener('click',function(){this.classList.toggle('flipped');cardFlipped=!cardFlipped});
```

**关键约束**：
- 翻转只通过 `.flip-inner` 的 click 事件（`addEventListener`），**不要**添加独立的翻转按钮或 `onclick="flipCard()"`
- **不要同时存在 inline `onclick` 和 `addEventListener`**——两者会互相取消，导致翻转失效
- 函数名为 `setCard`（不是 `renderCard`、`renderFlipCard`）
- ID 引用：`flipInner`（不是 `flipper`、`flipCard`）、`cardIndex`（不是 `cardIdx`、`flipCounter`）、`markLearnedBtn`（不是 `learnedBtn`、`learnBtn`）
- 概念数组名为 `concepts`，每个条目必须有 `id:'cN'`、`name`、`def`、`analogy`、`example`、`counter` 字段

**验收清单**：
- [ ] CSS 用 `.flip-card`/`.flip-inner`，不是 `.flip-container`/`.flipper`
- [ ] 无 `min-height`（全部用显式 `height` 或 `height:100%`）
- [ ] 无 `position: relative`（全部用 `absolute`）
- [ ] `backface-visibility: hidden` 存在
- [ ] `.flip-back` 有 `transform: rotateY(180deg)`
- [ ] JS 用 `setCard()` 函数，`setCard(0)` 在 init 时调用
- [ ] 翻转事件仅通过 `.flip-inner` 的 `addEventListener('click')`，无冗余 inline `onclick`
- [ ] DOM ID：`flipInner`、`flipFront`、`flipBack`、`cardIndex`、`markLearnedBtn`、`cardProgress`、`cardLearned`
- [ ] 概念数组有 `id` 字段（`c1`-`c10`）

### 思维导图
- [ ] **CSS 兼容 `.show` 和 `.open`**
- [ ] **内容为内联 HTML**：tab5-8 禁止用 JS 动态生成（`innerHTML = buildTree(...)` 等模式），必须参照 5号用内联嵌套 div 树。验收时浏览器逐 tab 截图。

### SVG
- [ ] **根节点从内容提取**，严禁套预设框架
- [ ] **图例从对象本身命名**
- [ ] **SVG 非空**：grep `<path\\|<polygon\\|<rect\\|<circle\\|<text` 有匹配（不能只有空 `<svg></svg>`）
- [ ] **JS 填充容器非空**：若用 `<div id="svgMap">` + JS 动态渲染，确认 JS 中有对应的 `renderSVG()` 或 `innerHTML` 赋值
- [ ] 🔴 **概念图质量达到专业标准**：≥3 层深度、边有中文标签、≥3 种颜色编码、右上角有图例、有回环虚线——不是扁平圆圈加箭头

### 导航条
- [ ] **`.top-bar` sticky 导航条**：所有专题页必须使用统一的 `.top-bar` 结构（position:sticky + 深色背景 + 标题左 + "← 知识主板"链接右），黄金模板为 `1-科技与商业逻辑.html`
- [ ] **禁止裸放 home-link**：`.home-link` 必须在 `.top-bar` 容器内，不在 `<header>` 或裸 `<body>` 中
- [ ] **`href="index.html"`**，不是 `href="#"` 或 `onclick` 拦截

### 导航
- [ ] **标准 9 标签**：知识集总览 → 知识卡片 → 题库系统 → 考试模式 → 复习模式 → 思维导图 → 深度追问 → 案例分析 → 实战决策
- [ ] **无旧标签**：无「概念卡片」「选择题」「简答题」「关系图」「苏格拉底问答」「决策模拟」等旧格式
- [ ] **无 emoji 前缀**：标签纯文字，不用 📋🃏📝 等前缀
- [ ] **`.subtab-btn:not(.home-btn)`**
- [ ] **所有标签切换无空白**
- [ ] **标签数 = 内容区数**
- [ ] **返回链接 `href="index.html"`**，文本「← 返回知识主板」— 禁止 `href="#"` 和 `onclick` 拦截

### 链接审计（批量）

当多个页面出现返回链接失效时，逐页扫描 `home-link` 上下文中 `href="#"` 和 `onclick=` 残留：

```python
import os, re
for p in pages:
    with open(p) as f: content = f.read()
    home = re.search(r'<a[^>]*home-link[^>]*>.*?</a>', content)
    has_hash = 'href="#"' in home.group() if home else False
    has_onclick = 'onclick=' in home.group() if home else False
    print(f"{p}: {'BAD' if has_hash or has_onclick else 'OK'}")
```

三种常见死链模板 → 统一替换为 `<a href="index.html" class="home-link">← 返回知识主板</a>`：
- `href="#" onclick="alert(...);return false"`
- `href="#" onclick="switchTab(0);return false"`
- `href="#" id="backHome"`

### CSS class 审计（批量）

当页面风格不一致时，对比目标页与黄金模板的 CSS 规则差异。**黄金模板为 `4-历史与文明对话.html`**（卡片翻转标准）和 `1-世界名校.html`（CSS class 命名标准）。

```python
# 提取两个文件的 CSS class 集合 → 求差集
mj_rules = extract_css("1-世界名校.html")  # 黄金模板（命名标准）
card_rules = extract_css("4-历史与文明对话.html")  # 黄金模板（卡片标准）
target_rules = extract_css("N-目标专题.html")
missing = mj_rules - target_rules  # 缺的规则
extra = target_rules - mj_rules    # 多出的旧命名
```

修复方向：目标页 CSS 全量替换为黄金模板 CSS，再逐 class 映射对齐 HTML/JS 引用。详见 `references/css-class-mapping.md`。

### 知识集总览
- [ ] **7 子项齐全**：核心目标、核心概念、最小知识集（骨架中的骨架）、概念关系图、边界知识表、学习路径、学习进度
- **选择题+简答题合并入「题库系统」**，不独立成 tab。题库系统不得包含论述题、判断题、填空题、配对题等其他题型。

### 题库系统
- [ ] **仅含选择题+简答题**，无论述题/判断题/填空题/配对题
- [ ] **选择题和简答题在同一 tab 内**，分两个区域展示
- [ ] **选择题 15-20 道，简答题 8-12 道**
- [ ] **「🎲 随机抽题练习」+「重置」两个按钮**：在题库 Tab 顶部、题目前方，与黄金模板 `7-职场与人生智慧.html` 对齐。`startQuiz()` 从全量题库中随机抽取 10 选择 + 3 简答，`resetQuiz()` 清空状态。必须适配每个文件的数据结构（选择题数组名、答案字段名、选项字段名各不相同）
- [ ] 🔴 **旧题库内容用 `#staticQuiz` 包装**：6 个文件中除黄金模板外，题库 Tab 内通常有两套内容并存：新的随机抽题区（`quizContainer`）+ 旧的静态题库区（`mcqContainer`/`saqContainer` 或其他命名）。必须用 `<div id="staticQuiz" style="display:block">` 包裹旧题库区，并在 `startQuiz()` 中添加 `document.getElementById('staticQuiz').style.display = 'none'`、在 `resetQuiz()` 中添加 `.display = 'block'`。否则点「随机抽题」后旧内容仍在下面显示，点「重置」只清 `quizContainer` 旧内容纹丝不动——看起来像重置失效
- [ ] **`startQuiz()` 渲染函数中区分选择题/简答题**：只用 `if(q.opts)` 判断（有 opts 字段 = 选择题），**禁止**加 `q.type===undefined` 冗余条件——简答题也没有 type 字段，会导致误入选择题分支、`q.opts.forEach()` 在 undefined 上报错
- [ ] **重置按钮实际行为验证**：浏览器打开题库 Tab → 点「随机抽题」→ 确认旧题库区消失且新题目渲染 → 点「重置」→ 确认 `quizContainer` 清空、计时器归零、旧题库区恢复显示。不能只 grep 检查按钮和函数存在

### 响应式
- [ ] **暖色系统一** #fdf6ec/#fffaf2/#c0392b/#3d2f2f
- [ ] **375px 全部正常**：汉堡菜单、卡片≤60%、按钮≥44px、导图可横滚
- [ ] **导航不挡内容** `body{padding-top:导航栏高度}`

### 持久化
- [ ] **localStorage 正常**

### 文件
- [ ] **版本 `_YYYYMMDD`**，不用 v1/v2
- [ ] **运行 validate_mks_html.js + 弯引号检查 + transform 检查**

```bash
node ~/.hermes/skills/note-taking/mks-builder/scripts/validate_mks_html.js <file>
grep -Pn '[\\x{201c}\\x{201d}]' <file> && echo "❌" || echo "✅"
grep -n 'transform:\\s*none\\s*!important' <file> && echo "❌" || echo "✅"
```

---

## 本地数据库（Get 笔记数据持久化）

⚠️ **核心原则**：从 Get 笔记拉取的数据必须存入本地 SQLite，不要删。后续更新只拉增量，避免全量重复拉取。数据越多全量越慢，本地调取才是正路。

### 基础设施

```bash
数据库: ~/.hermes/data/getnote_kb.db
表名: kb_notes
同步脚本: ~/.hermes/scripts/sync_getnote_kb.py
原始 JSON 备份: ~/.hermes/data/wujun_kb_full.json (5.8MB, 1340条)
历史碎片归档: ~/.hermes/data/getnote_archive/ (18个文件)
```

### 表结构

```sql
CREATE TABLE kb_notes (
    note_id TEXT PRIMARY KEY,   -- Get笔记唯一ID
    kb_name TEXT NOT NULL,      -- 知识库标识（如 'wujun'）
    title TEXT,                 -- 笔记标题（含课程标识，如「| 吴军·硅谷来信³」）
    content TEXT,               -- 笔记正文
    note_type TEXT,             -- 笔记类型
    created_at TEXT,            -- 创建时间
    updated_at TEXT,            -- 更新时间
    tags TEXT,                  -- JSON array
    ref_content TEXT,           -- 引用内容
    source TEXT                 -- 来源
);
```

### 增量同步流程

```bash
# 1. 从 Get 笔记拉取全量（首次或定期）
getnote kb <KB_ID> --all --output json > /tmp/kb_dump.json

# 2. 增量同步到本地 SQLite（INSERT OR IGNORE 去重）
python3 ~/.hermes/scripts/sync_getnote_kb.py --json-file /tmp/kb_dump.json --kb-name wujun

# 3. 全量重建（rare）
python3 ~/.hermes/scripts/sync_getnote_kb.py --json-file /tmp/kb_dump.json --kb-name wujun --full
```

### 🔴 课程识别铁律：标签优先于标题（2026-06-03 定）

**标题匹配不可靠。必须用 tags 做课程分类。**

Get 笔记中的「文件夹」本质是标签系统。吴军知识库中多个课程通过标签区分，标题可能不含课程名（如「教育的方法50讲」的笔记标题是「01｜定位：…」没有任何课程标识）。

**同步到本地库时必须保存完整 tags JSON 数组**，后续按 `tags LIKE '%课程标签%'` 筛选。

错误做法：`WHERE title LIKE '%教育%'` → 会混入硅谷来信3等课程的噪声笔记
正确做法：`WHERE tags LIKE '%教育的方法50讲%'` → 精准命中 64 条

已验证案例：教育的方法50讲——标题匹配检出 182 条（大量噪声），标签筛选精准 64 条。

### 按课程筛选笔记（用于 MKS 生成）

课程通过标题中的 `|` 管道符或 **tags 中的课程标签**识别。示例 SQL：

```sql
-- 硅谷来信3（285条）
SELECT * FROM kb_notes WHERE kb_name='wujun' AND title LIKE '%硅谷来信³%'

-- 硅谷来信1（通过排除3来筛选）
SELECT * FROM kb_notes WHERE kb_name='wujun' 
  AND title LIKE '%硅谷来信%' AND title NOT LIKE '%硅谷来信³%'

-- 谷歌方法论
SELECT * FROM kb_notes WHERE kb_name='wujun' AND title LIKE '%谷歌方法论%'

-- 导出为 JSON 供 Claude Code 使用
python3 -c "
import sqlite3, json
conn = sqlite3.connect('$HOME/.hermes/data/getnote_kb.db')
rows = conn.execute(\"SELECT * FROM kb_notes WHERE kb_name='wujun' AND title LIKE '%硅谷来信³%'\").fetchall()
cols = ['note_id','kb_name','title','content','note_type','created_at','updated_at','tags','ref_content','source']
data = [dict(zip(cols, r)) for r in rows]
with open('/tmp/course_notes.json', 'w') as f:
    json.dump(data, f, ensure_ascii=False)
print(f'Exported {len(data)} notes')
"
```

### 当前数据状态

> 🔴 **课程发现注意**：部分课程笔记标题不含课程名，标签里有。详见 `references/getnote-course-pull.md`。

| 知识库 | 笔记数 | 时间跨度 |
|--------|--------|---------|
| wujun | ~1,382 | 2018-02-01 ~ 2026-05-31 |

课程分布（按标题管道符 + 标签估算）：
- 硅谷来信3：285 条
- 硅谷来信1：~190 条
- 谷歌方法论：~10 条
- 信息论40讲：42 条（2026-05-30 增量同步）
- 阅读与写作50讲：52 条（2026-06-01，标签筛选同步）
- GPT：13 条
- 5G：8 条
- 其他/未分类：~782 条

### 标签驱动MKS（单课程，笔记标题不含课程名）

### 标签驱动MKS（单课程，笔记标题不含课程名）

> **Get 笔记的「文件夹」= 标签**：用户在平台上创建的文件夹本质是标签。发现所有文件夹 → 参考 `references/getnote-discovery.md`「标签即文件夹」。

部分课程笔记标题不含课程名（如阅读与写作50讲标题为 `andy邀请你读：...`，科技史纲部分笔记标题为 `34｜什么人受益于第二次工业革命？`），课程标识在 tags 中。拉取和导出流程：

```bash
# 1. 先查 Get Notes KB 确认标签中含有目标课程
getnote search --kb <KB_ID> '<课程关键词>' -o json | python3 -c "
import json,sys;d=json.load(sys.stdin)
notes=[n for n in d['data']['results'] if '课程标签' in str(n.get('tags',''))]
print(f'标签含目标课程: {len(notes)} 条')
"

# 2. 确认本地库覆盖（若本地不足，从 KB 全量拉取后同步）
getnote kb <KB_ID> --all -o json > /tmp/kb_full.json
python3 ~/.hermes/scripts/sync_getnote_kb.py --json-file /tmp/kb_full.json --kb-name wujun

# 3. 按标签导出（SQLite WHERE tags LIKE）
python3 -c "
import sqlite3, json
conn = sqlite3.connect('$HOME/.hermes/data/getnote_kb.db')
rows = conn.execute(\"SELECT title, content, tags FROM kb_notes WHERE tags LIKE '%目标标签%'\").fetchall()
data = [{'title':r[0], 'content':r[1], 'tags':json.loads(r[2]) if r[2] else []} for r in rows]
with open('/home/admin/course_notes.json','w') as f: json.dump(data, f, ensure_ascii=False, indent=2)
print(f'Exported {len(data)} notes')
\"
```

⚠️ **标签格式多样化**：同一课程可能使用多种标签格式。例如科技史纲有 `科技史纲60讲`、`科技史纲`、`吴军·科技史纲60讲`、`吴军科技史纲60讲` 等变体。SQLite 查询用 `LIKE '%科技史%'` 做宽匹配即可覆盖全部变体。标题不含课程名的笔记依赖标签发现——如果只按标题筛选会漏掉这类笔记（已验证：60→62，标签多出2篇正课+2篇邀请）。

---

## 🔴 Claude Code 调度铁律（含自检要求）

- `--dangerously-skip-permissions` 必带
- `background=true + notify_on_complete=true`
- **Kimi CLI 备选**：Claude Code 不可用时可用 Kimi CLI（`kimi -p`），但注意 `kimi-for-coding` **无思考模式**，不适合需要深度推理的 MKS HTML 生成。需思考时用 kimi-k2.6（需先配置 model entry）
- **派活必须带自检清单**：Claude Code 没有内置 post-fix verification——它修完就报「搞定」，但可能根本没修对。需求说明书末尾必须加入明确的「自检步骤」和「验收闸门」（一行硬性条件），例如：
  ```
  ## 自检步骤（修完逐条验证后再交付）
  1. grep 确认所有文件 .top-bar class 存在且含 position:sticky
  2. grep 确认 home-link href="index.html"（禁止 href="#"）
  3. 概念关系图区域非空：grep '<path|<polygon|<text' 有匹配 OR JS 中有 renderSVG()+innerHTML 赋值
   4. 提取 <script> 块后用 node -e "new Function(...)" 验证每个文件无语法错误（Node v24 不支持 .html 扩展名）
  5. 文件大小不低于修复前
  ## 验收闸门
  所有 N 个文件的导航条已统一，M 个概念关系图不再为空。
  ```
- **不给微操指令**，给目标和资源
- **可在 prompt 中嵌入 `/goal <验收条件>`** 防提前退出
- 🔴 MKS 文件调试/修复一律用 Claude Code，不用 Hermes 手动 patch（单行单点例外）
- **Claude Code 自报告不可信**：它说「fix confirmed」不代表真的修了——Hermes 必须独立复核，且复核必须包含浏览器渲染验证（不只是 grep/validator）
- 🔴 **并行 3 专题一进程 = 必死**：3 个专题 × 150 篇合计 → CC 进程静默死亡（零输出零文件改动）。每个专题独立一个 CC 进程才是可靠模式。科技参考3 验证：6 个独立进程 100% 成功，1 个合并进程（3专题）0% 成功
- 🔴 **僵尸进程延迟通知**：杀掉的 CC 进程可能在 20+ 分钟后才报告 completed，且已修改/commit 文件——其输出覆盖你的半成品。验证文件状态永远用 `os.path.getmtime()` + `git log`，不信任进程通知。科技参考3 实况：proc_4dfab03c398c 被杀→20min 后延迟完成通知 commit d022c0c 覆盖了 a6a3bb8
- 🔴 **CC 创意图空壳综合征**：CC 声称完成但 SVG 内零元素。验收：`grep -cP '<(path|polygon|rect|circle|ellipse)\\b' <file>` ≥30。
- 🔴 **MCQ 答案系统性偏向位置 1（选项 B）**：CC 独立生成的多文件 MCQ 中 ans:1 概率是 0/2/3 的 1.5-2 倍。验收：每文件用 Python Counter 统计 ans 值分布。
- 🔴 **大 JSON 静默死亡（>1.5MB）**：CC 读不动，30 分钟零输出。预抽取 title+摘要瘦身到 <100KB，同时提供完整 JSON 路径供深度出题。后备：两阶段生成（HTML+SVG→commit→补 JS）。
- 🔴 **3 专题合并在一个 CC 进程 = 必死（2026-06-14 科技参考3）**：150 篇文章/3 专题 → 27 分钟静默退出。MKS 专题重生成必须单专题单进程，--max-turns 60 足够。
- 🔴 **并行 CC 僵尸进程延迟通知**：杀掉的 CC 可能 20+ 分钟后才完成通知且已 commit/覆盖文件。验证用 `os.path.getmtime()+git log`，不信任进程通知。
- 🔴 **GitHub Pages CDN 缓存 + 忘 push 叠加**：用户反馈"没变化"时，先 `gh api` 查远端内容→若 API 也无变化→`git log --oneline origin/main` 查是否漏 push。最常见的根因是本地 commit 了但没 push。
- 🔴 **放权式创意 brief**：用户明确要求「让你参考不是照抄，根据自己内容重新创造，给最大权限」。导航页重设计只给源数据文件路径 + 一句愿景 + 最低约束，不给任何模板、配色、布局指令。结果：CC 自主创建「科技分光镜」「前沿观测台」等独特隐喻，远超按 spec 生成的品质。详见 `references/creative-brief-pattern.md`。**批量创意重设计 prompt 模板见 `references/creative-redesign-prompts.md`**（5+专题并行，每个含数据路径+内容主题+设计方向+叙事锚点+情感目标，6路并行全部成功的实战模板）。
## 大型 JSON 处理
- 🔴 **并行 3 专题一进程 = 必死**：3 个专题 × 150 篇合计 → CC 进程静默死亡（零输出零文件改动）。每个专题独立一个 CC 进程才是可靠模式。科技参考3 验证：6 个独立进程 100% 成功，1 个合并进程（3专题）0% 成功
- 🔴 **僵尸进程延迟通知**：杀掉的 CC 进程可能在 20+ 分钟后才报告 completed，且已修改/commit 文件——其输出覆盖你的半成品。验证文件状态永远用 `os.path.getmtime()` + `git log`，不信任进程通知。科技参考3 实况：proc_4dfab03c398c 被杀→20min 后延迟完成通知 commit d022c0c 覆盖了 a6a3bb8
- 🔴 **CC 创意图空壳综合征**：CC 声称完成但 SVG 内零元素。验收：`grep -cP '<(path|polygon|rect|circle|ellipse)\\b' <file>` ≥30。
- 🔴 **MCQ 答案系统性偏向位置 1（选项 B）**：CC 独立生成的多文件 MCQ 中 ans:1 概率是 0/2/3 的 1.5-2 倍。验收：每文件用 Python Counter 统计 ans 值分布。
- 🔴 **大 JSON 静默死亡（>1.5MB）**：CC 读不动，30 分钟零输出。预抽取 title+摘要瘦身到 <100KB，同时提供完整 JSON 路径供深度出题。后备：两阶段生成（HTML+SVG→commit→补 JS）。
- 🔴 **3 专题合并在一个 CC 进程 = 必死（2026-06-14 科技参考3）**：150 篇文章/3 专题 → 27 分钟静默退出。MKS 专题重生成必须单专题单进程，--max-turns 60 足够。
- 🔴 **并行 CC 僵尸进程延迟通知**：杀掉的 CC 可能 20+ 分钟后才完成通知且已 commit/覆盖文件。验证用 `os.path.getmtime()+git log`，不信任进程通知。
- 🔴 **GitHub Pages CDN 缓存 + 忘 push 叠加**：用户反馈"没变化"时，先 `gh api` 查远端内容→若 API 也无变化→`git log --oneline origin/main` 查是否漏 push。最常见的根因是本地 commit 了但没 push。
- 🔴 **放权式创意 brief**：用户明确要求「让你参考不是照抄，根据自己内容重新创造，给最大权限」。导航页重设计只给源数据文件路径 + 一句愿景 + 最低约束，不给任何模板、配色、布局指令。结果：CC 自主创建「科技分光镜」「前沿观测台」等独特隐喻，远超按 spec 生成的品质。详见 `references/creative-brief-pattern.md`。**批量创意重设计 prompt 模板见 `references/creative-redesign-prompts.md`**（5+专题并行，每个含数据路径+内容主题+设计方向+叙事锚点+情感目标，6路并行全部成功的实战模板）。
## 大型 JSON 处理

- 🔴 **大 JSON 导致 CC 静默卡死（2026-06-14 科技参考3 实况）**：Claude Code 读取 >1.5MB JSON 时可能静默卡死（30 分钟零输出、零文件修改）。**修复**：预抽取精简 JSON——只保留 title + 前 400 字符 content_text，可将 2MB → 75KB。Prompt 中同时提供精简 JSON（快速通读）和完整 JSON 路径（需要深度内容时再用）。

- 🔴 **3 专题合并在一个 CC 进程必然超时（批量 MKS 重生成经验）**：150 篇文章/3 个专题在一个 CC session 中 = 27 分钟零输出后硬盘 hang。**规则**：MKS 专题重生成必须单专题单进程。每进程 --max-turns 60 足够。并行启动 3 个独立 CC 进程比一个合并进程可靠 10 倍。

- 🔴 **CC `workdir` 参数不支持中文路径**：`terminal(workdir='/path/含中文/')` 直接 block（含非法字符）。**修复**：用 `cd /path/ && claude ...` 代替 workdir 参数。

- 🔴 **SVG 验证：pre-rendered dot 输出不含 'digraph' 字符串**：grep 'digraph' 搜索 dot 预渲染 SVG（`dot -Tsvg` 输出）必然返回 0——预渲染 SVG 只有 `<path|polygon|text>` 等图形元素，不含 dot 源码。**正确验证**：`grep -cP '<(path|polygon|rect|circle|ellipse|text)\b' <file>` 检查 SVG 图形元素数量。≥50 个元素 = 有内容；0 个元素 = 空壳。

---\n\n## Pitfalls

- **`getnote search` 硬上限 10 条**：标签筛选必须走 `getnote kb --all --output json`
- **`getnote kb --all` 大 KB 约需 3-5 分钟**：`--limit 500` 不起作用
- 🔴 **标题不含课程名的笔记依赖标签发现**：科技史纲案例——只按标题 LIKE '%科技史%' 搜到 60 篇，按 tags LIKE '%科技史%' 搜到 62 篇（多出 34/35/46 三讲+新课邀请）。同一课程标签有多种变体（如 `科技史纲60讲`/`吴军·科技史纲60讲`），SQLite 用 LIKE 宽匹配即可。**MKS 导出前必须同时查标题和标签，不能只查标题**。
- 🔴 **Claude Code prompt 中 shell 特殊字符导致执行失败**：当 prompt 含 `>`, `<script>`, `\u201C` 等字符时，shell 可能错误解释导致 Claude Code 收到乱码。症状：进程快速退出（exit code 1），输出 `bash: script 有且非空` 等错误。**修复**：将 prompt 写入文件，用 `claude -p "$(cat /tmp/prompt.txt)"` 传参。
- 🔴 **概念关系图不显示核心概念（2026-06-11 实况）**：部分子专题的概念层次图/概念关系图区域只显示图形框架但核心概念节点缺失。症状：SVG 存在但关键节点未渲染、或关系图只有层级标签没有具体概念名。根因：Claude Code 生成时的概念-图形映射断裂。修复：要求 Claude Code 逐子专题浏览器验证——打开每个专题的「概念关系图」区域，确认所有核心概念（至少 8 个）在图中可见且有中文关系标签。不要只依赖 grep 检查 SVG 标签存在。
- 🔴 **`submitExam()` 重新洗牌考题导致得分随机**：Claude Code 常见 bug——考试模式的提交函数可能重新 shuffle 题库而非使用原始考题顺序，导致用户作答与判分完全不对应。修复：新增 `examQuestions` 全局变量在考试开始时固定考题快照，`submitExam()` 使用快照判分。验收：在浏览器中完成一次考试，确认得分与所答选项一致。
- **首次生成答案偏斜率高达 4/7**：prompt 中必须写死数字分布
- **选择题答案偏斜是 Claude Code 最常见失败模式**：不要手动改 HTML，重跑或模板替换
- **顽固个案模板替换法**：取已通过的同类 HTML → 全局替换 → 5 分钟，比无限重跑可靠
- **HTML 生成后必须 JS 语法检查**：未转义单引号导致页面白屏
- **SVG 根节点不能套模板**：每个 MKS 从具体对象提取
- **笔记去重**：同内容不同日期 → 取最新一批，旧批独有的保留
- **校验器 regex 认四种 MCQ 格式**：`"answer":` / `answer:` / `ans:` / `a:`（2026-06-02 新增 `answer:` 无引号key支持，Claude Code 常用此格式）。详细 grep 验证命令见 `references/mcq-format-verification.md`。
- **DeepSeek 思考模式极浅**：必须 `effort=max`，否则 thinking ~200 tokens
- 🔴 **Claude Code prompt 传参：内联 `-p '...'` 在长 prompt（含中文/Unicode/特殊字符）下经常被 bash 误解析，导致 Claude Code 收到残缺 prompt 或直接报错。症状：`bash: script 有且非空` 或 `No such file or directory`。**可靠做法**：先将 prompt 写入文件（如 `/tmp/claude_prompt_N.txt`），再用 `claude -p "$(cat /tmp/claude_prompt_N.txt)"` 传参。此模式在本次 session 中验证可靠（3/3 成功 vs 内联 2/2 失败）。**注意**：`cat` 命令自动去除末尾换行，不会影响 prompt 完整性。
- **飞书不接收 HTML/zip 作为消息附件**：用云盘上传 + 发链接
- **飞书云盘 `drive +push` 假 skipped**：文件已本地修改但 push 仍显示全部 skipped → 先逐文件 `drive +delete --type file --as user --yes` 删除，再重新 push。token 可能在之前的删除操作后已变化，用 `drive +status` 获取当前文件 token。更可靠的方式：直接创建新文件夹（v2），全量上传。
- 🔴 **CSS `transform` 覆盖 SVG 内联 `transform`**：当 SVG `<g>` 元素已有 `transform="translate(x,y)"` 属性时，CSS `.comp-group:hover{transform:scale(1.015)}` 会**完全覆盖**（而非合并）SVG 属性，导致元素丢失位置从原点缩放——hover 时图标「飞走」。修复：删除 CSS `transform`，改用 `filter:brightness(1.25)` 或 `stroke-width` 变化实现 hover 效果。或者将主体内容包在内部 `<g class="comp-body">` 中，对 class 而非父级应用变换。
- 🔴 **`href="#"` + `onclick` 拦截导致返回链接失效**：MKS 专题页的「返回知识主板」链接常被 Claude Code 生成 `href="#" onclick="alert(...);return false"` 或 `href="#" onclick="switchTab(0);return false"`——这是死链模板占位符，部署时必须替换为 `href="index.html"` 并删除所有 `onclick`。验收时搜索 `href="#"` 和 `onclick="` 在 `.home-link` 上下文中确保零残留。
- 🔴 **翻转卡片固定宽度导致移动端太小**：`.flip-container` 禁止使用固定 `width: 280px`，必须用 `width: 100%; max-width: 500px`。移动端 `max-width: 400px`。
🔴 **翻转卡片 CSS 偏离金标准**：CC 常给 `.flip-card` 添加 `width:100%; max-width:Npx; height:auto; min-height:clamp(...)`。金标准（`4-历史与文明对话.html`）仅含 `perspective:1000px; height:clamp(220px,40vw,280px); margin-bottom:14px`——**无 width/max-width**（宽度由父容器控制），**用 height 非 min-height**。min-height 导致绝对定位子元素从父级获取不到高度→塌陷。修复：全局替换为金标准三属性。验收：`grep -n 'flip-card.*width\\|flip-card.*min-height' <file>` 必须零匹配。🔴 **用户反馈"宽度变窄了"**：当 CC 试图"加宽卡片"而添加 `max-width:700px` 时，反而因为 `width:100%` 约束使卡片在窄容器中更窄。金标准完全不用 width/max-width——让卡片宽度由父容器自然控制。
- 🔴 **翻转卡片高度不足遮挡翻页按钮**：`.flip-inner` 桌面端必须 ≥ 420px，移动端 ≥ 440px。四段式背面（定义+类比+示例+反例）内容多，300px 必然截断——背面文字和翻页按钮重叠。翻转后背面必须有 `overflow-y: auto`，确保内容多时可滚动。验收：在浏览器中切换知识卡片 tab，确认背面四段文字全部可见且翻页按钮在卡片下方不被遮挡。
- 🔴 **非标准模板翻转卡片固定 height 不足**：部分模块（如演化论）使用独立 CSS 架构（`.flip-card{height:240px;overflow:hidden}` + `.flip-card-inner`），不遵循标准 `clamp()` 模板。背面富文本（定义+类比+示例+反例，4-5 行/段）在 240px 高度下必然截断。**修复**：① 提高固定 height（240→380px 桌面端，220→320px 移动端）② 移除 `overflow:hidden` ③ 背面保留 `overflow-y:auto` 兜底。**验收**：`grep -n 'flip-card.*height.*px' <file>` 确认无 <300px 值。此模式已在演化论模块验证。
- 🔴 **`execute_code` 内 `read_file` 有 500 行上限**：`from hermes_tools import read_file` 在 execute_code 沙箱中默认只读前 500 行。对超过 500 行的文件（如 700+ 行的 index.html），`read_file(path)["content"]` 只返回前半部分——用 `write_file` 写回会**截断文件丢失后半段代码**。修复：在 execute_code 中使用 Python 原生 `with open(path) as f: content = f.read()` 读取完整文件。`patch` 工具无此问题，优先用 patch 做单点修改。
- 🔴 **CSS class 命名必须以 1-世界名校.html 为金标准**：所有专题 HTML 的 CSS class 必须统一使用世界名校的命名体系——`.quiz-card/.q-option/.q-feedback`（不是 `.quiz-option/.quiz-feedback`）、`.decision-round/.d-option/.d-result`（不是 `.decision-option/.decision-feedback`）、`.btn/.btn-outline`（不是 `.btn-primary/.btn-secondary`）、`.stat-card/.stat-grid`（不是 `.stat-item/.stats-grid`）、`.sa-input/.sa-reveal`（不是 `.short-answer-box`）。JavaScript 中的 `querySelector`/`classList` 引用也须同步对齐。
- 🔴 **题库选项容器 class 必须为 `quiz-card` 而非 `card`**：CSS 规则 `.quiz-card .q-option` 要求父容器有 `quiz-card` class。若用 `class="card"` 包裹题目，`.q-option` 样式（padding、border-radius、hover 效果）全部丢失，按钮显示为浏览器原生样式。验收：`grep -Pn 'class="card".*id="(mc|ex-mc|saq|mcq)' <file>` 必须零匹配。⚠️ 修复时注意模板语法：不同文件用不同模板（backtick `${i}` vs 拼接 `'+i+'`），`sed` 替换前先 `grep` 确认实际模板模式，不要照搬上一个修复命令。
- 🔴 **翻转卡片 HTML class 与 CSS 类名不一致**：Claude Code 常生成 `<div class="flipper">` 的 HTML 但 CSS 用 `.flip-card` 定义样式——类名不匹配导致翻转动画和 3D transform 完全失效。验收时检查：CSS 的翻转规则（`.flip-card` 或 `.flipper`）是否与 HTML 元素 class 一致。修复：CSS 中全局替换类名使其对齐。
- 🔴 **翻转卡片 `min-height` 导致绝对定位子元素塌陷**：当 `.flipper`、`.flip-front` 使用 `min-height` 而非 `height` 时，`position: absolute` 的子元素无法从父级获取高度，正反面叠加同时可见。修复：三处同时改为显式 `height: 420px`——①`.flipper{height:420px}` ②`.flip-front{height:420px}` ③`.flip-front,.flip-back{height:420px}`。移动端 `@media` 内同样三处同步。背面加 `overflow-y:auto` 处理长内容溢出。验收：`grep -n 'min-height' <file> | grep -i fli` 必须零匹配。
- 🔴 **媒体查询中 `position: relative` 覆盖 `absolute` 破坏 3D 翻转**：`@media (max-width: 600px)` 内常复制了 `.flip-front` 样式块但保留了 `position: relative`——此声明覆盖外层 `position: absolute`，使 `.flip-front` 脱离绝对定位堆叠，正反面不再叠加而是流式排列，导致两端内容同时可见。修复：媒体查询内 `.flip-front` 的 `position` 必须写 `absolute`（不是 `relative`），且 `.flip-front, .flip-back` 共享规则也必须包含 `position: absolute; height: 420px`。验收：`grep -n 'flip.*position.*relative' <file>` 必须零匹配。**调试提示**：`browser_snapshot` 的无障碍树会显示所有 DOM 元素（包括 `backface-visibility: hidden` 的背面），会误判翻转正常——必须用 `browser_console` 查 `getComputedStyle` 的 `position` 和 `height` 值来验证。
- 🔴 **翻转卡片 inline `onclick` 与 `addEventListener` 冲突**：当一个元素同时有 HTML 属性 `onclick="flipCard()"` 和 JS 中 `addEventListener('click', function(){...})` 时，两者依次触发且互相取消——`onclick` toggle 了 `.flipped` class，紧接着 `addEventListener` 又 toggle 回来，翻转无效。修复：只保留一种。标准做法是仅用 `addEventListener('click')` 绑定在 `.flip-inner` 上，移除所有 inline `onclick`。
- 🔴 **HTML/JS DOM ID 不匹配（部分标准化残留）**：当 HTML 已更新为 `.flip-card`/`.flip-inner` 标准模板但 JS 仍引用旧 ID（如 `flipper`、`cardIdx`、`learnedBtn`、`flipCounter`）时，所有卡片功能静默失效——无报错只是因为 `document.getElementById` 返回 `null` 且后续操作不抛出异常。修复：确保 JS 中的 ID 引用与 HTML 完全一致。标准 ID 清单：`flipInner`、`flipFront`、`flipBack`、`cardIndex`、`markLearnedBtn`、`cardProgress`、`cardLearned`。验收：`grep -Pn "flipper|cardIdx|learnedBtn|flipCounter|flipTitle|learnBtn" <file>` 在 JS 块中必须零匹配。
- 🔴 **JS 类名替换时 `+` 号被吞进引号产生语法错误**：当手动将 JS 中的旧 class 名替换为新 class 名时（如 `quiz-option` → `q-option`），`querySelectorAll('#mc-'+k+' .quiz-option')` 中的 `+` 可能被误写入引号内成为 `'#mc-+k+'`，产生 `missing ) after argument list` 语法错误。修复：替换后必须用 `node -e "new Function(js)"` 验证 JS 语法——HTML 的 `<style>` 不影响 JS 解析，只提取 `<script>` 块做语法检查即可。
- 🔴 **GitHub Pages CDN 缓存迷惑调试**：`git push` 后 GitHub Pages 不会即时更新，CDN 缓存约 1-2 分钟。若本地代码已修复但浏览器仍显示旧版，先用 `git log --oneline -1 -- <file>` 确认提交已在远端，再用地址加 `?v=N` 参数绕过缓存（如 `page.html?v=3`），或用无痕窗口打开。⚠️ **`?v=N` 不总是有效**：部分边缘 CDN 节点可能忽略 query param——即使换了 N 值仍返回旧内容。更可靠的做法：推送一个无伤大雅的空白变更（如在 `<title>` 末尾加空格）来强制全链路重新 fetch。诊断技巧：当 `node --check` 本地通过但浏览器报 JS 语法错误时，先对比三个来源确认谁才是罪魁祸首——`raw.githubusercontent.com` hex（源文件）、`browser_console` DOM（CDN 实际交付）、本地 git working tree。若 raw.githubusercontent.com 正确而 browser DOM 错误 → CDN 缓存问题，push 空白变更解决。
- 🔴 **浏览器工具验证 MKS 页面**：推送后可用 `browser_navigate` + `browser_console` 在服务器端验证页面是否正确渲染——检查 JS 错误（`browser_console` 的 `js_errors` 字段）和关键 DOM 元素是否存在（`browser_console(expression="document.querySelectorAll('.tab-btn').length")`）。⚠️ `browser_snapshot` 的无障碍树会显示所有 DOM 元素（包括 `backface-visibility: hidden` 的背面），不能用 snapshot 判断翻转是否正常。完整调试指南见 `references/browser-debugging-mks.md`。
- 🔴 **翻转卡片 `id=flipCard` 放错元素**：世界名校模板中 CSS 翻转规则是 `.flip-inner.flipped { rotateY(180deg) }`，JS 函数 `flip()` 调用 `document.getElementById('flipCard').classList.toggle('flipped')`。Claude Code 常把 `id="flipCard"` 放在外层 `.flip-container` 上——此时 JS toggle 的 `flipped` class 与 CSS 选择器 `.flip-inner.flipped` 不是同一个元素，翻转失效。修复：`id="flipCard"` 必须放在 `.flip-inner`（内层）而非 `.flip-container`（外层）。验收：`grep -A1 'flipCard' <file>` 确认 id 所在元素的 class。
  ⚠️ **例外——inline onclick 架构**：部分专题使用 `onclick="this.classList.toggle('flipped')"` 直接在 `.flip-inner` 上，此时翻转通过 `this` 引用自身，不依赖 `id="flipCard"`。外层 `id="flipCard"` 在此架构下是死代码，不影响功能。修复前先 `grep 'onclick.*flipped' <file>` 确认翻转机制类型——若用 inline onclick，无需移动 id。
- 🔴 **两套 MKS 代码架构差异**：硅谷来信1_v2 和谷歌方法论使用完全不同的 CSS/JS 体系（前者用 `.tab-nav`+`.flipper`，后者用 `.tab-bar`+`.flip-inner`），不能跨项目套用同一补丁脚本。修复前先确认目标文件属于哪个架构。
- 🔴 **单专题项目 404**：只有一个专题时，根 `index.html` 链接必须指向 HTML 文件（`href="项目名/1-专题名.html"`），不能指向目录（`href="项目名/"`）。目录无 `index.html` → GitHub Pages 返回 404。已验证：逻辑思维训练50讲。

- 🔴 **用户给 URL → 先映射到本地文件路径，再操作**：用户说「改这个页面 https://andy-zokelink.github.io/mks-knowledge/blog/」时，本地路径是 `blog/index.html`，不是 `吴军思想体系/index.html`。GitHub Pages URL 的路径段直接对应 repo 目录结构：`mks-knowledge/blog/` → `/home/admin/mks-knowledge/blog/index.html`。**操作前强制步骤**：① 从 URL 提取 repo 内的相对路径（去掉 `https://域名/仓库名/` 前缀）；② `ls -la 提取的路径/` 确认目标文件存在；③ 再开始修改。**禁止凭 URL 中的内容线索（如看到「阿森智库」文字就跳到其他目录）猜测文件位置**——URL 路径是唯一真相源。：这是 MKS 修复中最隐蔽、最浪费时间的坑。根 `index.html` 的链接可能指向 `项目名_v2/` 而非 `项目名/`——改错了目录，所有修复对用户完全不可见。**任何 MKS 文件编辑操作前，强制第一步**：① `grep "项目名" index.html` 确认根导航链接指向的实际目录；② `ls -la 项目名*/` 确认是否有多个版本目录共存；③ 确认后，**在相同目录下操作**，跨目录的修复用 `cp` 同步。**禁止凭记忆或假设直接编辑**——此步骤耗时 5 秒，跳过它可能浪费数小时。用户反馈「刷新后没变化」时，此检查永远是第一个诊断动作。
- 🔴 **delegate_task 部分完成后文件状态不一致**：delegate_task 超时时文件可能处于「CSS/HTML 已标准化但 JS 仍引用旧 ID」的半成品状态。核心症状：HTML 使用 `id="flipInner"`、`id="cardIndex"`、`id="markLearnedBtn"`，但 JS 仍引用 `flipper`、`cardIdx`、`learnedBtn`——所有卡片功能静默失效（`getElementById` 返回 null，不抛错）。验收命令：`grep -Pn "flipper|cardIdx|learnedBtn|flipCounter|flipTitle|learnBtn" <file>` 在 JS 块中必须零匹配。**不可假设 delegate_task 成功后文件就完全一致**——必须逐文件验证 CSS、HTML、JS 三层的 DOM ID 对齐。
- 🔴 **无本地 clone 时用 `gh api` 读取 GitHub 文件**：当本地没有 repo clone 且 `git clone` 超时时，用 `gh api repos/owner/repo/contents/path --jq '.content' | base64 -d` 直接获取文件内容。比 browser_navigate 到 GitHub Pages 更可靠——中文路径可能 404，但 API 路径正常。
- **批量 patching renderCard 可能产生重复的 `document.getElementById`**：用 Python 正则替换 renderCard 内部 HTML 时，若 old_front_block 匹配不全，会在同一行残留两个 `document.getElementById('flipFront')`，导致 JS 语法错误。patch 后必须 `python3 -c "scripts[0].split('\\n')[420]"` 抽查关键行。
- **flipCard 函数缺失导致翻转按钮无响应**：批量打补丁时 flipCard 函数不会自动注入。
- 🔴 **delegate_task 不适合 MKS HTML 生成或批量修复**：子 agent 在长任务（>100s）中频繁被父进程中断，返回 `Parent agent interrupted`。MKS HTML 生成（读笔记→分析概念→写 HTML→validator）需 5-15 分钟，远超 delegate_task 生命周期。⚠️ **即使轻量任务（批量 CSS/JS 修复多个文件）也可能超时**——处理 5-7 个大 HTML 文件（80KB+）时，子 agent 用 600s 仍可能不够，最终文件处于「HTML 已标准化但 JS 仍引用旧 ID」的半成品状态（CSS/HTML 正确，JS 静默失效——`getElementById` 返回 null 不抛错，极难排查）。MKS 所有文件操作一律用 Claude Code `background=true + notify_on_complete=true` 或 Hermes 直接 `patch`。delegate_task 仅用于纯分析/分类/单文件单点替换。
- 🔴 **JS 调试残留 inline transform 干扰 CSS 翻转验证**：在浏览器 console 用 `element.style.transform = 'rotateY(...)'` 测试翻转后，inline style 会永久覆盖 CSS class 的 `transform`（即使 class 已 toggle）。此后 CSS 翻转规则看似「不生效」但实际是 inline 优先级更高。修复：测试后执行 `element.style.transform = ''` 清除 inline，即可恢复 CSS class 控制。验收翻转时，先确认元素无 inline style 残留。
- 🔴 **空 SVG/div 容器通过 validator**：`validate_mks_html.js` 只检查 `<svg` 标签存在，不检查内容。`<svg viewBox="0 0 900 620"></svg>`（空标签）和 `<div id="svgMap"></div>`（无 JS 填充的空容器）都能通过校验。验收必须加一步内容完整性检查：
  ```bash
  # 检查 SVG 是否有实际图形元素
  grep -cP '<(path|polygon|rect|circle|ellipse|text|g\b)' <file>
  # 若返回 0，说明 SVG 是空的 —— 即使 grep -c '<svg' 返回正数
  # 对于 JS 动态填充的容器（如 id="svgMap"），检查是否有对应的 renderSVG() / innerHTML 赋值
  grep -cP 'renderSVG|svgMap.*innerHTML|svgContainer.*innerHTML' <file>
  ```
- 🔴 **导航条不统一通过 validator**：`validate_mks_html.js` 不检查导航条结构。不同文件可能用 `<header>`、裸放 `.home-link`、`.back-home` 等不同模式，全部通过校验。验收时必须逐文件确认：① `.top-bar` class 存在 ② `position: sticky` 在 `.top-bar` CSS 中 ③ `.home-link` 在 `.top-bar` 内 ④ `href="index.html"`。黄金模板为 `1-科技与商业逻辑.html`（硅谷来信1_v2）和 `1-世界名校.html`（谷歌方法论）。
  ```bash
  # 批量导航条审计
  for f in *.html; do
    has_topbar=$(grep -c 'class="top-bar"' "$f")
    has_sticky=$(grep -cP '\.top-bar\s*\{[^}]*position\s*:\s*sticky' "$f")
    has_homelink=$(grep -c 'home-link.*index.html\|home-btn.*index.html' "$f")
    echo "$f: top-bar=$has_topbar sticky=$has_sticky home=$has_homelink"
  done
  ```
- 🔴 **MKS 统一性审计**：当需要对一批专题 HTML 做统一性检查时，按 8 个维度审计：标签名、导航条、知识卡片、题库系统、概念关系图、配色方案、内容文本、知识集总览。完整审计清单和快速命令见 `references/mks-uniformity-audit.md`。检查顺序：先审计黄金模板本身是否合规 → 再逐文件对照 → 文本替换类 Hermes 直接 sed → 结构类派 Claude Code。
- **MKS 专题重生成 prompt 模板**：将静态文章页升级为 9 标签交互 MKS 的标准 prompt，含大 JSON 精简策略和并行调度方案。见 `references/mks-topic-regeneration-prompt.md`。

### 题库系统交互规范（2026-05-31 定稿）

**终态行为**：
1. 进入题库 tab → 仅显示「🎲 随机抽题练习」+「重置」两个按钮
2. 点击「随机抽题」→ 题目出现在 quizContainer，staticQuiz 保持隐藏
3. 点击「重置」→ 清空 quizContainer，回到纯按钮状态（staticQuiz 不复显）
4. 再次点击「随机抽题」→ 随机生成新题目（与上次不同）

**关键实现**：
- `staticQuiz` 初始 `display:none`（不是 `block`）
- `startQuiz()` 做 `Math.random()-0.5` 随机打乱 + 取前 N 题
- `resetQuiz()` 只清 `quizContainer` + 停止计时器，**不操作 staticQuiz**（staticQuiz 始终保持 `display:none`）

**禁止**：
- `resetQuiz` 中 `staticQuiz.style.display = 'block'`（会导致旧题库突然冒出）
- `staticQuiz` 初始 `display:block`（进入题库就暴露旧题目）

- 🔴 **`new Function()` 语法检查通过但题库运行时崩溃：中文选项漏引号（2026-06-12 实况）**：`node -e "new Function(m[1])"` 只验证语法结构——它将未加引号的中文文本当作合法 identifier token 放行。但浏览器执行 `opts:[不是给细胞换机器而是发一张临时配方纸条,...]` 时，JS 引擎将 `不是给细胞换机器而是发一张临时配方纸条` 解析为变量引用 → `ReferenceError: X is not defined`。**症状**：所有 6 个文件 `node -e "new Function()"` 通过、零报错，但浏览器中一个专题页的题库渲染时报错。**修复**：`grep -Pn "opts:\[[^'\"]" <file>` 扫描所有文件——未以引号开头的 opts 数组元素即漏引号。**验收**：`grep -cPn "opts:\[[^'\"]" *.html` 必须全零。典型症状：`h+='</li>'; return h; } document.getElementById('mindmap').innerHTML=...` 作为孤立行残留在 `function renderMindmap(){}`（no-op）之后、下一个函数之前。`return h;` 在全局作用域触发 `SyntaxError: Illegal return statement`，中断所有 JS 初始化——tab 切换全页失效。**验收时 `node --check` 逐文件验证 JS 语法是硬性要求**，不能只看文件内容。修复：删除函数体外的残留代码行。

- 🔴 **`isChoice` 正确但 `options` 提取漏了 `q.o`**：即使 `isChoice = q.opts || q.options || q.o` 正确识别了 MC 题，渲染时 `const options = q.opts || q.options;` 仍漏掉 `q.o`——导致 MC 题显示「选择题」标签但选项为空（无 A/B/C/D 按钮）。不同文件用不同字段存选项：1/2/3/4号用 `o`，5/6/7号用 `opts`。修复：`options` 提取必须三级兼容 `q.opts || q.options || q.o`。验收：`grep -n 'options = q.opts || q.options;' <file>` → 必须改为 `q.opts || q.options || q.o`。\n\n- 🔴 **Claude Code 达到 max-turns 但已提交**：当 Claude Code 在 turn 80 退出（exit code 1）时，可能在退出前已完成 commit+push。不要假设 exit 1 = 零进度。先 `git log --oneline -3` + `git diff --stat HEAD~1 HEAD` 确认是否有新 commit，避免重复派活。本次对话：Claude Code ran 80 turns → exited with code 1 → but had already committed `078d59f` fixing all 7 files。
- 🔴 **Claude Code 达到 max-turns 但修改了文件未 commit（更隐蔽）**：CC 超时后可能已完成修改但既未 commit 也未 push——`git log` 看不到，`git status` 才有脏文件。验证三步：① `git status --short` 查脏文件 ② 若文件有修改，`git diff --stat` 确认改动量 ③ 若改动有效（如 CSS/HTML 结构修复），手动 `git add && git commit && git push`。**不要假设浏览器端 `undefined` = 修复失败**——可能是本地修好了但没 push 到 GitHub Pages。2026-06-12 实况：模5 CC 25 turns 超时，本地 85+/92- 行修复完成但未 commit，浏览器 `typeof renderAllQuiz` 返回 `undefined`，反复调试才发现是 CDN 缓存了旧版。\n\n- 🔴 **init() 中调用未定义函数导致全页 JS 初始化链断裂**：任何 `ReferenceError: X is not defined` 在 init 段都会中断后续所有渲染调用——tab 切换、思维导图、深度追问、实战决策全部静默失效（用户看到空白页或交互无响应）。常见变体：`renderCard()`（已重命名为 `setCard()`）、`renderFlipCard()`（翻卡已改为内联 setCard，但旧调用残留）、`renderMCQ()`/`renderSAQ()`（已合并入 renderQuiz）。🔴 **另一变体：`TypeError: Cannot read properties of undefined (reading '0')`**——当 init 链中某个 render 函数访问了 undefined 数组的元素时（如 `useCases[i][0]` 把并行子数组误写为按行索引——CC 常见 bug，混淆 2D 数组维度方向），同样会无声中断后续所有 `renderSocratic()`、`renderDecision()` 等调用，症状为深度追问/实战决策 tab 完全为空。**诊断**：`browser_console` 查看 `js_errors` 字段定位中断点。**两种修复策略**：① 注释掉调用（`// renderFlipCard();`）——适用于该功能已由其他函数覆盖；② 添加空函数 no-op stub（`function renderFlipCard(){}`）——适用于调用分散在文件多处、逐个修改风险高、或该函数在 init 链中位置关键（删除调用会改变执行顺序）。**验收**：`grep -n 'renderCard\|renderFlipCard\|renderMCQ\|renderSAQ' <file>` 在调用位置（非定义位置）必须零匹配，或确认已有对应 no-op stub。

- 🔴 **GitHub Pages CDN 缓存下的本地验证策略**：`git push` 后 CDN 可能缓存数分钟，即使加 `?v=N` 参数也可能不刷新。**发版后首轮验证直接使用本地 `file://` URL**（如 `file:///home/admin/mks-knowledge/谷歌方法论/3-发明与创新.html`），绕过 CDN 确认文件本身正确后再等线上同步。`browser_navigate` 支持 `file://` 协议。验收：修复后 → 本地 file:// 逐页验证 → 确认无误 → push → 告知用户 CDN 需 1-2 分钟刷新。诊断命令：
  ```bash
  # 检查是否有空壳 div 无 JS 填充（或 JS 填充存在但内容不渲染）
  grep -n 'getElementById.*mindmap\|getElementById.*socratic\|getElementById.*caseStudy\|getElementById.*decision\|getElementById.*simGame\|getElementById.*simScenarios' <file>
  # 若命中，说明是 JS 动态生成模式——需验证浏览器实际渲染是否空白
  ```
  验收：浏览器打开 → 依次切换 tab5/6/7/8 → 每页确认有可见内容（不是空 div），验收前必须 screenshot。

- 🔴 **`startQuiz()` 渲染函数条件判断误用 `q.type===undefined`**：当 `renderQuizPractice()` 中的条件写为 `if(q.type===undefined || q.opts)` 时，选择题和简答题都没有 `type` 字段，`q.type===undefined` 永远为 true——简答题也会误入选择题分支，然后 `q.opts.forEach()` 在 undefined 上报错 `Cannot read properties of undefined (reading 'forEach')`。修复：只用 `if(q.opts)` 判断（有 opts 字段 = 选择题，无 opts = 简答题），删除 `q.type===undefined ||` 冗余前缀。此 bug 在 Claude Code 生成的 6 个文件中有 1 个（1-科技与商业逻辑）使用了错误条件，其余 5 个正确。验收：`grep -n 'q.type===undefined' <file>` 必须零匹配。

- 🔴 **`toggleMindmap` 函数签名不匹配导致思维导图无法折叠**：不同文件的 `toggleMindmap` 有两种签名——`toggleMindmap(el)` 接收 DOM 元素（通过 `el.classList.toggle()` + `el.nextElementSibling.classList.toggle()` 操作），`toggleMindmap(id)` 接收字符串 ID（通过 `document.getElementById(id)` 查找）。内联 HTML `onclick="toggleMindmap(this)"` 只能与 `toggleMindmap(el)` 签名兼容——传给 `toggleMindmap(id)` 时，DOM 元素被隐式转为字符串 `[object HTMLDivElement]`，`getElementById` 返回 `null`，折叠静默失效。**修复**：统一使用 `toggleMindmap(el)` 签名，兼容两种调用：`typeof el==='string'` 分支走 id 查找，否则走 `el.classList.toggle()`。验收：`grep -n 'function toggleMindmap' <file>` 确认函数体内同时处理 string 和 element 两种参数类型。

- 🔴 **重复 `id` 属性导致 DOM 查询返回错误元素**：当 tab5 区域出现两个 `<div id="tab5">`（一个旧版空壳 + 一个新版完整内容）时，`document.getElementById('tab5')` 永远返回第一个（空壳），新版完整内容不可见——用户看到「显示不全」或「空白」。此 bug 源于 Claude Code 在已有结构上追加新内容而非替换旧内容。修复：删除旧版空壳 div，只保留一个 `id="tab5"`。验收：`grep -c 'id="tab5"' <file>` 必须返回 1。**此 bug 也适用于任何重复的 id**（tab6、tab7 等），验收时对所有 tab-content id 做去重检查。

- 🔴 **Tab 名称漂移——'苏格拉底问答' 未对齐规范**：即使 tab 按钮显示「深度追问」，`tabNames` 数组或内容区 comment/header 中仍可能残留 `'苏格拉底问答'` 或 `'苏格拉底'` 旧名。验收：`grep -Pn "苏格拉底(?!式)" <file>` 在 JS 字符串和 HTML comment 中必须零匹配（只允许「苏格拉底式追问」这种内容文本，不允许 tab 标签名中有「苏格拉底」）。

- 🔴 **批量修复时文件架构差异导致新 bug**：谷歌方法论各文件使用不同的 tab 切换机制——4号用 `data-tab` 属性 + event listener，2/7号用 `id="tabN"` + `getElementById`，7号还用 `id="tab-N"`（带连字符）。Claude Code 批量修改时按一个模式改所有文件，必然在架构不同的文件上产生 bug（如 tab 切换失效、mindmap toggle 类型不匹配、duplicate ID）。**任何批量 MKS 修复任务，需求说明书必须先列出每个文件的架构差异**（tab 切换方式、mindmap toggle 签名、DOM ID 命名规则、数据字段命名），让 Claude Code 逐个文件按本地架构修改，不套统一 patch。验收时逐文件检查以上四项的兼容性。

- 🔴 **思维导图内容重复 + 多余 `</div>` 导致 DOM 节点泄漏到 tab-panel 外**：当思维导图内容被复制粘贴两次后，中间多出一个 `</div>` 提前关闭了 `tab-panel`——后续重复内容变成 `.container` 的直接子节点，不受 `tab-panel { display: none }` 控制，在任何 tab 下都可见（表现为页面底部出现大量奇怪文字）。**诊断命令**：`browser_console` 中执行 `document.querySelectorAll('.container > :not(.tab-panel):not(.tab-bar):not(.toast)')` 检查是否有裸节点在 tab-panel 外部。**修复**：删除重复内容块 + 多余闭合标签，用 `grep -c '<div'` vs `grep -c '</div>'` 数开口/闭口数量差定位净不平衡位置。**验收**：浏览器打开知识集总览 tab → 滚动到底部 → 确认「学习进度」之后无任何其他文字。

- 🔴 **Hermes 复核必须包含浏览器渲染验证**：grep + validator 只能验证「有没有」，不能验证「对不对」。Claude Code 修完 MKS HTML 后，Hermes 必须逐文件做浏览器视觉复核：
  - `browser_navigate` 打开每个页面 → 确认导航条 sticky 常驻右上角
  - 确认概念关系图有实际图形渲染（非空白区域）
  - 逐一切换 tab5-8：思维导图可折叠、深度追问有 Q&A、案例分析有内容、实战决策可交互
  - 用 `browser_console` 检查 `document.querySelectorAll('#tab5 .node-header').length` 确认节点数
  - **不要假设 grep 通过 = 渲染正确**——JS 生成的 SVG 在源码中只有模板字符串，grep 搜不到 `<path>` 元素；空 `<svg></svg>` 也有标签但无内容
- 🔴 **`browser_snapshot` 的无障碍树包含 hidden 元素**：用 `browser_console` 查 `getComputedStyle` 或 `offsetParent` 确认实际可见性
  - **`browser_vision` 可能不可用**：kimi 等模型不支持 `image_url` 消息格式（400 `unknown variant 'image_url'`），此时用 `browser_console` 做 DOM 级检查 + `browser_snapshot` 做文本级检查替代视觉截图分析
  - 🔴 **`browser_vision` 桌面端宽视口可能漏检窄屏 SVG 遮挡**：vision 在默认桌面宽度（~1200px）分析 SVG 图时，由于 viewBox 等比缩放后有充足空间，常报告「无遮挡」。但用户在实际设备（窄屏/移动端）上看到节点重叠。**诊断**：① 先用桌面端 vision 快扫 ② 若用户仍反馈遮挡 → 直接检查 SVG viewBox 宽度与页面实际容器宽度之比——viewBox>900 且无 `max-width:100%;height:auto` 大概率在窄屏出现缩放挤压 ③ 或用 `browser_console` 执行 `document.querySelector('.svg-wrap svg').getBoundingClientRect().width` 对比 viewBox 确认缩放比例。**修复**：优先扩大 viewBox + 调整节点间距，而非只加 CSS 缩放（后者在极端窄屏文字不可读）。2026-06-12 实况：工具箱 viewBox 960→1020、医学简史 viewBox 960×520→960×570，两者均重排了节点坐标。

- 🔴 **`.html` 文件 JS 语法检查方法**：`node --check file.html` 在 Node.js v24 上因 `.html` 扩展名报 `ERR_UNKNOWN_FILE_EXTENSION`。正确做法：提取 `<script>` 块后再检查。
  ```bash
  # 通用方法（适用于单个 <script> 块的文件）
  sed -n '/<script>/,/<\/script>/p' file.html | sed '1d;$d' > /tmp/check_js.js && node --check /tmp/check_js.js
  # 更健壮的方法（Node 直接解析）
  node -e "const fs=require('fs');const c=fs.readFileSync('file.html','utf8');const m=c.match(/<script>([\\s\\S]*?)<\/script>/);new Function(m[1]);console.log('OK')"
  ```

- 🔴 **`Unexpected token ']'` / `'}'` 在 `];` 或 `};` 行——报错行不是真 bug 位置**：当 node 报错指向某个 `];` 或 `};` 行时，真正的缺失括号通常在**上方 1-3 行的嵌套对象/数组结构内**。典型模式：深层 `{theme:'...', pairs:[...]}` 缺了 theme 对象的 `}`，只有 `pairs]`——解析器继续往下读到外层 `];` 时才发现上下文不匹配。`};` → `];` 的修复是错误方向（改完报错变 `Unexpected token ']'`）。诊断：① 提取 JS 块做 `node --check`，定位报错行 ② 回溯检查报错行上方最后一个复杂对象的完整闭合链（`}` + `]` + `}` 是否齐全）③ 字符级 hex dump 确认（`7d`=`}`, `5d`=`]`）。详见 `references/js-bracket-mismatch-debug.md`。
- 🔴 **Hermes 复核必须包含浏览器渲染验证**：grep + validator 只能验证「有没有」，不能验证「对不对」。Claude Code 修完 MKS HTML 后，Hermes 必须逐文件做浏览器视觉复核：
  - `browser_navigate` 打开每个页面 → 确认导航条 sticky 常驻右上角
  - 确认概念关系图有实际图形渲染（非空白区域）
  - 确认导航链接可点击且指向正确 href
  - **不要假设 grep 通过 = 渲染正确**——JS 生成的 SVG（`document.getElementById().innerHTML = svg`）在源码中只有模板字符串，grep 搜不到 `<path>` 元素，但浏览器可以正常渲染
  - 验证完后用 `browser_back` 或再次 `browser_navigate` 打开下一页

- 🔴 **`<div>` 开口/闭口计数法定位 DOM 泄漏**：当页面出现莫名文字泄漏（思维导图内容出现在首页底部），用 Python 快速统计目标区域的 `<div` 开口数和 `</div>` 闭口数之差。净不平衡位置即问题行。硅谷来信 6号案例：356-550 行开口 137、闭口 143（净 -6），定位到行 460 的多余 `</div>` 提前关闭了 tab-panel，导致后续 83 行重复内容脱离 tab-panel 变成 `.container` 裸子节点始终可见。

- 🔴 **HTML 中 Unicode 转义序列乱码**：Claude Code 生成 HTML 时，emoji 和特殊字符可能被错误编码为 JS 转义序列（如 `\\u2728`、`\\U0001f4ca`、`\\u201C`），在浏览器中显示为原始字符串而非实际字符。**症状**：页面标题、导航按钮、链接文字出现 `\\uXXXX` 裸文本。**根因**：这些序列应该直接使用 UTF-8 编码的实际 Unicode 字符，而非 JS 风格的转义序列——JS 转义只在 `<script>` 块内有效，HTML 正文中必须用原始 Unicode 或 HTML 实体。**修复**：逐字符替换 → `\\u2728`→✨、`\\U0001f4ca`→📊、`\\u201C`→"、`\\u201D`→" 等。**验收**：`grep -Pn '\\\\\\\\u[0-9a-fA-F]{4}|\\\\\\\\U[0-9a-fA-F]{8}' <file>` 在 HTML 正文（非 script 块）中必须零匹配。

- 🔴 **Hero区域与tab0统计重复（"总览页的开头"）**：Claude Code 常在每个专题页顶部生成 hero 区域显示统计条（N+日课篇、N核心概念、N检测题、N学习阶段），而这些数据在 tab0（知识集总览）中已完整展示——用户看到的是两个"总览页的开头"，显得多余重复。**修复**：hero 应精简为一句引人入胜的专题引言+副标题，像杂志封面导语而非数据仪表盘。或者将统计数字融入 hero 设计但不要与 tab0 内的相同数据重复出现。🔴 **更严重：hero 放在 tab-panel 外部**——CC 常把 `<section class="hero">` 放在所有 `<div class="tab-panel">` 之外，导致切换到任何 tab 都看到 hero 文字（如实战决策 tab 仍显示"理解数字世界运转的底层指令"）。**修复**：将 hero HTML 移入 `panel0` 内部最前面，作为 tab0 的专属引言。**验收**：浏览器打开页面 → 切换到实战决策 tab → 确认 hero 文字不再显示。

- 🔴 **多文件创意图千篇一律（模板填充）**：当 Claude Code 批量生成多个专题文件时，每个文件的"创意图"（🎨 创意图 / creativeSVG）常使用相同的 SVG 布局模板，只是替换了文字标签——6 个文件看起来像同一模板换了不同标题。**修复**：要求 Claude Code 重读每个专题的核心概念和 MKS 工具箱内容，从内容本身出发为每个专题创作独特的视觉表达。不要给模板约束，说「根据各自专题内容创作独特的创意图，宁缺毋滥」。**验收**：逐文件浏览器打开创意图区域 → 确认 6 个图的视觉语言、布局、配色有明显差异，不是同一模板填充。

- 🔴 **标杆驱动质量提升（2026-06-11 验证）**：多专题项目中，自然会产生一个质量出众的标杆专题（如科学思维课的「演化论」专题——结构完整、排版设计符合内容）。将此标杆作为其他专题的参照物，要求 Claude Code：「以 X 专题为设计标杆，确保核心概念、概念层次关系、学习路径、知识卡片、思维导图、深度追问、案例透析、实战决策等工具自然合理穿插到内容中」。不要修复已经优秀的专题，只修需要修的。违例：全线重做导致优质专题被覆盖。

- 🔴 **MKS 视觉主题重设计工作流**：当已有 MKS 页面功能正确但视觉平淡时，不需要走场景 D 重生成——给 Claude Code 最大创作自由（不给 preset 配色/布局），让它从内容本源出发自主设计。不改 JS，只换 CSS + HTML 结构。⚠️ 重设计后必查三大回归：翻转卡片失效、题库随机只出 5 题、思维导图混乱。完整流程和验收见 `references/mks-visual-redesign.md`。**V2 创意重设计（初版→完整版升级）**见 `references/mks-v2-creative-redesign.md`——当代用户反馈"不够完整""排版不够""配色不匹配内容"时使用。**回归 checklist 和重型任务拆分策略见 `references/mks-redesign-regression-checklist.md`。**
- 🔴 **多模块并行修复工作流**：当重设计导致跨模块共同回归时，用 `terminal(background=true)` + `claude -p` 逐模块并行修复——Hermes 只做调度/协调/总结，Claude Code 做重体力编码。复杂模块（5+ 任务）拆两半并行（机械修复 30 turns + 创意重绘 40 turns），避免单次 60 turns 超时。完整流程、根因分析和 Andy 审 MKS 硬性标准见 `references/mks-bugfix-parallel-workflow.md`。
- 🔴 **重设计后三大共同回归已模式化**：翻转卡片失效（缺 -webkit- 前缀 + 父容器高度塌陷）、题库随机只出 5 题（slice(0,5) 未改）、思维导图混乱（`<li>` 裸放 `<div>` 内）。逐模块修复 checklist 和重型任务拆分策略见 `references/mks-redesign-regression-checklist.md`。

- 🔴 **文章摘要目录 ≠ MKS（2026-06-11 重大事故）**：从课程正文批量生成 HTML 时，最危险的歧路是按标题/日期把文章归类，每类一个 HTML 列出「文章标题+摘要」——看起来像 MKS（白底琥珀色、卡片式布局、响应式），但**零概念提取、零 flip-card、零 SVG 关系图、零 MCQ、零 9 标签**。用户当场否定全量重做。任何从 SQLite/content_text 批量生成 MKS 的任务，Claude Code prompt 必须写死：提取 8-12 个核心概念（不是按文章标题分类）、每概念配定义/类比/示例/反例、9 标签齐全、Graphviz SVG 概念图、MCQ 均匀分布。验收时 grep 确认 `flip-card` 和 `知识集总览` 存在——缺失任一即为摘要目录而非 MKS。

- 🔴 **MKS 知识卡片必须是概念 flip-card，禁止原始笔记全文 dump**：Claude Code 在生成专题 HTML 时，有时会将 JSON 源数据中的每条笔记原文直接填入卡片（每张卡片几百字的 raw markdown），而不是提炼为「概念名(正面)+1-2句精炼定义(反面)」的标准 flip-card。**症状**：卡片 tab 中显示的是「美索不达米亚文明」「苏美尔文明」「古巴比伦」等按文明分类的原始笔记全文，而非按概念组织的 flip-card。**根因**：Claude Code 把原始笔记当成了概念卡片数据源，直接 map 到卡片 DOM。**修复**：要求 Claude Code 从笔记中提取 8-10 个跨文明的抽象概念（如「五大文明中心」「农耕 vs 商业文明」「楔形文字」「汉谟拉比法典」），而非按文明分类。每个卡片 = 概念名(正面) + 1-2句精炼定义(反面)，控制在 50 字以内。**验收**：browser_click 到卡片 tab，确认每张卡片显示的是概念名而非原始笔记标题，翻转后是简短定义而非全文。

- 🔴 **全项目旧模板扫描→场景D批量重生成**：当整个项目（如 硅谷来信3 全部 7 文件）使用旧模板架构时，先逐文件审计 8 维度（导航条/翻卡/题库/标签/总览/SVG/配色/tab5-8），汇总共性问题后走场景 D：7 个 Claude Code `background=true` 并行重生成，每个读旧文件提取数据 + 读黄金模板套结构 → 自检验证 → Hermes 逐文件浏览器复核 → 统一 commit push。旧标签对照表（选择题/简答题→题库系统、关系图→知识集总览、苏格拉底→深度追问、决策模拟→实战决策、概念卡片→知识卡片）是批量改造的核心参照。

- 🔴 **实体卡片项目须预处理，不要让 Claude Code 边读边拆**：5,000+ 张实体卡片需从 SQLite 用 regex 预抽取为 JSON，只给 Claude Code 做视觉创作。5000+ DOM 卡片会崩溃浏览器，必须用 Canvas 渲染 + 视口虚拟化。完整抽取流水线见 `references/entity-extraction-pattern.md`。**⚠️ 自动抽取的实体 70%+ 是语义碎片（见参考文档质量陷阱节）。用户明确要求：先筛选有意义概念 → 再分类 → 再补充解释 → 最后设计呈现。不要跳过筛选直接建 UI。**语义提取流水线见 `references/concept-extraction-pipeline.md`——用三批并行 CC 从原始笔记提取高质量概念（去碎片率 >90%）。

**🆕 概念卡片图谱专项**：跨课全量概念提取 + 富元数据（定义/类比/示例/反例/来源）+ 水下卡片 UI 的完整流水线见 `references/concept-cards-pipeline.md`。水下卡片交互模式（hover 涟漪 + 点击捞出放大 + 单开约束 + 浅色低对比 palette）见 `references/underwater-card-ux.md`。**清洗后概念的批量富化**（已有 name/category 的概念 → 批量 Claude Code 生成完整定义/类比/事例/反例）见 `references/concept-batch-enrichment.md`。
- 🔴 **文章列表页→MKS升级模式**：当专题文件是静态文章归档（白底+琥珀色，无 flip-card/quiz/SVG），需要全量升级为 9 标签交互 MKS。完整流程见 `references/article-listing-to-mks-upgrade.md`。关键词：提取文章编号→反查 JSON→分专题数据→并行 CC 升级。
- 🔴 **卓克项目数据发现三步**：① 先 `browser_navigate` 看 GitHub Pages 确认页面是否存在；② `search_files` 查本地 repo 目录结构；③ 找独立 JSON 源文件（`find /home/admin -name '*zhuoke*' -name '*.json'`），不要只搜 SQLite。卓克数据不走 Get Notes 管道。
- 🔴 **源数据标签≠内容真相——CC会静默编造内容填补标签空洞（2026-06-13 科技参考3航天专题实况）**：当 JSON 文件的章节标签（如 `chapter_id` 对应"航天与太空探索"）与实际文章内容不匹配时（科技参考3的航天 JSON 装的是消费电子/FDA/芯片文章），Claude Code 不会报错——它会编造贴合标签的内容（星舰、火箭方程、月球基地），产出的页面看起来专业但**完全不是来自源数据**。**根因**：CC 被要求"基于 JSON 生成"，JSON 内没有航天内容，CC 选择用自身知识填补而非报告数据问题。**修复**：① 派 CC 前，Hermes 必须对每批 JSON 做 10% 随机抽样——读文章标题+正文首段确认实际主题与标签一致；② 若 CC 生成过程中主动报告数据不匹配（如"这 20 篇文章没有任何一篇是关于航天的"），立即停止、修正数据、重新派发——不要压着 CC 用错误数据生成。**验收**：生成后随机抽查 3-5 篇文章原文，确认页面内容确实引用了原文具体事实/数据/引述，而非泛泛的领域常识。：① 先 `browser_navigate` 看 GitHub Pages 确认页面是否存在；② `search_files` 查本地 repo 目录结构；③ 找独立 JSON 源文件（`find /home/admin -name '*zhuoke*' -name '*.json'`），不要只搜 SQLite。卓克数据不走 Get Notes 管道。：卓克课程数据以独立 JSON 文件存储（如 `/home/admin/zhuoke_techref_notes.json`），不是从 getnote_kb.db 拉取。JSON 中 `course_name` 可能与部署目录名不一致（如 JSON 写"科技参考基础版"，部署为"科技参考3"）。课程按 `chapter_id` 分组（16章），但部署时可能重组合并为 N 个专题。数据映射需从现有 HTML 中提取文章编号再反查 JSON。
- 🔴 **terminal workdir 不接受中文路径**：`terminal(workdir='/含有中文/的路径')` 会被拦截（`Blocked: workdir contains disallowed character`）。改用 `cd /路径 && command` 或省略 workdir 用绝对路径。
- 🔴 **CC 读不了大 JSON → 用模板+Python注入模式**：当实体数据 >25K tokens（~100KB+），Claude Code 读取时会被截断或报错。**让 CC 生成含 `{{DATA_PLACEHOLDER}}` 的 HTML 模板 + Python 注入脚本，CC 自己跑注入后验证**。CC 第一轮常试图自己"提取"数据 → 产出的全是碎片垃圾。完整模式见 `references/cc-template-inject-pattern.md`。
- 🔴 **并行三线 CC 生成视觉变体**：同一数据集的多页面变体可同时启动 3 个 `terminal(background=true, notify_on_complete=true)` CC 进程，每个给不同的视觉方向 brief。三个进程独立运行、独立验证、互不干扰。本 session：Warhol Grid + Mondrian Blocks + Pixel Pop 三线并行。
- 🔴 **Canvas 项目 Claude Code 经典 bug：「定义了零件，忘了组装」**：Claude Code 生成 Canvas 应用时最常见的两类初始化失败：
  1. **布局函数定义了但从未调用**：`generateLayout()` 写了完整实现但脚本末尾忘了调用，导致 `cards` 数组始终为空，整个页面空白。症状：`browser_console` 中手动调用后页面渲染正常。修复：确认所有初始化函数在 `requestAnimationFrame` 之前被显式调用。
  2. **嵌套循环内变量未从数组提取**：外层 `for...of` 把 `nameIdx` 存入中间数组 `names`，内层 `for(let i)` 直接用 `nameIdx` 但从未写 `const nameIdx = names[i]`，产生 `ReferenceError: nameIdx is not defined`。Node `--check` 不报错（因为语法正确、只是运行时变量未定义），必须浏览器实跑才能发现。
  **通用修复流程**：① `browser_console` 手动调用 `generateLayout()` 确认数据逻辑正确；② 检查脚本末尾是否有显式的初始化和 `requestAnimationFrame` 调用链；③ 排查所有 `for...of` 嵌套 `for(let i)` 模式中是否有变量作用域断链。：7 个并行 Claude Code background 进程通常 3-5 个先完成，其余静默退出。重新派发失败的进程后，**第一批中"看起来失败了"的进程可能延迟 10-30 分钟才完成**——此时文件已被后续进程正确重生成并 commit，但迟到进程又用损坏版本覆盖了正确文件。症状：`git status` 在一切"完成"后显示 uncommitted changes，检查发现关键结构（flip-card/staticQuiz/9标签）缺失、Unicode 弯引号大量出现。**修复**：① 所有重生成完成后必须 `git status` 检查残留改动；② 若文件被迟到进程污染，用 `git checkout <last_good_commit> -- <file>` 回退到正确版本；③ 删除迟到进程可能遗留的垃圾文件（如 `test.html`）。**预防**：重生成 prompt 中要求 Claude Code 先检查文件是否已被修改（mtime 晚于派发时间），若是则跳过并报告，避免重复覆盖。

- 🔴 **多个 `<script>` 块之间跨块调用导致 ReferenceError 静默吞没**：当 Clause Code 将页面拆成两个 `<script>` 块时（第一块放数据+renderOverview+SVG，第二块放翻卡+题库+考试），若第一块末尾调用了第二块才定义的函数（如 `updateOverviewProgress()`），浏览器会抛 `ReferenceError` 并**静默终止第一块剩余代码的执行**——SVG 赋值 `document.getElementById('svgContainer').innerHTML = ...` 永远不执行，grep 能搜到 SVG 数据但浏览器中容器为空。诊断：`browser_console` 查 `document.getElementById('svgContainer').innerHTML.length === 0` 但源码有完整 SVG 模板字面量。修复：删除跨块调用的那一行（如删掉第一块末尾的 `updateOverviewProgress()`），该函数在第二块末尾仍有调用。验收：`browser_navigate` 打开页面，确认「概念关系图」区域有 SVG 图形而非空白。① `execute_code` 审计脚本扫描 7 文件 8 维度 → 输出问题矩阵；② 7 个 `terminal(background=true)` 并行 Claude Code，每个读旧 HTML 提取数据 + 读黄金模板套结构；③ 用文件 mtime 判断哪些完成（`os.path.getmtime`），未完成的重新派发；④ 完成一批 commit 一批（已完成的自检 commit，未完成的等重派）；⑤ **最后清理阶段（关键）**：`git status` 检查残留 → 迟到进程覆盖的回退 → 遗留垃圾文件删除 → 最终 `git status` 确认干净。整个流程约 20-40 分钟。硅谷来信3 最终 commit 链：`cc3c186`(2号终版) → `e57520c`(4/5号残留) → `8fbaffd`(1/2/3/7) → ... 共 6 个 commit 覆盖 7 文件。
