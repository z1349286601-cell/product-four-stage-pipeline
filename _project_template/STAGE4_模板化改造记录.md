# Stage 4 模板化改造记录

> 日期：2026-07-03 | 来源项目：集装箱理货系统分析平台 | 改造范围：_project_template + make-project-template

---

## 一、CLAUDE.md 更新（_project_template/CLAUDE.md）

在 Stage 4 章节中新增以下内容：

### 新增 5.1.5：环境依赖预检
- 位置：5.1「获取 make 项目路径」之后，5.2「读上游」之前
- 类型：阻断型门禁
- 内容：
  - 读技术栈表 → 列依赖清单 → 逐包验证已安装
  - 缺失的统一 `npm install` 一次性补齐
  - 验证 DESIGN.md 存在 + `npm run dev` 无报错
  - 常见遗漏依赖：echarts、echarts-for-react、react-grid-layout、react-markdown、remark-gfm
- 来源：_流程问题经验 #5（依赖预检）

### 新增 5.5.5：DESIGN.md 合规自检
- 位置：5.5「样板页面开发」步骤 7 之后
- 内容：样板页面输出后，对照 DESIGN.md 逐项检查（布局/Token/组件模式/状态完整性）
- 引用：`make-project-template/src/prototypes/_template/.spec/DESIGN_MD_COMPLIANCE_CHECKLIST.md`（26 项，4 层）

### 新增 5.5.6：页面布局硬约束
- 位置：5.5.5 之后
- 内容：从多轮迭代中提炼的 9 条强制规则

| # | 规则 |
|:--:|------|
| 1 | 外层 Layout `height: '100vh'`（非 minHeight） |
| 2 | 内层 Layout `height: calc(100vh - 56px)` |
| 3 | Content `overflow: 'auto'` |
| 4 | Sider `overflow: 'auto'` + `height: '100%'` |
| 5 | 分析看板 padding: 12px, gutter: [8,8], gap: 8px |
| 6 | 面包屑唯一承载页面标题 |
| 7 | 筛选栏: flex + space-between + Segmented |
| 8 | 单子菜单组 → 平级菜单项 |
| 9 | 内容少的卡片 `height: '100%'` |

### 增强 5.7：验收
- 新增 5.7.0：流程检查点自检（12 项，3 阶段）
- 新增 5.7.1：DESIGN.md 合规终检（26 项，4 层）
- 原 5.7 内容顺延编号为 5.7.2-5.7.5

---

## 二、make-project-template 创建（平级于 _project_template）

在流水线产品根目录 `产品四阶段流水线优化/make-project-template/` 创建了完整的 make 工程模板。

### 来源
从 `D:\AI\workspace\axhub_workspace\jzxznlhxtfxpt` 整包复制。

### 排除项
| 排除 | 原因 |
|------|------|
| `node_modules/` | 通过 `npm install` 重新安装 |
| `.axhub/` | 本地运行时状态 |
| `.claude/skills/` | 会话特定技能 |
| `src/prototypes/container-tally-analytics/` | 项目特定原型 |

### 保留项（工程基础设施）
`package.json` + `package-lock.json`、`vite.config.ts`、`tsconfig*.json`、`src/common/`、`src/themes/`、`src/preview-templates/`、`src/resources/`、`scripts/`、`vite-plugins/`、`rules/`、`.agents/`、`.gitignore`、`CLAUDE.md`、`AGENTS.md`、`README.md`

### 模板内新建：`src/prototypes/_template/`
从 container-tally-analytics 提炼的通用原型启动骨架：

| 文件 | 说明 |
|------|------|
| `index.tsx` | Glass 布局外壳（ConfigProvider + Header + Sider + Content + useHashPage + 布局切换），菜单/路由/页面映射 → TODO 占位 |
| `style.css` | CSS Token + body 点阵纹理 + antd menu 覆盖样式（零改动可用） |
| `pages/Dashboard.tsx` | 完整分析看板参考（StatCard/ChartCard/筛选栏/9种图表/三态），数据导入 → TODO |
| `data/dashboard.ts` | Mock 数据结构骨架（保留 shape，值通用化） |
| `.spec/DESIGN_MD_COMPLIANCE_CHECKLIST.md` | 26 项 DESIGN.md 合规自检（T1 布局 5 + T2 Token 10 + T3 组件 7 + T4 状态 4） |
| `.spec/STAGE4_PROCESS_CHECKPOINTS.md` | 12 项流程检查点（阶段 A 环境就绪 2 + 阶段 B 每页质量 7 + 阶段 C 上游完整性 3） |
| `.spec/checklist-template.md` | 开发核对清单骨架 |
| `.spec/acceptance-checklist-template.md` | 验收清单骨架 |
| `README.md` | 模板使用说明 |

---

## 三、影响范围

| 文件 | 变更类型 | 位置 |
|------|:------:|------|
| `_project_template/CLAUDE.md` | 修改 | Stage 4 章节新增 4 个子章节 |
| `make-project-template/` | 新建 | 流水线产品根目录，平级于 _project_template |
| `project_cases/集装箱理货系统分析平台/CLAUDE.md` | 修改 | 同步更新（同上内容） |
| `C:\Users\ZZP\.claude\skills\make-prototype-generator\SKILL.md` | 修改 | 同步更新核心原则/技术栈/步骤一/步骤三/布局约束/复用机制/验收标准 |

---

## 四、新项目启动流程

```
Step 1: 复制流水线项目模板
  cp -r _project_template/ project_cases/<新项目名>/

Step 2: 复制 make 工程模板到新目录
  cp -r make-project-template/ D:/AI/workspace/axhub_workspace/<新make项目>/

Step 3: 安装依赖
  cd <新make项目> && npm install

Step 4: 从 _template 创建原型骨架
  cp -r src/prototypes/_template/ src/prototypes/<原型名>/

Step 5: 定制 TODO 占位 → 开发页面 → 跑自检清单
```
