---
name: make-prototype-generator
description: 高保真原型生成器。基于 PRD + 页面清单 + 线框图 + DESIGN.md，在 make 项目体系中生成可交互、可直接用于客户演示的原型代码。采用"样板确认 → 批量产出 → 验收修复"三阶段模式。当用户提到生成原型、高保真原型、make原型、原型开发、原型生成、前端原型等意图时使用此技能。
---

# 高保真原型生成技能

在 `@axhub/make-client` 项目体系内，依据 PRD、页面清单、线框图和 DESIGN.md，产出符合项目架构规范、可本地运行、**可直接用于客户演示**的原型。

## 核心原则

- **模板启动，非从零手写**：从 `make-project-template` 复制原型骨架（`src/prototypes/_template/`），在此基础上定制。不做每个项目从零手写 index.tsx 和 style.css
- **Glass Morphism 设计**：Header/Sider 使用毛玻璃效果（`rgba(255,255,255,0.72)` + `backdrop-filter: blur(12px)`），Content 透明，body 承载几何点阵纹理
- **样板驱动**：先选覆盖面最广的页面完整开发 → 用户确认 → 其余页面严格复用样板模式
- **线框图是布局权威源**：每个页面开发前先读对应线框图，布局以线框图为准
- **流水线集成**：读 pipeline-status.yaml 获取上下文，完成后更新状态文件
- **分阶段确认**：每阶段结束必须停止，等待用户确认后方可继续
- **客户端演示级**：图表真实渲染、所有状态有兜底（loading/空/错误）
- **逆向反馈经审核**：发现上游设计问题时，草拟反馈内容 → 用户确认 → 再写入 `_待反馈问题.md`
- **DESIGN.md 合规自检**：每页输出后跑 26 项合规清单，确保设计一致性

---

## 完整工作流程

```
步骤一：环境准备与上游读取
    ↓
步骤二：核对清单输出 → 用户确认
    ↓
步骤三：样板页面开发 → 用户目视确认
    ↓
步骤四：批量产出其余页面
    ↓
步骤五：验收与修复 → 输出验收报告
```

---

## 步骤一：环境准备与上游读取

### 1.1 读流水线状态

读取 `pipeline-status.yaml`，确认：
- stage-2 和 stage-3 状态为 `done`
- 记录上游文件版本号（用于后续 synced_upstream_versions 对比）
- 记录 `pipeline.updated` 时间戳（用于写入防护）

### 1.2 获取 make 项目路径

检查 `pipeline.make_project.status`：
- 若 `status = "pending"` → 询问用户提供 make 项目根目录的绝对路径
- 验证路径存在（`ls` / `Test-Path`）→ **若路径不存在，必须先向用户确认路径是否正确，不可自行推断原因跳过**
- 验证 `{make_project.path}/{make_project.theme}` 文件存在
- 更新 pipeline-status.yaml：
  ```yaml
  make_project.path = 用户提供的路径
  make_project.status = "ready"
  pipeline.updated = 当前时间
  ```

### 1.3 环境依赖预检 ⚠️ 阻断型门禁

在写任何代码之前，必须完成环境检查：

1. 读 CLAUDE.md 技术栈表 → 列出全部运行时依赖清单
2. 逐包验证：`ls node_modules/<包名>`
3. 缺失的统一 `npm install` 一次性补齐（不要分批次安装）
4. 验证 DESIGN.md 存在且可读
5. `npm run dev` 确认无编译报错

**常见遗漏依赖**：
- `echarts` + `echarts-for-react` — 图表
- `react-grid-layout` — 模板编辑器拖拽布局
- `react-markdown` + `remark-gfm` — Markdown 渲染

> 确认环境就绪后才开始写原型代码。此步骤来源于 _流程问题经验 #4（路径校验）#5（依赖预检）。

### 1.4 从模板创建原型骨架

> **不再从零手写**。从 `make-project-template` 复制骨架：

```
cp -r make-project-template/src/prototypes/_template/ src/prototypes/<原型名>/
```

得到：
```
src/prototypes/<原型名>/
├── index.tsx       # Glass 布局外壳（含 TODO 占位标记）
├── style.css       # CSS Token + 背景纹理（零改动可用）
├── pages/Dashboard.tsx  # 样板分析看板（全模式保留）
├── data/dashboard.ts    # Mock 数据骨架
├── .spec/               # 自检清单
└── README.md
```

