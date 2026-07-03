# {{项目名称}} — 产品流水线操作指南

> 本文件是产品四阶段流水线的**执行指令集**。每个 Branch 会话打开后，按本文件操作。

## 术语约定

- `/branch`、`/resume` 是**用户在 Claude Code 终端中输入的命令**，AI 不执行这些命令
- AI 的职责是：读状态文件 → 定位上游产物 → 执行当前阶段流程 → 更新状态文件
- AI 在阶段收尾时**提示用户**输入 `/branch` 进入下一阶段，**禁止自行执行**
- "产品设计" = 负责 stage-2 + stage-3 的会话
- "原型开发" = 负责 stage-4 的会话

---

## 零、启动必检（所有 Branch 通用）

打开本会话后的**第一个动作**：

```
步骤 0：定位项目根目录
  → 本文件（CLAUDE.md）所在的目录 = 项目根目录 {PROJECT_ROOT}
  → 记录绝对路径，所有后续文件读写基于此路径
  → 示例：CLAUDE.md 路径为 D:\...\project_cases\智能报表平台\CLAUDE.md
          则 PROJECT_ROOT = D:\...\project_cases\智能报表平台\

步骤 1：读取流水线状态
  → Read {PROJECT_ROOT}/pipeline-status.yaml
  → 若 pipeline.project_root 为空 → 用 Edit 自动填入 {PROJECT_ROOT}
  → 记录 pipeline.updated 时间戳（用于后续写入防护）
  → 了解各 stage 当前状态（done / in_progress / needs_update / not_started）

步骤 2：意图分析 → 阶段判定
  分析用户当前消息的操作意图，映射到流水线阶段：

  | 用户意图关键词 | 对应阶段 | 会话建议 |
  |-------------|---------|---------|
  | 竞品调研、资料收集、搜索竞品 | Stage 1 | Master |
  | PRD、需求文档、功能分析、业务流程、线框图 | Stage 2+3 | 产品设计 |
  | 生成原型、高保真原型、make 原型 | Stage 4 | 原型开发 |
  | 迭代更新、顺流更新、上游改了 | 根据 pipeline-status 判定 | 受影响的下游会话 |

  若意图明确 → 向用户确认：
    "你的操作对应 [Stage X]，负责会话为 [XXX]。
     当前状态：[done/in_progress/not_started]。
     需要创建 /branch [XXX] 吗？"
  
  若意图不明确 → 根据 pipeline-status 当前进度推断：
    - stage-1 not_started → "看起来这是一个新项目，建议从 Stage 1 调研开始。"
    - stage-1 done, stage-2 not_started → "调研已完成，建议进入 Stage 2+3 产品设计。"
    - stage-2+3 done, stage-4 not_started → "产品设计已完成，建议进入 Stage 4 原型开发。"
    - 全部 done → "全流程已完成。如需修改，将作为迭代处理。"
  → 用户确认后再继续。

步骤 3：检查前置条件
  - 产品设计 → stage-1 必须是 done
  - 原型开发 → stage-2 和 stage-3 必须都是 done

步骤 4：检查当前阶段 status
  - needs_update → "上游已变更，是否需要顺流更新下游？"（见第六章）
  - in_progress → "上次在此阶段中断，是否继续未完成的工作？"
  - done → "本阶段已完成。如需修改，将作为迭代处理。"

步骤 5：检查上游文件是否存在（路径有效）

步骤 6：对比 synced_upstream_versions
  → 逐条对比：上游文件的当前 version vs synced_version
  → 当前版本 > synced_version → "上游 {文件} 已更新（v{synced}→v{current}），需要顺流更新"
  → 全部一致 → 继续未完成的工作

步骤 7：输出路径速查
  确认角色后，向用户输出当前项目的关键路径：

  ┌─────────────────────────────────────────────┐
  │ 📁 项目根目录：{PROJECT_ROOT}                │
  │ 📄 流水线状态：{PROJECT_ROOT}/pipeline-status.yaml │
  │ 🎨 设计基底：  {PROJECT_ROOT}/DESIGN.md       │
  │                                              │
  │ 上游产物（从项目根目录读取）：                   │
  │   01-调研与资料/   ← Master 产出               │
  │   02-产品设计/     ← 产品设计 产出              │
  │                                              │
  │ 🔧 make 项目路径：{make_project.path}          │
  │   （独立目录树，与项目根目录不同）               │
  │   状态：{make_project.status}                  │
  └─────────────────────────────────────────────┘
```

