# make 原型开发提示词（优化版）

## 总目标

在 `@axhub/make-client` 项目体系内，依据《需求规格说明书》和 `src/themes/port-industry/DESIGN.md`，产出符合项目架构规范、可本地运行、**可直接用于客户演示**的原型。所有页面 1:1 对齐需求说明书「详细功能页面设计」章节的内容，UI 严格匹配 DESIGN.md。

执行采用 **"样板确认 → 批量产出"** 模式。本提示词分为 4 个独立阶段，每阶段结束必须停止，等待用户确认后方可继续。

**客户端演示级标准**：
- 地图组件必须使用高德地图 JS API 真实渲染瓦片+标记点，禁止用图片/占位图/示意图替代
- 所有图表使用 ECharts 真实渲染，含完整 tooltip/图例/维度切换/缩放交互
- 模板编辑器的编辑模式和预览模式必须是**同一套 React 组件的两种状态**——编辑态显示拖拽手柄/网格/属性面板，预览态隐藏编辑 UI，数据布局完全一致
- 所有列表/表格/表单必须有 loading/空状态/错误兜底

---

## 一、技术栈与规范

| 类别 | 选型 |
|------|------|
| 框架 | React 18 + TypeScript（Classic JSX），`@/` → `src/` |
| UI 组件库 | Ant Design 5.x，`ConfigProvider` 统一配置主题 |
| 样式 | Tailwind CSS v4（`@import "tailwindcss";`）+ ConfigProvider Design Token |
| 图标 | `@ant-design/icons` 为主，lucide-react 按需补充 |
| 路由 | 单页：`index.tsx` 直接导出；多页：`useHashPage`（`src/common/useHashPage.ts`） |
| 构建 | Vite 5.4 MPA 模式，入口由 `node scripts/scan-entries.js` 自动注册 |
| 数据 | 原型内 `data/` 目录存放 Mock 数据，处理 loading/空态/失败态 |
| 图表 | echarts 6.x + echarts-for-react 3.x |
| 地图 | 高德地图 JS API，key: `bd77af8b7b13b4c563f7fd06fe4a68c3`，按需懒加载 |
| 画布引擎 | `react-grid-layout`，12 列网格，卡片拖拽/缩放/吸附/不可重叠 |
| 设计基底 | `src/themes/port-industry/DESIGN.md` |
| 富文本 | Markdown 渲染用 `react-markdown` + `remark-gfm` |

### ECharts 基础配置

```ts
grid: { top: 60, bottom: 40, containLabel: true },
legend: { type: 'scroll', top: 0 },
color: ['#406AFF', '#22C55E', '#F59E0B', '#EF4444', '#8B5CF6', '#06B6D4', '#F97316', '#64748B'],
```
```tsx
<ReactECharts option={option} style={{ width: '100%', minHeight: 400 }} />
```

图表类型按 PRD 每个页面指定的类型渲染，PRD 未指定时自行判断。

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

### 布局约束

- 同行卡片内所有图表 `minHeight` 必须相等
- 卡片高度不一时用 `align-items: start`
- 小内容卡片防塌陷：`display: flex; flex-direction: column; justify-content: center;`
- PRD 中一张卡片 = 一个 `Card`。卡片内部可用 `Row/Col` 分栏，但绝不为填满 grid 而把原本属于同一卡片的内容拆到多栏
- 长页面（如分析看板）使用 `overflow-y: auto` + 固定高度容器实现滚动

### 边界

- 不新增需求文档未提及的功能、页面、动效
- 不修改项目全局配置（vite.config.ts、tsconfig.base.json 等）
- 不引入项目未使用的重型框架/路由库/状态管理库

---

## 二、分阶段执行

### 阶段 1：需求读取与核对清单

#### 1.1 分层读取 PRD

按以下优先级读取《需求规格说明书》：