### 1.5 定制模板

1. 修改 `index.tsx` → 替换菜单组（`menuItemGroups`）、路由、页面映射、Logo、品牌名、搜索 placeholder
2. 基于 `pages/Dashboard.tsx` 的模式开发各页面

### 1.6 读取上游产物

**必须精读**（直接影响原型开发）：

| 文件 | 用途 | 关键章节 |
|------|------|---------|
| `02-产品设计/页面清单.yaml` | 结构化页面索引 | 全部：编号/名称/路由/页面类型/归属模块/核心功能点 |
| `02-产品设计/PRD.md` | 完整需求规格 | 第 2 章 功能需求总览、第 4 章 系统核心业务流程、第 5 章 页面结构设计、**第 6 章 页面详细设计**（交互行为表 + 异常处理表 + 弹窗清单）、第 8 章 核心业务闭环验证 |
| `02-产品设计/_同步校验记录.md` | 已知的线框-PRD 不一致点 | 全部 |
| DESIGN.md | 设计基底 Token | 全部：颜色/字体/圆角/间距/阴影/组件规范 |

**按需参考**（每页开发时查阅）：
- `02-产品设计/线框图/PC-XX-名称.excalidraw` → 逐页布局参考。**线框图是布局的权威源**，原型布局必须对齐线框图
- PRD 第 1 章 → 产品边界和目标用户（理解上下文）
- PRD 第 3 章 → 功能架构和模块依赖关系
- PRD 第 7 章 → 性能/安全合规约束

> 注意：原"分层读取"机制已简化。Branch B 的上下文只包含原型开发工作，上游内容全部通过文件传递，不需要再区分"核心层/参考层"。以上"按需参考"的章节仅在具体开发遇到不明确问题时查阅，不做全量精读。

### 1.5 读取跨分支反馈

读取 `02-产品设计/_待反馈问题.md`，了解是否有上一轮迭代遗留的待处理反馈。

### 步骤一产出
- make 项目路径已确认
- 上游产物全部读完
- 对 PRD 内容有不明确的点在对话中与用户确认

---

## 步骤二：核对清单输出

### 2.1 输出开发核对清单

基于 `页面清单.yaml` + PRD 第 6 章，输出 `.spec/checklist.md`：

```markdown
# 开发核对清单

## 页面清单
| 编号 | 页面名称 | pageId | 页面类型 | 归属模块 | PRD 章节 |
|------|---------|--------|:-------:|---------|---------|

## 每页详情
### PC-XX 页面名称
- **路由**：...
- **模块/区域**：...
- **图表**：...（类型 + 数据绑定）
- **地图**：是/否（如高德地图）
- **操作点**：...
- **关联弹窗**：...
```

**pageId 命名规则**：从页面清单.yaml 的编号自动生成，小写字母 + 连字符。如 PC-01 分析看板 → `dashboard`，PC-02 AI 对话 → `ai-chat`。

### 2.2 输出验收检查清单

从 PRD 中提取结构化验收项，输出 `.spec/acceptance-checklist.md`：

**提取规则**：

| 来源 | 提取方式 | 验收类型 |
|------|---------|:--:|
| PRD 第 6 章各页面「交互行为表」 | 每条「触发动作 → 系统响应 → 反馈」= 一个验收项 | 交互 |
| PRD 第 6 章各页面「异常处理表」 | 每条「异常场景 → 系统行为 → 用户提示」= 一个验收项 | 异常 |
| PRD 第 8 章「闭环详情」 | 每条闭环的「操作步骤 → 预期结果」= 一个验收项 | 流程 |

**验收清单格式**：

| 编号 | 类型 | 所属页面 | 来源（PRD章节） | 验收步骤 | 预期结果 |
|:----|:---:|---------|---------------|---------|---------|
| AC-001 | 交互 | 分析看板 | 6.1 交互行为表 | 切换日期筛选器 | 全部图表按条件刷新，显示 loading |

**提取要求**：
- 交互行为表的每行都要提取，不合并、不省略
- 异常处理表的每行都要提取，含具体提示文案
- 闭环按步骤拆分，每步一个验收项
- 每条标注来源（PRD 章节号），验收失败时可回溯定位

### 2.3 DESIGN.md 设计基底确认