---

## 一、项目目录结构

三个关键路径，职责不同，切勿混淆：

```
① 项目根目录 = CLAUDE.md 所在目录
│
├── pipeline-status.yaml    ← 所有 Branch 共享读写的状态文件
├── CLAUDE.md               ← 本文件
├── DESIGN.md               ← 设计基底
├── 01-调研与资料/           ← 上游产物：Master 写入，下游只读
├── 02-产品设计/             ← 上游产物：产品设计 写入，原型开发 只读
├── 03-原型/                 ← 原型开发 写入（指向 make 项目的说明）
└── _迭代日志/               ← 所有 Branch 追加

② make 项目 = 完全独立的目录树（如 D:\AI\workspace\axhub_workspace\znbb2.0\）
│
├── src/prototypes/<原型名>/  ← 原型代码实际存放位置
├── src/themes/               ← 设计主题
└── package.json

③ 上游产物路径 = ① 内的子目录，相对路径
   - 01-调研与资料/竞品调研报告.md
   - 02-产品设计/PRD.md
   - 02-产品设计/页面清单.yaml
   - 02-产品设计/线框图/
```

会话与阶段对应：
| 会话 | 负责阶段 | 读写权限 |
|------|---------|---------|
| Master | stage-1 调研与资料 | 写 `01-调研与资料/`，读无限制 |
| 产品设计 | stage-2 功能分析 + stage-3 线框PRD | 写 `02-产品设计/`，读 `01-调研与资料/` |
| 原型开发 | stage-4 高保真原型 | 写 make 项目 `src/prototypes/`，读 `01-调研与资料/` + `02-产品设计/` |

---

## 二、阶段一：调研与资料收集（Master 执行）

### 2.1 初始化流水线

如果是全新项目：
1. 创建上述目录结构
2. 向用户确认：
   - 产品正式全称、目标行业、客群层级
   - **不需要此时提供 make 项目路径**（Branch B 启动时再向用户要）
3. 写入 pipeline-status.yaml（所有 stages.status = not_started，make_project.status = "pending"）

### 2.2 竞品调研

1. 搜索竞品（WebSearch + WebFetch）
2. 竞品选取原则：
   - 直接竞品：3-5 个（同赛道直接竞争产品）
   - 泛行业标杆：3-5 个（跨行业但有参考价值的顶级产品）
   - 垂直赛道（如港口智能报表）：直接竞品可减少至 2-3 个，补 3-4 个同赛道跨界参考
   - 竞品不足时不强行凑数，标注"该赛道直接竞品有限"
3. 构建功能对比矩阵
4. UML 分析（用例图/流程图/状态图，按需选择）
5. 输出 `01-调研与资料/竞品调研报告.md`（含竞品选取逻辑说明）

调研报告标准结构：
- 行业背景与趋势
- 竞品选取逻辑（为什么选这些）
- 竞品总览表
- 功能基准矩阵
- 竞品详细分析（每家 1-2 页）
- 关键发现与差异化机会

### 2.3 资料归档

- 所有参考链接/截图 → `01-调研与资料/参考资料/`
- 对话中提取的关键信息 → `01-调研与资料/_调研原始记录/`

### 2.4 阶段收尾

1. 更新 pipeline-status.yaml：
```yaml
stages[0].status: "done"
stages[0].outputs[0].version: 1
stages[0].outputs[0].last_modified: "<今天日期>"
pipeline.updated: "<当前时间>"
```
2. 告知用户：
```
阶段一完成。产物：
  - 01-调研与资料/竞品调研报告.md
  - 01-调研与资料/参考资料/

下一步：在终端输入 /branch 产品设计 进入阶段二+三。
```

