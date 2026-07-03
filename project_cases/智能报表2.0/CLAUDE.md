# 智能报表2.0 — 产品流水线操作指南

> 本文件是产品四阶段流水线的**执行指令集**。每个 Branch 会话打开后，按本文件操作。

## 术语约定

- `/branch`、`/resume` 是**用户在 Claude Code 终端中输入的命令**，AI 不执行这些命令
- AI 的职责是：读状态文件 → 定位上游产物 → 执行当前阶段流程 → 更新状态文件
- AI 在阶段收尾时**提示用户**输入 `/branch` 进入下一阶段，**禁止自行执行**

---

## 零、启动必检（所有 Branch 通用）

打开本会话后的**第一个动作**：

步骤 1：Read pipeline-status.yaml（记录 pipeline.updated 时间戳）
步骤 2：判定本 Branch 角色（master > 原型开发 > 产品设计）
步骤 3：检查前置条件
步骤 4：检查自身 status（needs_update / in_progress / done）
步骤 5：检查上游文件是否存在
步骤 6：对比 synced_upstream_versions

---

## 一、项目目录结构

```
智能报表2.0/
├── pipeline-status.yaml
├── CLAUDE.md
├── DESIGN.md
├── 01-调研与资料/
├── 02-产品设计/
├── 03-原型/
└── _迭代日志/
```

| 会话 | 负责阶段 | 目录 |
|------|---------|------|
| Master | stage-1 调研与资料 | 01-调研与资料/ |
| 产品设计 | stage-2 功能分析 + stage-3 线框PRD | 02-产品设计/ |
| 原型开发 | stage-4 高保真原型 | 03-原型/ |

---

## 二、阶段一：调研与资料收集（Master 执行）

1. 创建目录结构，确认产品全称
2. 竞品调研：搜索 → 功能矩阵 → UML → 输出报告
3. 更新 stage-1.status = done，提示 /branch 产品设计

---

## 三、阶段二：业务功能分析（产品设计 前半段）

1. 精读竞品调研报告
2. 产品定位 → PRD Ch1
3. 功能矩阵+模块决策 → PRD Ch2
4. 业务流程+术语 → PRD Ch3-4
5. 更新 stage-2.status = done

---

## 四、阶段三：线框图与PRD（产品设计 后半段）

1. 检查 _待反馈问题.md
2. 输出页面清单.yaml → 用户确认
3. 确认 DESIGN.md
4. 逐页线框图（Excalidraw）：标注区域/组件/数据绑定/交互/异常/弹窗
5. PRD 文档 Ch5-8 + 附录（第6章引用线框图，不重复ASCII布局）
6. 同步校验：输出 _同步校验记录.md
7. 更新 stage-3.status = done，提示 /branch 原型开发

---

## 五、阶段四：高保真原型（原型开发 执行）

### 技术栈
React 18 + TypeScript + Ant Design 5 + Tailwind CSS v4 + ECharts 6 + react-grid-layout + useHashPage

### 流程
1. 获取 make 项目路径 → 更新 pipeline
2. 精读上游：PRD.md / 页面清单.yaml / DESIGN.md / 线框图/
3. 输出 .spec/checklist.md + .spec/acceptance-checklist.md
4. 样板页面开发（PC-01）→ npm run dev 确认
5. 批量产出其余7页 → 对照线框图
6. 验收：编译 + 业务 + 视觉 → .spec/acceptance-report.md
7. 更新 stage-4.status = done

---

## 六、迭代规则

- 顺流：上游改 → 下游 needs_update → 启动必检提示 → 执行更新
- 逆向：下游发现问题 → _待反馈问题.md → 上游处理 → 顺流更新
- 双轨：pipeline iterations[] + _迭代日志/<id>.md

## 七、状态文件更新规范

- 写入前二次读取，对比 pipeline.updated 时间戳
- 状态流转：not_started → in_progress → done
- done → needs_update（仅上游变更触发）
- 禁止手动把 needs_update 改回 done