从 `{make_project.path}/{make_project.theme}` 读取 DESIGN.md。向用户确认是否沿用此设计基底，无需重新选择。

> DESIGN.md 的路径从 pipeline-status.yaml 的 `make_project.theme` 读取，不硬编码。

### 2.4 依赖安装

检查 make 项目根 `package.json`，对比当前原型所需依赖：

- `react-grid-layout` — 画布拖拽引擎（仅当页面清单中有画布/编辑器类型页面时需要）
- `react-markdown` + `remark-gfm` — Markdown 渲染（仅当有富文本/Markdown 展示需求时需要）
- `@amap/amap-jsapi-loader` — 高德地图（仅当页面清单中有地图组件时需要）

**原则**：已安装的不重复添加，不需要的不装。检查后列出需新增的依赖，用户确认后 `npm install`。

```
🛑 步骤二完成。请确认核对清单（含推荐图表类型）、验收检查清单。
```

---

## 步骤三：样板页面开发

### 3.1 选择样板页面

优先选择**覆盖面最广的页面**作为样板。推荐优先级：
1. **分析看板**（如果页面清单中有）：覆盖全部图表类型 + 联动 + 筛选，一次性验证图表栈
2. 否则选**模板预览页**或**工作台**：覆盖卡片布局 + 图表 + 筛选

### 3.2 骨架已就绪

步骤一已从 `make-project-template/src/prototypes/_template/` 复制了完整的原型骨架，无需重新创建目录结构。`index.tsx` 已包含 Glass 布局（Header/Sider/Content）+ `useHashPage` 路由 + 菜单折叠 + 布局切换（side↔top）。

### 3.3 定制布局外壳（如未在步骤一完成）

### 3.4 完整开发样板页面

1. 在 Pro 布局骨架基础上开发页面内容
2. **对照线框图**开发页面布局（线框图是布局权威源）
3. 对照 PRD 第 6 章该页面的元素说明表 / 交互行为表 / 异常处理表逐项实现
4. 完整开发：功能 / 字段 / 图表 / 地图 / 交互 / loading / 空态 / 错误态
5. `npm run dev` 确认可访问无报错

### 3.5 样板页面标准（= 后续所有页面基准线）

- Glass 布局：Header/Sider 毛玻璃（`rgba(255,255,255,0.72)` + `blur(12px)`）+ body 点阵纹理
- Content: padding 12px（side 模式）/ `12px 32px`（top 模式）
- 卡片：白底不透明、6px 圆角、无边框
- KPI 卡片：`<Statistic>` 26px/600 + 迷你 ECharts 36px
- 所有图表真实渲染（非占位图）
- 筛选栏：flex + space-between + Segmented 时间切换
- loading / 空态 / 错误兜底齐全
- 图表 tooltip/图例/resize 自适应
- 单子菜单组折叠为平级项

```
🛑 步骤三完成。请目视确认样板页面。
```

---

## 步骤四：批量产出其余页面

### 4.1 页面类型 → 模板匹配

在逐页开发前，先根据 PRD 和页面清单判断每页的类型，匹配 DESIGN.md 中定义的标准模板：

| 页面类型 | 模板 | 核心结构 |
|---------|------|---------|
| 分析 / 仪表盘 / 总览 / 监控 / 看板 | **分析页模板** | 统计卡片行（Row > Col span=6）→ Tab 复合卡片（trend 16 + rank 8） → 明细表格 |
| 列表 / 管理 / 查询 / 搜索 | **列表页模板** | 筛选栏 Card → 操作栏 → Table → Pagination |
| 设置 / 配置 / 新建 / 编辑 | **表单页模板** | Form Card（分组 + Divider） → sticky 底部提交栏 |
| 详情 / 预览 | 列表页变体 | 单 Card、无筛选栏、面包屑返回 |
| 对话 / 聊天 | 自定义 | 不套模板，按 PRD 设计 |
| 编辑器 / 画布 | 自定义 | 不套模板，按 PRD 设计 |

详细模板结构参见 DESIGN.md「Page Templates」章节和 AI Injection Reference 第 4 节。

### 4.2 逐页开发流程

对页面清单中除样板页面外的每个页面：