**核心层（必须精读，逐页提取）**：
- 第 1 章 产品概述 → 理解产品边界、目标用户、核心场景
- 第 3 章 系统核心业务流程 → 理解页面间前后关系、业务规则
- 第 5 章 用户体验与页面结构 → 导航结构、跳转关系、**页面清单**
- 第 6 章 页面详细设计 → 每个页面的布局/元素/交互/异常/弹窗

**参考层（暂不读，阶段 3 按触发规则查阅）**：
- 第 2 章 功能需求总览 → 功能范围和权限矩阵
- 第 4 章 功能架构设计 → 模块结构和依赖关系
- 第 7 章 非功能需求 → 性能和安全合规约束
- 第 8 章 核心用例规约 → 完整用例场景

#### 1.2 输出开发核对清单

基于核心层内容输出《开发核对清单》，包含：
- 页面清单（页面名称、对应 PRD 章节号、页面类型）
- 每页的模块/字段/操作点
- 页面跳转关系（pageId 映射表）
- 图表/地图标注（标明每页使用的图表类型和地图组件）
- 对未指定图表类型的数据，按「图表选型」原则标注推荐类型

核对清单输出到 `src/prototypes/<原型名>/.spec/checklist.md`

**pageId 命名规则**：从 PRD 页面清单自动生成，使用小写字母+连字符。如 PC-01 分析看板 → `dashboard`，PC-02 AI 对话 → `ai-chat`，PC-04 模板编辑器 → `template-editor`。

#### 1.3 提取验收检查清单

从 PRD 中提取结构化验收项，输出到 `src/prototypes/<原型名>/.spec/acceptance-checklist.md`：

**提取规则**：

| 来源 | 提取方式 | 验收类型 |
|------|---------|:--:|
| 第 6 章各页面「交互行为表」 | 每条「触发动作 → 系统响应 → 反馈」= 一个验收项 | 交互 |
| 第 6 章各页面「异常处理表」 | 每条「异常场景 → 系统行为 → 用户提示」= 一个验收项 | 异常 |
| 第 8 章「核心用例详情」 | 每条用例的「操作步骤 → 预期结果」= 一个验收项 | 流程 |

**验收清单格式**：

| 编号 | 类型 | 所属页面 | 来源（PRD章节） | 验收步骤 | 预期结果 |
|:----|:---:|---------|---------------|---------|---------|
| AC-001 | 交互 | 分析看板 | 6.1 交互行为表 | 切换日期筛选器 | 全部图表按条件刷新，显示 loading |

**提取要求**：
- 交互行为表的每行都要提取，不合并、不省略
- 异常处理表的每行都要提取，含具体提示文案
- 用例按步骤拆分，每步一个验收项
- 每条标注来源（PRD 章节号），阶段 4 验收失败时可回溯定位

#### 1.4 DESIGN.md 确认

使用 `src/themes/port-industry/DESIGN.md` 作为设计基底。无需重新选择，直接引用。

#### 1.5 依赖安装

新增依赖写入根 `package.json`，`npm install`。当前原型需增加的依赖：
- `react-grid-layout`（画布拖拽引擎）
- `react-markdown` + `remark-gfm`（Markdown 渲染）
- `@amap/amap-jsapi-loader`（高德地图，如有地图页面）

```
🛑 阶段 1 完成。请确认核对清单（含推荐图表类型）、验收检查清单。
```

---

### 阶段 2：样板页面开发

#### 2.1 选择样板页面

优先选择**覆盖面最广的页面**作为样板。推荐优先级：
1. **分析看板**（如果 PRD 有）：覆盖全部图表类型+联动+筛选，一次性验证图表栈
2. 否则选**模板预览页**或**工作台**：覆盖卡片布局+图表+筛选

#### 2.2 完整开发

1. 创建原型目录和文件骨架
2. 搭建 `index.tsx`（ConfigProvider + Token 映射 + useHashPage）
3. **导入 DESIGN.md 中的颜色/字体/间距/圆角 token 作为 CSS 变量或 Tailwind 配置**
4. 完整开发样板页面：功能、字段、交互、图表渲染、loading/空态/失败态
5. 如有地图组件，必须加载高德地图真实渲染
6. `npm run dev` 确认可访问无报错