### 2.5 后续修改调研报告

如果用户回到 Master 修改调研报告：
1. 重复 2.2-2.4
2. 更新 version 号 +1
3. 将 stage-2.status 设为 needs_update
4. 告知用户：调研报告已更新，产品设计和原型开发的下游产物可能需要顺流更新

---

## 三、阶段二：业务功能分析（产品设计 执行 前半段）

### 3.1 读上游

1. 精读 `01-调研与资料/竞品调研报告.md`
2. 更新 pipeline-status.yaml → stage-2.status = in_progress

### 3.2 功能分析（逐步确认，内容写入 PRD）

**3.2.1 产品定位确认**
- 基于调研报告提炼产品定位陈述
- 确认目标用户画像（角色/痛点/频率/关注点）
- 确认核心使用场景（3-5 个）
- 确认项目约束（部署/数据源/AI模型/浏览器/信创）
- → 确认后写入 PRD.md 第 1 章

**3.2.2 功能基准矩阵 + 模块决策**
- 从调研报告提取竞品功能对比
- 逐模块决策：保留/增强/新增/删除，每个决策附理由
- 用户逐项确认
- → 确认后写入 PRD.md 第 2 章

**3.2.3 业务流程 + 术语**
- 核心业务流程图（Mermaid）+ 关键节点说明 + 系统间数据流转
- 核心术语提取，全文统一命名
- 用户确认
- → 确认后写入 PRD.md 第 3 章 + 第 1.5 节

> 注意：本阶段不创建独立文件。所有确认后的内容在阶段三统一写入 PRD.md。

### 3.3 阶段收尾

更新 pipeline-status.yaml：
```yaml
stages[1].status: "done"
stages[1].outputs[*].version: 当前版本号
stages[1].outputs[*].last_modified: "<今天日期>"
stages[1].synced_upstream_versions[0].synced_version: 上游当前版本号
pipeline.updated: "<当前时间>"
```

---

## 四、阶段三：线框图与PRD（产品设计 执行 后半段）

> ⚠️ 重要：stage-2 和 stage-3 同属「产品设计」会话，但版本号互不联动。
> - 仅修改 stage-3（如调线框图布局、不改功能清单）→ 只更新 stage-3.outputs.version，stage-2 不动
> - 修改 stage-2（如新增功能点）→ 更新 stage-2.outputs.version，并强制把 stage-3.status 设为 needs_update

### 4.0 检查跨分支反馈

在开始线框图之前，先检查 `02-产品设计/_待反馈问题.md`：
- 如果有状态为"待处理"的反馈 → 逐条处理后再开始或并行处理
- 「原型开发」可能在上一轮原型开发中发现了线框图设计问题

### 4.1 切换状态

更新 pipeline-status.yaml → stage-3.status = in_progress

### 4.2 页面清单确认（关键卡点）

**4.2.1 输出页面清单**
- 基于功能清单和业务流程
- 每页：编号 / 名称 / 路由 / 类型 / 归属模块 / 核心功能点
- 输出 `02-产品设计/页面清单.yaml`
- 用户确认

**4.2.2 二次确认**
- 告知用户："确认后页面数量不再轻易改动。增加页面会影响原型范围。"
- 确认页面数量、名称、归属、类型

### 4.3 DESIGN.md 确认

- 读项目根目录的 `DESIGN.md`
- 如果没有 → 与用户讨论设计方向，新建
- 如果有 → 确认是否需要调整（线框图阶段是调整 DESIGN.md 的最佳时机）

### 4.4 逐页线框图

使用 Excalidraw 逐页绘制。每页 `.excalidraw` 文件放入 `02-产品设计/线框图/`。

**线框图标准**：
- 24 列 Web 网格系统对齐
- 标注页面编号（PC-XX）
- 标注关键区域名称（如"全局筛选器""核心指标区""趋势分析区"）
- 标注组件类型（图表/表格/输入框/按钮/弹窗触发点）
- 标注数据绑定（该区域显示什么数据，数据来自哪个指标）
- 标注交互行为（点击 → 跳转/弹窗/联动；拖拽 → 新增）
- 标注异常态占位（空状态/加载中/错误/权限不足 + 提示文案）
- 标注弹窗触发入口和弹窗编号（Mxx）