1. **读对应线框图**：`02-产品设计/线框图/PC-XX-名称.excalidraw`
2. **严格复用**样板页面的 ConfigProvider、Design Token 注入方式、图表配置、代码结构
3. 对照 PRD 第 6 章该页面的元素说明表 / 交互行为表 / 异常处理表逐项实现
4. 打通页面间导航（useHashPage），补全交互和状态
5. 每页自检：元素完整 → 交互正确 → 异常兜底 → 弹窗完备

### 4.2 模板编辑器特殊要求（如页面清单中有 PC-04 或画布类页面）

**编辑器 = 预览，同一 state**：

```
const [isPreview, setIsPreview] = useState(false)
const [canvasItems, setCanvasItems] = useState([])
const [filters, setFilters] = useState({})

// 编辑模式：显示侧边栏 + 画布 + 属性面板 + 拖拽手柄
// 预览模式：隐藏编辑 UI，保留同一份 canvasItems + filters
// 切换 isPreview 时，canvasItems 和 filters 不变
// → 预览看到的就是编辑后的结果
```

**画布引擎**：使用 `react-grid-layout`：
- 12 列网格，`rowHeight={8}`，卡片大小以网格单元为单位
- `onLayoutChange` 持久化卡片位置和尺寸
- `isDraggable={!isPreview}` / `isResizable={!isPreview}`
- `compactType="vertical"` 自动避让，不可重叠

**组件类型**（来自组件库拖入）：
| 组件 | 渲染方式 | 数据 |
|------|---------|------|
| 图表卡片 | ReactECharts | 指标数据绑定 |
| 文本块 | react-markdown | Markdown 字符串 |
| 图片 | `<img>` | 上传后的图片 URL |

**属性面板**：选中画布组件时，右侧面板切换对应属性编辑器：
- 图表卡片：标题 Input + 图表类型 Select + 数据指标 Select + 样式开关
- 文本块：Markdown 编辑 TextArea + 预览 Tab 切换
- 图片：图片预览 + 更换图片 Button + 说明文字 Input
- 未选中时：显示 Tab 管理

**图表自适应**：卡片缩放时监听容器尺寸变化，调用 ECharts `resize()`。

**右键菜单**：编辑属性 / 复制 / 删除 / 置于顶层 / 置于底层

### 4.3 按需查阅参考章节

逐页开发过程中，仅在以下明确条件触发时查阅 PRD 对应章节：

| 触发条件 | 查阅位置 | 目的 |
|---------|---------|------|
| 页面中出现的功能点不在页面清单里 | PRD 第 2 章 功能清单 | 确认该功能是否在本期范围 |
| 页面涉及的角色操作权限不明确 | PRD 第 2 章 权限矩阵 | 确认当前角色的可见/可操作范围 |
| 页面间的前后依赖关系不明确 | PRD 第 4 章 业务流程 | 确认流程顺序和上下游 |
| 页面中需要判断的状态值不明确 | PRD 第 4 章 业务节点说明 | 确认状态定义和流转规则 |
| 页面归属哪个一级菜单/模块不明确 | PRD 第 3 章 功能模块结构 | 确认模块分组 |
| 页面交互涉及跨模块操作 | PRD 第 3 章 模块依赖关系 | 确认模块间调用关系 |
| 页面需要展示实时数据但刷新频率不明确 | PRD 第 7 章 性能需求 | 确认刷新频率/加载时间约束 |
| 页面涉及的合规约束不明确 | PRD 第 7 章 安全合规 | 确认展示层面的合规要求 |
| 页面交互逻辑复杂，仅靠第 6 章不够 | PRD 第 8 章 闭环验证 | 通过端到端流程补充上下文 |

未触发上述条件时，不读参考章节，避免上下文噪音。

```
🛑 步骤四完成。请目视验收全部页面。
```

---

## 步骤五：验收与修复

### 5.0 流程检查点自检

读取 `STAGE4_PROCESS_CHECKPOINTS.md`（12 项 3 阶段），逐项确认全部 ✅

### 5.1 DESIGN.md 合规终检

读取 `DESIGN_MD_COMPLIANCE_CHECKLIST.md`（26 项 4 层），全量终检全部 ✅

### 5.2 编译验收

```bash
node scripts/check-app-ready.mjs /prototypes/<原型名>
```

- status: READY 且控制台无报错
- 编译报错定位修复后重新执行，直到通过

### 5.3 结构化业务验收

读取 `.spec/acceptance-checklist.md`，逐条核验：