#### 2.3 样板页面标准

样板页面的完成度 = 后续所有页面的基准线：
- 所有图表真实渲染（非占位图）
- 所有按钮有 hover/active/disabled 三态
- 表格有 loading/空态/错误兜底
- 筛选器切换触发数据刷新
- 图表联动生效

```
🛑 阶段 2 完成。请目视确认样板页面。
```

---

### 阶段 3：批量产出其余页面

#### 3.1 逐页开发

1. **严格复用**样板页面的 ConfigProvider、Design Token 注入方式、图表配置、代码结构
2. 按核对清单逐页开发，每页自检模块完整性
3. 打通页面间导航（useHashPage），补全交互和状态

#### 3.2 模板编辑器的特殊要求（关键）

模板编辑器（如 PRD 中的 PC-04）是交互复杂度最高的页面。必须满足：

**编辑器 = 预览，同一 state**：
```
┌─────────────────────────────────────────┐
│  const [isPreview, setIsPreview] = useState(false)
│  const [canvasItems, setCanvasItems] = useState([])
│  const [filters, setFilters] = useState({})
│
│  // 编辑模式：显示侧边栏 + 画布 + 属性面板 + 拖拽手柄
│  // 预览模式：隐藏编辑 UI，保留同一份 canvasItems + filters
│  // 切换 isPreview 时，canvasItems 和 filters 不变
│  // → 预览看到的就是编辑后的结果
└─────────────────────────────────────────┘
```

**画布引擎**：使用 `react-grid-layout`：
- 12 列网格，`rowHeight={8}`，卡片大小以网格单元为单位
- `onLayoutChange` 持久化卡片位置和尺寸
- `isDraggable={!isPreview}` / `isResizable={!isPreview}`
- `compactType="vertical"` 自动避让，不可重叠
- 预览模式下 `isDraggable={false} isResizable={false}`，保留同一套布局数据

**组件类型**（来自组件库拖入）：
| 组件 | 渲染方式 | 数据 |
|------|---------|------|
| 图表卡片 | ReactECharts | 指标平台数据绑定 |
| 文本块 | react-markdown | Markdown 字符串 |
| 图片 | `<img>` | 上传后的图片 URL |

**属性面板**：选中画布组件时，右侧面板切换对应属性编辑器——
- 图表卡片：标题 Input + 图表类型 Select + 数据指标 Select + 样式开关
- 文本块：Markdown 编辑 TextArea + 预览 Tab 切换
- 图片：图片预览 + 更换图片 Button + 说明文字 Input
- 没选中任何组件：显示 Tab 管理

**图表自适应**：卡片缩放时，监听容器尺寸变化，调用 ECharts `resize()`。文本块内容自适应容器大小。

**右键菜单**：编辑属性 / 复制 / 删除 / 置于顶层 / 置于底层

#### 3.3 参考层触发查阅规则

逐页开发过程中，仅在以下明确条件触发时查阅 PRD 对应章节：

| 触发条件 | 查阅位置 | 目的 |
|---------|---------|------|
| 页面中出现的功能点不在第 5 章页面清单里 | 第 2 章 2.3 功能清单 | 确认该功能是否在本期范围 |
| 页面涉及的角色操作权限不明确 | 第 2 章 2.4 权限矩阵 | 确认当前角色的可见/可操作范围 |
| 页面间的前后依赖关系不明确 | 第 3 章 3.1 业务流程 | 确认流程顺序和上下游 |
| 页面中需要判断的状态值或枚举值不明确 | 第 3 章 3.2 业务节点说明 | 确认状态定义和流转规则 |
| 页面归属哪个一级菜单/模块不明确 | 第 4 章 4.1 功能模块结构 | 确认模块分组 |
| 页面交互涉及跨模块操作 | 第 4 章 4.3 模块依赖关系 | 确认模块间调用关系 |
| 页面需要展示实时数据但刷新频率不明确 | 第 7 章 7.1 性能需求 | 确认刷新频率/加载时间约束 |
| 页面涉及的合规约束不明确 | 第 7 章 7.2 安全合规 | 确认展示层面的合规要求 |
| 页面交互逻辑复杂，仅靠第 6 章交互行为表描述不够 | 第 8 章 用例规约 | 通过完整用例场景补充上下文 |