**逐页确认**：每页线框图完成后，用户确认后再画下一页。

#### 4.4.1 格式强制

- 线框图格式**必须**是 `.excalidraw`（JSON 结构化矢量图）
- **严禁**用 `.md` ASCII 伪图替代线框图
- 若用户未安装 Excalidraw 插件，提示用户安装后再绘制

#### 4.4.2 图表区域绘制规范

线框图中的图表区域**无需呈现具体数据图形**：
- ✅ 只画：容器框 + 标题 + 数据源标注 + 占位标记（如 `[ 图表区 · 占位 ]`）
- ❌ 禁止：用 line/rect/ellipse 等元素模拟数据点、柱状条、弧线、折线走向
- 原因：模拟数据图形绘制成本高、折线走向混乱不准确，且线框图阶段不需要确定具体数据可视化形式

#### 4.4.3 逐页确认强制

- **每页线框图完成后必须停止**，等待用户确认后再画下一页
- **严禁**一次性批量输出多页线框图
- 即使页面数量多（如 8+ 页），也必须遵守逐页节奏

### 4.5 PRD 文档生成

**4.5.1 生成完整 PRD.md**
PRD.md 是单一需规文档，所有内容集中于此：
- 第 1 章 → 产品定位 / 用户画像 / 场景 / 约束 / 术语（stage-2 确认的内容）
- 第 2 章 → 功能基准矩阵 / 模块决策表 / 功能清单 / 权限矩阵（stage-2 确认的内容）
- 第 3 章 → 核心业务流程 + 节点说明 + 数据流转（stage-2 确认的内容）
- 第 4 章 → 功能模块结构 + 依赖关系（基于功能清单自动生成）
- 第 5 章 → 导航结构 / 跳转关系 / 页面清单（同时输出 `页面清单.yaml` 供 Branch B 使用）
- 第 6 章 → 每个页面的详细设计。**不重复描述布局**，引用线框图文件路径，只补充交互行为表、异常处理表、弹窗清单
- 第 7 章 → 非功能需求（性能/安全/集成/可用性）
- 第 8 章 → 核心用例规约（从业务流程提取）

**4.5.2 线框图是第 6 章布局的权威源**
- PRD 中不重复描述布局
- PRD 中第 6 章每个页面写："布局参见线框图：`02-产品设计/线框图/PC-XX-名称.excalidraw`"
- 交互行为表和异常处理表由 AI 从线框图标注中提取，写入 PRD

### 4.6 同步校验

逐项检查并输出 `02-产品设计/_同步校验记录.md`：

```
[ ] 线框图页面数量 = 页面清单.yaml 页面数量
[ ] 每页线框图的交互标注 → PRD 第 6 章交互行为表（不遗漏）
[ ] 每页线框图的异常标注 → PRD 第 6 章异常处理表（不遗漏）
[ ] 线框图中的组件类型 → 页面清单中的页面类型（一致）
[ ] 线框图中的页面编号 → 页面清单中的编号（一致）
[ ] PRD 第 1-2 章的功能数量 → 页面清单覆盖的功能数量（一致）
```

### 4.7 阶段收尾

更新 pipeline-status.yaml：
```yaml
stages[2].status: "done"
stages[2].outputs[*].version: 当前版本号
stages[2].outputs[*].last_modified: "<今天日期>"
stages[2].synced_upstream_versions[*].synced_version: 上游当前版本号
pipeline.updated: "<当前时间>"
```

告知用户：
```
阶段二+三完成。产物均在 02-产品设计/ 下：
  - PRD.md                     ← 核心：完整需规文档（8章，功能/流程/术语/交互/异常全含）
  - 页面清单.yaml               ← 原型开发的结构化接口
  - 线框图/PC-01~08.excalidraw  ← 每页的布局权威源
  - _同步校验记录.md

下一步：在终端输入 /branch 原型开发 进入阶段四。
      在此之前，请确保已创建好 make 项目目录。
```