**交互验收项**（来源：PRD 第 6 章交互行为表）：
- 在浏览器中实际操作页面，执行验收步骤
- 确认系统响应和反馈方式与 PRD 描述一致
- 记录结果：✅ 通过 / ❌ 未通过（附截图和问题描述）

**异常态验收项**（来源：PRD 第 6 章异常处理表）：
- 模拟异常条件（如断网、空数据、权限不足）
- 确认系统行为和用户提示文案与 PRD 描述一致
- 记录结果：✅ 通过 / ❌ 未通过（附截图和问题描述）

**流程验收项**（来源：PRD 第 8 章闭环验证）：
- 按闭环步骤完整走通操作路径
- 确认每一步的预期结果与 PRD 描述一致
- 记录结果：✅ 通过 / ❌ 未通过（附截图和问题描述）

### 5.4 客户端演示专项检查

| 检查项 | 要求 |
|-------|------|
| 地图真实渲染 | 高德地图瓦片 + 标记点真实加载，非图片占位 |
| 图表完整性 | 所有图表 tooltip / 图例 / 缩放可用，窗口 resize 自适应 |
| 编辑器预览一致性 | 编辑模式下修改卡片 → 切换到预览模式 → 布局和内容完全一致 |
| 状态兜底 | 每个列表/表格/图表有 loading / 空 / 错误三态 |

### 5.5 输出验收报告

生成 `.spec/acceptance-report.md`：

| 编号 | 类型 | 验收步骤 | 预期结果 | 实际结果 | 判定 | 问题截图/说明 |
|:----|:---:|---------|---------|---------|:--:|-------------|
| AC-001 | 交互 | 切换日期筛选器 | 全部图表刷新 | 符合预期 | ✅ | — |
| AC-005 | 异常 | 断网后加载 | 显示"加载失败" | 文案缺失 | ❌ | 截图 |

报告汇总：总验收项数 / 通过数 / 未通过数 + 未通过项明细含修复记录。

### 5.6 闭环修复

- 未通过项按 PRD 来源定位修复 → 重新执行该项验收
- 循环至全部 ✅

### 5.7 发现设计问题时的处理

若在开发或验收过程中发现上游设计（PRD/线框图）不合理：

1. **草拟反馈内容**（FB-XXX 格式）：问题页面 / 问题描述 / 建议方案
2. **提交用户审核**，等待用户确认后再写入 `02-产品设计/_待反馈问题.md`
3. 反馈状态 = 待处理，供后续产品设计会话处理

```
🛑 步骤五完成。验收报告已输出，全部验收项通过，原型开发结束。
```

---

## 步骤六：阶段收尾

### 6.1 更新流水线状态

更新 `pipeline-status.yaml`：

```yaml
stages[3].status: "done"
stages[3].outputs[0].version: 当前版本号 +1
stages[3].outputs[0].last_modified: "<今天日期>"
stages[3].synced_upstream_versions[0].synced_version: 页面清单当前版本号
stages[3].synced_upstream_versions[1].synced_version: PRD 当前版本号
pipeline.updated: "<当前时间>"
```

### 6.2 告知用户

```
全流程完成。产物：
  - make 项目 src/prototypes/<原型名>/ 下全部原型代码
  - .spec/checklist.md          ← 开发核对清单
  - .spec/acceptance-checklist.md ← 验收检查清单
  - .spec/acceptance-report.md   ← 验收报告
```

---

## 技术栈与规范

| 类别 | 选型 |
|------|------|
| 框架 | React 18 + TypeScript（Classic JSX），`@/` → `src/` |
| UI 组件库 | Ant Design 6.x + ConfigProvider，**glass morphism 毛玻璃主题** |
| 布局 | `<Layout>` + `<Layout.Sider>`(glass) + `<Layout.Header>`(glass 56px) + `<Layout.Content>`(transparent) |
| 背景 | body 几何点阵纹理（`radial-gradient` 20px网格）+ 蓝色椭圆渐变 |
| 样式 | Tailwind CSS v4（`@import "tailwindcss";`）+ CSS 自定义属性 |
| 图标 | `@ant-design/icons` 为主，lucide-react 按需补充 |
| 路由 | 多页原型使用 `useHashPage`（`src/common/useHashPage.ts`），URL 格式 `#page=<id>` |
| 构建 | Vite 5.4 MPA 模式，入口由 `node scripts/scan-entries.js` 自动注册 |
| 数据 | 原型内 `data/` 目录存放 Mock 数据 |
| 图表 | echarts 6.x + echarts-for-react 3.x |
| 地图 | 高德地图 JS API，key: `bd77af8b7b13b4c563f7fd06fe4a68c3`，按需懒加载 |
| 画布引擎 | `react-grid-layout`，12 列网格 |
| 富文本 | `react-markdown` + `remark-gfm` |
| 设计基底 | 路径从 `pipeline-status.yaml → make_project.theme` 读取 |