未触发上述条件时，不读参考层章节，避免上下文噪音。

```
🛑 阶段 3 完成。请目视验收全部页面。
```

---

### 阶段 4：验收与修复

#### 4.1 编译验收（保留）

```bash
node scripts/check-app-ready.mjs /prototypes/<原型名>
```
- status: READY 且控制台无报错
- 编译报错定位修复后重新执行，直到通过

#### 4.2 结构化业务验收

读取阶段 1 生成的 `.spec/acceptance-checklist.md`，逐条核验：

**交互验收项（来源：第 6 章交互行为表）**：
- 在浏览器中实际操作页面，执行验收步骤
- 确认系统响应和反馈方式与 PRD 描述一致
- 记录结果：✅ 通过 / ❌ 未通过（附截图和问题描述）

**异常态验收项（来源：第 6 章异常处理表）**：
- 模拟异常条件（如断网、空数据、权限不足）
- 确认系统行为和用户提示文案与 PRD 描述一致
- 记录结果：✅ 通过 / ❌ 未通过（附截图和问题描述）

**流程验收项（来源：第 8 章用例规约）**：
- 按用例步骤完整走通操作路径
- 确认每一步的预期结果与 PRD 描述一致
- 记录结果：✅ 通过 / ❌ 未通过（附截图和问题描述）

#### 4.3 客户端演示专项检查（新增）

| 检查项 | 要求 |
|-------|------|
| 地图真实渲染 | 高德地图瓦片+标记点真实加载，非图片占位 |
| 图表完整性 | 所有图表 tooltip/图例/缩放可用，窗口 resize 自适应 |
| 编辑器预览一致性 | 编辑模式下修改卡片 → 切换到预览模式 → 布局和内容完全一致 |
| 拖拽流畅度 | react-grid-layout 拖拽/缩放 ≥ 30fps |
| 状态兜底 | 每个列表/表格/图表有 loading/空/错误三态 |

#### 4.4 输出验收报告

生成 `.spec/acceptance-report.md`：

| 编号 | 类型 | 验收步骤 | 预期结果 | 实际结果 | 判定 | 问题截图/说明 |
|:----|:---:|---------|---------|---------|:--:|-------------|
| AC-001 | 交互 | 切换日期筛选器 | 全部图表刷新 | 符合预期 | ✅ | — |
| AC-005 | 异常 | 断网后加载 | 显示"加载失败" | 文案缺失 | ❌ | 截图 |

报告汇总：总验收项数 / 通过数 / 未通过数 + 未通过项明细含修复记录。

#### 4.5 闭环修复

- 未通过项按 PRD 来源定位修复 → 重新执行该项验收
- 循环至全部 ✅
- 人工目视确认（保留，作为最终兜底）

```
🛑 阶段 4 完成。验收报告已输出，全部验收项通过，原型开发结束。
```

---

## 三、验收标准

- `check-app-ready.mjs` 返回 `status: READY`，控制台无报错
- 页面数量、名称、模块、字段与需求文档一致
- 主色、组件风格、间距与 `port-industry/DESIGN.md` 一致
- 搜索/筛选/分页/排序/弹窗/表单校验可用
- loading/空态/错误兜底齐全
- 地图真实渲染，图表完整交互
- 模板编辑器编辑/预览状态一致
- 全部验收检查清单项 ✅

---

## 四、交付物

| 产物 | 位置 |
|------|------|
| 原型源码 | `src/prototypes/<原型名>/` |
| 核对清单 | `src/prototypes/<原型名>/.spec/checklist.md` |
| 验收检查清单 | `src/prototypes/<原型名>/.spec/acceptance-checklist.md` |
| 验收报告 | `src/prototypes/<原型名>/.spec/acceptance-report.md` |