---

## 五、阶段四：高保真原型（原型开发 执行）

### 5.1 获取 make 项目路径

```
步骤 1：检查 pipeline.make_project.status
  - 如果 status = "pending" → 询问用户：
    "请提供 make 项目的根目录路径（绝对路径，如 D:/AI/workspace/axhub_workspace/01moban）。
     如果还没有创建 make 项目，请先创建后再提供路径。"
  - 用户提供路径后：
    ① 验证路径是否存在
    ② 验证 DESIGN.md（make_project.theme 指向的文件）是否存在
    ③ 更新 pipeline-status.yaml：
       make_project.path = 用户提供的路径
       make_project.status = "ready"
       pipeline.updated = 当前时间

步骤 2：确定原型名和目录
  - 原型名 = pipeline.project 的英文 slug（如 "智能报表平台" → "smart-report"）
  - 目标目录 = {make_project.path}/{make_project.prototype_dir}/{原型名}/
  - 创建目标目录（如已存在则提示用户是否覆盖）
```

### 5.1.5 环境依赖预检

> ⚠️ **阻断型门禁**：未通过则不进入编码阶段。

```
步骤 1：读 CLAUDE.md 5.3 技术栈表 → 列出全部运行时依赖清单
步骤 2：逐包验证已安装：
   ls node_modules/<包名>   （如 echarts、echarts-for-react、react-grid-layout、react-markdown、remark-gfm 等）
步骤 3：缺失的统一 npm install 一次性补齐
步骤 4：验证 make_project.theme 指向的 DESIGN.md 文件存在且可读
步骤 5：npm run dev 确认无编译报错（控制台无红色错误）
步骤 6：确认后再开始写原型代码
```

**常见遗漏依赖**（从 _流程问题经验 #5 记录）：
- `echarts` + `echarts-for-react` — 图表
- `react-grid-layout` — 模板编辑器拖拽布局
- `react-markdown` + `remark-gfm` — Markdown 渲染

### 5.2 读上游

**必须全部精读的文件**：
- `02-产品设计/PRD.md` → 完整需规（功能清单→第2章，流程→第3章，术语→第1.5节，交互/异常→第6章）
- `02-产品设计/页面清单.yaml` → 页面列表和路由（Branch B 的结构化接口）
- `DESIGN.md` → Design Token（颜色/字体/圆角/间距/阴影/组件）
- `02-产品设计/_同步校验记录.md` → 了解已知的线框-PRD不一致点
- `02-产品设计/_待反馈问题.md` → 了解是否有待处理反馈

**参考阅读**（按需）：
- `02-产品设计/线框图/` → 逐页布局参考（每页开发时看对应的线框图）

更新 pipeline-status.yaml → stage-4.status = in_progress

### 5.3 技术栈

| 类别 | 选型 |
|------|------|
| 框架 | React 18 + TypeScript（Classic JSX） |
| UI 组件库 | Ant Design 5.x + ConfigProvider |
| 样式 | Tailwind CSS v4 + Design Token CSS变量 |
| 图标 | @ant-design/icons + lucide-react |
| 路由 | useHashPage（src/common/useHashPage.ts） |
| 构建 | Vite 5.4 MPA |
| 图表 | echarts 6.x + echarts-for-react 3.x |
| 地图 | 高德地图 JS API（key: bd77af8b7b13b4c563f7fd06fe4a68c3） |
| 画布引擎 | react-grid-layout（12列网格） |
| 富文本 | react-markdown + remark-gfm |

### 5.4 输出核对清单

**5.4.1 开发核对清单**
基于页面清单.yaml + 线框图 + PRD 交互行为表，输出 `.spec/checklist.md`：
- 页面清单（页面名称/对应PRD章节/页面类型）
- 每页的模块/字段/操作点
- 页面跳转关系（pageId 映射表）
- 图表/地图标注（标明每页使用的图表类型和地图组件）