### ECharts 基础配置

```ts
const BASE_CHART_OPTION = {
  grid: { top: 40, bottom: 24, left: 0, right: 0, containLabel: true },
  legend: { bottom: 0 },
  color: ['#1677FF', '#52C41A', '#FAAD14', '#FF4D4F', '#722ED1', '#13C2C2', '#FA8C16', '#8C8C8C'],
  tooltip: { backgroundColor: 'rgba(0,0,0,0.75)', borderWidth: 0, textStyle: { color: '#fff', fontSize: 12 } },
};
```

```tsx
<ReactECharts option={option} style={{ height: 280 }} />
{/* 图表高度按类型分层：迷你图 36px / 饼图 200px / 柱状 250px / 趋势 280px / 复杂 320px+。详见 DESIGN.md chart-container */}
```

图表类型按 PRD 第 6 章每个页面指定的类型渲染，PRD 未指定时按 DESIGN.md 图表选型表自行判断。

### 图表自适应（关键）

所有图表容器必须绑定 resize 事件：

```tsx
const chartRef = useRef<ReactECharts>(null);
useEffect(() => {
  const handleResize = () => chartRef.current?.getEchartsInstance()?.resize();
  window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize);
}, []);
```

在模板编辑器中，卡片缩放时调用对应图表实例的 `resize()`。

### ⚠️ position: sticky 失效警告

当父容器设置了 `overflow: auto` / `overflow: hidden` / `overflow: overlay` 时，子元素的 `position: sticky` 会定位异常（sticky 依赖滚动容器，overflow 会截断 sticky 的滚动上下文）。

典型场景：Dashboard 页面中固定筛选栏。若外层容器用 `overflow-y: auto` 实现滚动，筛选栏的 sticky 会失效。

解决方案：
- 方案 A：将 sticky 元素移到 overflow 容器外部
- 方案 B：改用 `position: fixed` + 手动计算偏移（需处理 z-index 层级）
- 方案 C：用 JS 监听滚动，动态切换 `position` 和 `top` 值

开发时遇到 sticky 场景需主动检查父容器是否有 overflow 属性。

### 地图强制要求

```tsx
// 高德地图必须真实渲染，禁止静态图片/占位图
import AMapLoader from '@amap/amap-jsapi-loader';

// 按需懒加载，仅在包含地图的页面加载
const AMap = await AMapLoader.load({
  key: 'bd77af8b7b13b4c563f7fd06fe4a68c3',
  version: '2.0',
});
```

地图必须包含：真实瓦片图层 + 至少 1 个标记点(Marker) + 缩放控件。如 PRD 要求热力图/区域着色，必须实现对应图层。

### 原型目录

```
src/prototypes/<原型名>/
├── index.tsx       # 必需，export default，顶部 /** @name xxx */
├── style.css       # 以 @import "tailwindcss"; 开头
├── components/     # 原型内共享组件
├── pages/          # 多页面原型的页面组件
├── data/           # Mock 数据
├── .spec/          # 核对清单、验收报告
└── assets/         # 素材
```

### 复用机制

**从模板启动（新项目）**：
```
cp -r make-project-template/src/prototypes/_template/ src/prototypes/<原型名>/
```

**样板页面开发完成后，后续页面严格复用**：

| 复用项 | 方式 |
|--------|------|
| Glass 布局骨架 + background 纹理层 | 直接从 `_template/index.tsx` 复制，替换菜单和页面映射 |
| ConfigProvider + Menu Token | 直接从样板复制，不改 Token 值 |
| style.css | 直接复用（`:root` 变量 + 点阵纹理 + top-nav-menu 样式） |
| StatCard / ChartCard 组件模式 | 直接从样板复制函数组件定义 |
| ECharts BASE_GRID + useMemo 模式 | 复用常数 + option 构建模式 |
| 筛选栏 flex + Segmented 模式 | 复用结构 |
| 卡片等高 `height: '100%'` 规则 | 内容少的卡加 height |
| 异常卡片 flex 列布局 | 复用 Card flex + body flex:1 模式 |
| Mock 数据结构 | 复用 data/ 目录结构和命名约定 |

