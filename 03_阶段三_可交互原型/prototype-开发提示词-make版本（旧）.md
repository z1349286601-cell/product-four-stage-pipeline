# 总目标

在 `@axhub/make-client` 项目体系内工程化执行：依据《需求规格说明书》和已确认的 `DESIGN.md`，产出符合项目架构规范、可本地运行的原型。所有页面 1:1 对齐需求说明书「详细功能页面设计」章节的功能、字段、模块和交互逻辑，UI 严格匹配 `DESIGN.md`。

执行采用 **"样板确认 → 批量产出"** 模式：仅首屏/核心页面需用户目视确认，确认后以此为基准批量生成其余页面，最终通过项目内置验收脚本全量校验。

> 本提示词分为 4 个独立阶段。每阶段结束必须停止，等待用户确认后方可继续。禁止跨阶段执行。


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

### 图表选型

先判断数据要回答什么问题，再选最自然的表达：

- 排名对比 → 横向柱状图 `xAxis: 'value' + yAxis: 'category'`
- 时序趋势 → 折线图 `line`
- 分组比较 → 纵向柱状图 `bar`
- 占比构成 → 环形图 `pie, radius: ['40%','70%']`
- 二维交叉 → 散点图 `scatter`
- 层级/流向 → 树图 `tree` / 桑基图 `sankey`
- 多指标画像 → 雷达图 `radar`
- 单值状态 → 仪表盘 `gauge`

强制配置：
```ts
grid: { top: 60, bottom: 40, containLabel: true },
legend: { type: 'scroll', top: 0 },
color: ['#1677ff', '#52c41a', '#faad14', '#ff4d4f', '#4096ff', '#8c8c8c'],
```
```tsx
<ReactECharts option={option} style={{ width: '100%', minHeight: 400 }} />
```

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

### 边界

- 不新增需求文档未提及的功能、页面、动效
- 不修改项目全局配置（vite.config.ts、tsconfig.base.json 等）
- 不引入项目未使用的重型框架/路由库/状态管理库


## 二、分阶段执行

### 阶段 1：需求读取与图表选型

1. 读取《需求规格说明书》「详细功能页面设计」章节
2. 输出《开发核对清单》：页面清单、每页模块/字段/操作点、跳转关系、图表/地图标注
3. **对未指定图表类型的数据，按「图表选型」原则标注推荐类型**
4. DESIGN.md 确认：复用已有 / 从主题库选 3-4 个候选 / 创建新的
5. 新增依赖写入根 `package.json`，`npm install`

```
🛑 阶段 1 完成。请确认核对清单（含推荐图表类型）和 DESIGN.md。
```

### 阶段 2：样板页面开发

1. 创建原型目录和文件骨架
2. 搭建 `index.tsx`（ConfigProvider + Token 映射 + useHashPage）
3. 选定 1 个覆盖面最广的页面作为样板（如仪表盘/列表页）
4. 完整开发：功能、字段、交互、loading/空态/失败态
5. `npm run dev` 确认可访问无报错

```
🛑 阶段 2 完成。请目视确认样板页面。
```

### 阶段 3：批量产出其余页面

1. 严格复用样板页面的 ConfigProvider、样式、代码结构
2. 按核对清单逐页开发，每页自检模块完整性
3. 打通页面间导航（useHashPage），补全交互和状态

```
🛑 阶段 3 完成。请目视验收全部页面。
```

### 阶段 4：验收与修复

- `node scripts/check-app-ready.mjs /prototypes/<原型名>`
- 编译报错定位修复，视觉问题按反馈调整
- 循环至编译通过 + 视觉确认

```
🛑 阶段 4 完成，原型开发结束。
```


## 三、验收标准

- `check-app-ready.mjs` 返回 `status: READY`，控制台无报错
- 页面数量、名称、模块、字段与需求文档一致
- 主色、组件风格、间距与 DESIGN.md 一致，无硬编码色值
- 搜索/筛选/分页/排序/弹窗/表单校验可用，loading/空态/错误兜底齐全

## 四、交付物

| 产物 | 位置 |
|------|------|
| 原型源码 | `src/prototypes/<原型名>/` |
| 核对清单 | `src/prototypes/<原型名>/.spec/checklist.md` |
| 设计快照 | `src/prototypes/<原型名>/.spec/YYYY-MM-DD-<topic>.md` |
| 验收报告 | `src/prototypes/<原型名>/.spec/acceptance.md` |