**5.4.2 验收检查清单**
从 PRD 中提取结构化验收项，输出 `.spec/acceptance-checklist.md`：

| 来源 | 提取方式 | 验收类型 |
|------|---------|:--:|
| PRD 第6章「交互行为表」 | 每行 = 一个验收项 | 交互 |
| PRD 第6章「异常处理表」 | 每行 = 一个验收项 | 异常 |
| PRD 第8章「用例规约」 | 每步骤 = 一个验收项 | 流程 |

验收清单格式：
| 编号 | 类型 | 所属页面 | 来源(PRD章节) | 验收步骤 | 预期结果 |

### 5.5 样板页面开发

1. 选覆盖面最广的页面作为样板（优先分析看板）
2. 创建原型目录和文件骨架
3. 搭建 index.tsx（ConfigProvider + Token映射 + useHashPage）
4. 导入 DESIGN.md 中的颜色/字体/间距/圆角 token
5. 完整开发样板页面：功能/字段/图表/交互/loading/空态/错误态
6. npm run dev 确认可访问无报错
7. **样板页面不是半成品**——完成度 = 后续所有页面的基准线

### 5.5.5 DESIGN.md 合规自检

样板页面完成后，对照 DESIGN.md 的 AI Injection Reference 逐项检查：

1. **布局结构检查**：Header(56px) / Sider(220px) / Content 齐全，层级正确
2. **Token 引用检查**：颜色/字体/间距/圆角/阴影是否引用 DESIGN.md 定义的值
3. **组件模式检查**：StatCard / ChartCard 等 DESIGN.md 约定的组件模式是否正确使用
4. **状态完整性检查**：loading / empty / error / resize 四态

> 完整检查清单：`make-project-template/src/prototypes/_template/.spec/DESIGN_MD_COMPLIANCE_CHECKLIST.md`（26 项，4 层）

### 5.5.6 页面布局硬约束

从多轮迭代中提炼的强制规则：

| # | 规则 | 说明 |
|:--:|------|------|
| 1 | 外层 Layout `height: '100vh'` | 非 minHeight，锁定视口 |
| 2 | 内层 Layout `height: calc(100vh - 56px)` | 填满 Header 下方 |
| 3 | Content `overflow: 'auto'` | 内容独立滚动，Sider 不动 |
| 4 | Sider `overflow: 'auto'` + `height: '100%'` | 长菜单可滚动 |
| 5 | 分析看板 Content padding: 12px | gutter: [8,8]，Card gap: 8px |
| 6 | 面包屑唯一承载页面标题 | 内容区不重复大标题 |
| 7 | 筛选栏: flex + space-between | 左侧筛选，右侧操作，时间用 Segmented |
| 8 | 单子菜单组 → 平级菜单项 | 点击组名直接进入，面包屑不冗余 |
| 9 | 内容少的卡片 `height: '100%'` | 不压缩内容多的卡片去迁就 |

### 5.6 批量产出

1. 严格复用样板页面的结构/配置/Token注入
2. 按页面清单逐页开发，每页开发时对照对应线框图
3. 每页自检：元素说明 → 交互行为 → 异常处理 → 弹窗/子页面
4. 打通页面间导航（useHashPage）

**模板编辑器的特殊要求（PC-04）**：
- 编辑/预览同一 state（`const [isPreview, setIsPreview] = useState(false)`）
- react-grid-layout 12 列网格
- 组件类型：图表卡片 + 文本块(Markdown) + 图片
- 属性面板按选中项切换
- isPreview 时隐藏编辑 UI

### 5.7 验收

**5.7.0 流程检查点自检**：
读取 `make-project-template/src/prototypes/_template/.spec/STAGE4_PROCESS_CHECKPOINTS.md` → 逐项确认 12 项全部 ✅

**5.7.1 DESIGN.md 合规终检**：
读取 `make-project-template/src/prototypes/_template/.spec/DESIGN_MD_COMPLIANCE_CHECKLIST.md` → 全量终检 26 项全部 ✅