**不做**：每个项目从零手写 index.tsx 和 style.css。模板骨架就是起点。

### 布局硬约束

从多轮迭代中提炼的强制规则（任何页面必须遵守）：

| # | 规则 | 说明 |
|:--:|------|------|
| 1 | 外层 Layout `height: '100vh'` | 非 minHeight，锁定视口 |
| 2 | 内层 Layout `height: calc(100vh - 56px)` | 填满 Header 下方 |
| 3 | Content `overflow: 'auto'` | 内容独立滚动，Sider 不动 |
| 4 | Sider `overflow: 'auto'` + `height: '100%'` | 长菜单可滚动 |
| 5 | 分析看板 padding: 12px, gutter [8,8], gap 8px | 紧凑间距 |
| 6 | 面包屑唯一承载页面标题 | 内容区不重复大标题 |
| 7 | 筛选栏: flex + space-between | 左侧筛选，右侧操作，时间用 Segmented |
| 8 | 单子菜单组 → 平级菜单项 | 点击组名直接进入，面包屑不冗余 |
| 9 | 内容少的卡片 `height: '100%'` | 不压缩内容多的卡片去迁就 |

### DESIGN.md 合规自检

每页输出后，对照 `DESIGN_MD_COMPLIANCE_CHECKLIST.md`（26 项 4 层）逐项检查：
- T1 布局结构（5 项）
- T2 Token 合规（10 项）
- T3 组件模式（7 项）
- T4 状态完整性（4 项）

> 完整清单：`make-project-template/src/prototypes/_template/.spec/DESIGN_MD_COMPLIANCE_CHECKLIST.md`

### 流程检查点

关键节点执行 `STAGE4_PROCESS_CHECKPOINTS.md`（12 项 3 阶段）：
- 阶段 A（写代码前）：路径校验 Gate + 依赖预检 Gate
- 阶段 B（写代码后）：视口锁定 / DESIGN.md 合规 / 视觉密度 / 卡片等高 / 筛选栏 / 菜单折叠 / 布局切换
- 阶段 C（读上游时）：线框图格式 / 逐页确认 / 过度绘制

> 完整清单：`make-project-template/src/prototypes/_template/.spec/STAGE4_PROCESS_CHECKPOINTS.md`

### 边界

- 不新增需求文档未提及的功能、页面、动效
- 不修改项目全局配置（vite.config.ts、tsconfig.base.json 等）
- 不引入项目未使用的重型框架/路由库/状态管理库

---

## 验收标准

- `check-app-ready.mjs` 返回 `status: READY`，控制台无报错
- 页面数量、名称、模块与 `页面清单.yaml` 一致
- **Glass 布局**：Header/Sider 毛玻璃（`blur(12px)`）+ Content 透明 + body 点阵纹理
- 主色、圆角、字号与 DESIGN.md 一致（`#1677FF` / 6px / 14px）
- **分析页结构**（如有）：筛选栏 → KPI 卡片行 → 趋势图 + 进度 → 分布图 → 热力图 + 对比/异常 → 趋势 + 雷达
- KPI 卡片：`<Col span={4}>`、`<Statistic>` 26px/600、迷你折线图 36px
- 筛选栏：flex + space-between + Segmented 时间切换
- loading / 空态 / 错误兜底齐全
- 图表完整交互（tooltip / 图例 / resize 自适应）
- **流程检查点 12 项全部 ✅**
- **DESIGN.md 合规 26 项全部 ✅**
- 全部验收检查清单项 ✅

---

## 交付物

| 产物 | 位置 |
|------|------|
| 原型源码 | `src/prototypes/<原型名>/` |
| 开发核对清单 | `src/prototypes/<原型名>/.spec/checklist.md` |
| 验收检查清单 | `src/prototypes/<原型名>/.spec/acceptance-checklist.md` |
| 验收报告 | `src/prototypes/<原型名>/.spec/acceptance-report.md` |