**5.7.2 编译验收**：
```bash
node scripts/check-app-ready.mjs /prototypes/<原型名>
# 必须返回 status: READY，控制台无报错
```

**5.7.3 业务验收**：逐条跑 acceptance-checklist.md
- 交互项：实际操作 → 确认与 PRD 一致
- 异常项：模拟异常（断网/空数据/权限不足）→ 确认行为和文案一致
- 流程项：按用例步骤走通

**5.7.4 视觉验收**：
- 对照线框图和 DESIGN.md
- 地图真实渲染（非占位图）
- 图表 tooltip/图例/缩放/resize 自适应完整
- 编辑器编辑/预览状态一致
- 每个列表/表格/图表有 loading/空/错误三态

**5.7.5 输出验收报告**：`.spec/acceptance-report.md`

### 5.8 阶段收尾

更新 pipeline-status.yaml：
```yaml
stages[3].status: "done"
stages[3].outputs[*].version: 当前版本号
stages[3].outputs[*].last_modified: "<今天日期>"
stages[3].synced_upstream_versions[*].synced_version: 上游当前版本号
pipeline.updated: "<当前时间>"
```

告知用户：全流程完成。

---

## 六、迭代规则（所有 Branch 通用）

### 6.1 顺流迭代（上游改 → 下游更新）

```
触发方式：用户切到下游 Branch，说 "上游 XXX 改了，更新下游产物"

执行步骤：
1. 读 pipeline-status.yaml → 找到上游 stage 的最新版本号
2. 对比 synced_upstream_versions → 定位变更文件
3. 读上游新文件 → 对比变化内容
4. 根据文件级 downstream_impact 判定影响范围：
   - 文案级 → 搜索替换对应文案
   - 布局级 → 调整组件位置/尺寸
   - 功能级 → 可能需要新增/删除/修改页面组件和交互
5. 输出影响判定给用户确认（或用户直接说"全改"跳过确认）
6. 执行更新 → 修改下游产物
7. 更新 pipeline-status.yaml：
   - 下游 stage.outputs.version +1
   - synced_upstream_versions 刷新为上游当前版本
   - iterations 追加元数据
   - pipeline.updated 更新
8. 在 _迭代日志/ 下创建详细迭代记录
9. 告知用户更新完成
```

### 6.2 逆向迭代（下游发现问题 → 回溯上游）

```
触发方式：用户在「原型开发」发现 "线框图/PRD 设计不合理"

操作路径：
1. 原型开发 → 写入 02-产品设计/_待反馈问题.md：
   - 问题页面 / 问题描述 / 建议方案 / 发现时间 / 状态 = 待处理
2. /resume 产品设计
3. 产品设计 启动必检 → 发现 _待反馈问题.md 有待处理项
4. 产品设计 处理反馈 → 修改上游产物 → 标记反馈状态 = 已处理
5. 更新 pipeline-status.yaml → 上游 stage.outputs.version++
6. /resume 原型开发
7. "上游已更新，重新读取，顺流更新"
```

### 6.3 最小影响迭代（只改 DESIGN.md）

```
触发方式：用户改了 DESIGN.md

操作：
1. /resume 原型开发
2. "DESIGN.md 更新了，刷新原型视觉样式"
3. 原型开发 读新 DESIGN.md → 更新 Token → 目视确认
4. 不影响功能清单/线框图/PRD
```

### 6.4 迭代记录追加（双轨制）

**pipeline-status.yaml → iterations**（仅存元数据索引）：
```yaml
iterations:
  - id: iter-002
    date: "2026-07-02"
    trigger_stage: "stage-4"
    status: "completed"
    # 详见 _迭代日志/iter-002.md
```

**_迭代日志/<id>.md**（完整详情）：
```markdown
# 迭代记录 iter-002
- **触发环节**：stage-4（原型开发发现 PC-04 拖拽交互设计不合理）
- **触发原因**：自由拖拽+实时碰撞检测实现成本过高
- **改动文件**：
  - 02-产品设计/线框图/PC-04-模板编辑器.excalidraw（简化交互标注）
  - 02-产品设计/PRD.md（更新 PC-04 交互行为表）
  - 03-原型/.../pages/TemplateEditor.tsx（按新线框图更新）
- **下游更新**：无（stage-4 是最终下游）
- **验证结果**：✅ 通过
```

两个位置通过迭代 id 关联，避免重复维护。

### 6.5 迭代发起位置矩阵

| 从哪发起 | 改了什么 | 需要更新 | 操作路径 |
|---------|---------|---------|---------|
| Master | 调研报告新增竞品 | 全链路②③④ | 产品设计 改功能+线框 → 原型开发 改原型 |
| Master | 参考资料补充 | 取决于内容 | 仅产品设计 重读，线框可能不变 |
| 产品设计 | 功能清单增删功能点 | ③④ | 产品设计 更新线框+PRD → 原型开发 更新原型 |
| 产品设计 | 线框图布局调整 | ④ | 直接 原型开发 更新原型（功能清单没变） |
| 产品设计 | PRD 文案/交互表修改 | ④ | 直接 原型开发 更新原型 |
| 原型开发 | 原型样式微调 | 无 | 原型开发 直接改，不回溯上游 |
| 原型开发 | 发现设计不合理 | ③ | 写入 _待反馈问题.md → 产品设计 改线框/PRD → 原型开发 改原型 |
| 全局 | DESIGN.md 改了 | ④ | 直接 原型开发 重读 DESIGN.md 刷新视觉 |

---

## 七、状态文件更新规范

### 7.1 写入前防护（必须执行）

每次修改 pipeline-status.yaml 之前：
```
1. 二次读取文件
2. 对比 pipeline.updated 时间戳：
   - 与本次会话初始读取时一致 → 安全，执行写入
   - 不一致 → 暂停，提示用户：
     "状态文件已被其他会话修改。可能其他 Branch 同时在写。
      上次读取：{时间A}，当前：{时间B}。是否仍然写入？"
3. 用户确认后才写入
```

### 7.2 何时更新

| 事件 | 更新内容 |
|------|---------|
| 开始一个 stage | status → in_progress |
| 完成一个产出文件 | outputs[].version +1, outputs[].last_modified |
| 完成一个 stage | status → done |
| 上游变更影响本 stage | 由上游 Branch 将本 stage.status → needs_update |
| 顺流更新完成 | status → done，synced_upstream_versions 刷新 |
| 完成一次迭代 | iterations 追加元数据（id/date/trigger_stage/status） |
| 任何写入操作 | pipeline.updated 更新为当前时间 |

### 7.3 needs_update 流转规则

```
触发：上游 Branch 修改产物并升级版本号后
  → 遍历 downstream_impact，将受影响的下游 stage.status 设为 needs_update
  → 同会话内：stage-2 修改 → 强制 stage-3.status = needs_update

检测：下游 Branch 启动必检时（见第零章步骤 4）

清除：顺流更新完成后 → status 改回 done
  → 同步刷新 synced_upstream_versions 为上游当前版本号

禁止：任何 Branch 不得在未执行顺流更新的情况下手动把 needs_update 改回 done
```

### 7.4 状态回退规则

```
正向：not_started → in_progress → done
回退：
  in_progress → not_started ：允许（阶段中途作废）
  done → not_started ：禁止（done 后所有修改必须走迭代流程）
  done → needs_update ：允许（仅上游变更触发，非人为回退）
  needs_update → done ：允许（顺流更新完成后）
```

### 7.5 更新方式

- 先用 Read 读完整文件
- 用 Edit 修改对应字段（不整体覆写）
- 禁止修改其他 stage 的 status（例外：上游 Branch 可设下游 status = needs_update）

### 7.6 禁止行为

- 未读 pipeline-status.yaml 就直接写
- 修改不归自己管的 stage 的 status（例外：上游可设下游 needs_update）
- 整体覆写 pipeline-status.yaml（用 Edit 逐字段改）
- 手动把 needs_update 改回 done（必须先执行顺流更新）
