---
version: 3.1
name: antd-pro-design-system
description: Clean enterprise backend design language. White sidebar, white header, standard Ant Design 6 default tokens. Built for data-dense dashboards, list pages, and form pages in a professional client-facing context. StatCard and ChartCard patterns replace ProCard dependency.

colors:
  # ── Brand / Primary (Ant Design 5 default) ──
  primary: "#1677FF"
  primary-hover: "#4096FF"
  primary-active: "#0958D9"
  primary-bg: "#E6F4FF"
  primary-bg-hover: "#BAE0FF"

  # ── Neutral / Text (Ant Design 5 default) ──
  text: "rgba(0,0,0,0.88)"
  text-secondary: "rgba(0,0,0,0.65)"
  text-tertiary: "rgba(0,0,0,0.45)"
  text-quaternary: "rgba(0,0,0,0.25)"
  text-white: "#FFFFFF"
  text-white-secondary: "rgba(255,255,255,0.65)"

  # ── Surface ──
  canvas: "#f0f2f5"
  surface: "#FFFFFF"
  surface-hover: "#FAFAFA"
  surface-active: "#F5F5F5"

  # ── Border ──
  border: "#D9D9D9"
  border-secondary: "#F0F0F0"

  # ── Sidebar (white theme) ──
  sidebar-bg: "#FFFFFF"
  sidebar-hover: "#F5F5F5"
  sidebar-active: "#E6F4FF"
  sidebar-text: "rgba(0,0,0,0.88)"
  sidebar-active-text: "#1677FF"
  sidebar-logo-bg: "#FFFFFF"
  sidebar-logo-text: "#1677FF"

  # ── Header ──
  header-bg: "#FFFFFF"
  header-text: "rgba(0,0,0,0.88)"
  header-shadow: "0 1px 4px rgba(0,0,0,0.08)"

  # ── Semantic ──
  success: "#52C41A"
  success-bg: "#F6FFED"
  success-border: "#B7EB8F"
  warning: "#FAAD14"
  warning-bg: "#FFFBE6"
  warning-border: "#FFE58F"
  error: "#FF4D4F"
  error-bg: "#FFF2F0"
  error-border: "#FFCCC7"
  info: "#1677FF"
  info-bg: "#E6F4FF"
  info-border: "#91CAFF"

  # ── Chart Palette (8 colors) ──
  chart-1: "#1677FF"
  chart-2: "#52C41A"
  chart-3: "#FAAD14"
  chart-4: "#FF4D4F"
  chart-5: "#722ED1"
  chart-6: "#13C2C2"
  chart-7: "#FA8C16"
  chart-8: "#8C8C8C"

typography:
  fontFamily: "-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'PingFang SC', 'Microsoft YaHei', 'Helvetica Neue', sans-serif"
  fontFamilyCode: "'SF Mono', 'JetBrains Mono', SFMono-Regular, Consolas, 'Liberation Mono', monospace"
  fontSize: 14
  fontSizeLG: 16
  fontSizeSM: 12
  fontSizeXL: 20
  fontSizeHeading1: 38
  fontSizeHeading2: 30
  fontSizeHeading3: 24
  fontSizeHeading4: 20
  fontSizeHeading5: 16
  lineHeight: 1.5714
  fontWeight: 400
  fontWeightStrong: 600

rounded:
  xs: 2px
  sm: 4px
  md: 6px     # Ant Design 5 default borderRadius
  lg: 8px
  xl: 12px

spacing:
  xxs: 4px
  xs: 8px
  sm: 12px
  md: 16px
  lg: 24px
  xl: 32px
  xxl: 48px

shadows:
  sm: "0 1px 2px 0 rgba(0,0,0,0.03), 0 1px 6px -1px rgba(0,0,0,0.02), 0 2px 4px 0 rgba(0,0,0,0.02)"
  md: "0 3px 6px -4px rgba(0,0,0,0.12), 0 6px 16px 0 rgba(0,0,0,0.08), 0 9px 28px 8px rgba(0,0,0,0.05)"
  lg: "0 6px 16px -8px rgba(0,0,0,0.08), 0 9px 28px 0 rgba(0,0,0,0.05), 0 12px 48px 16px rgba(0,0,0,0.03)"
  header: "0 1px 4px rgba(0,0,0,0.08)"

components:
  # ── Layout ──
  sidebar:
    width: 220px
    collapsedWidth: 80px
    backgroundColor: "{colors.sidebar-bg}"
    textColor: "{colors.sidebar-text}"
    logoBackground: "{colors.sidebar-logo-bg}"

  sidebar-menu-item:
    height: 40px
    margin: 4px 8px
    padding: 0 16px
    rounded: "{rounded.lg}"
    typography: "{typography.fontSize} / {typography.lineHeight}"
    hoverBackground: "{colors.sidebar-hover}"
    activeBackground: "{colors.sidebar-active}"
    activeTextColor: "{colors.sidebar-active-text}"

  header:
    height: 56px
    backgroundColor: "{colors.header-bg}"
    padding: 0 24px
    boxShadow: "{shadows.header}"
    zIndex: 9

  content:
    backgroundColor: "{colors.canvas}"
    padding: 16px

  # ── Card ──
  card:
    backgroundColor: "{colors.surface}"
    rounded: "{rounded.md}"
    padding: 16px
    border: none
    gap: 12px

  # ── Buttons (Ant Design 5 defaults, listed for reference) ──
  button-primary:
    backgroundColor: "{colors.primary}"
    textColor: "#FFFFFF"
    rounded: "{rounded.md}"
    height: 32px
    padding: 4px 15px

  button-default:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.text}"
    borderColor: "{colors.border}"
    rounded: "{rounded.md}"
    height: 32px
    padding: 4px 15px

  button-danger:
    backgroundColor: "{colors.error}"
    textColor: "#FFFFFF"
    rounded: "{rounded.md}"
    height: 32px
    padding: 4px 15px

  # ── Table ──
  table-header:
    backgroundColor: "#FAFAFA"
    textColor: "{colors.text}"
    fontWeight: 600

  table-row:
    height: 48px

  # ── Input / Select ──
  input:
    backgroundColor: "{colors.surface}"
    borderColor: "{colors.border}"
    rounded: "{rounded.md}"
    height: 32px
    padding: 0 11px

  input-focus:
    borderColor: "{colors.primary}"
    boxShadow: "0 0 0 2px rgba(22,119,255,0.1)"

  # ── Tag ──
  tag:
    rounded: "{rounded.sm}"
    padding: 0 7px
    height: 22px
    fontSize: 12px

  # ── Modal / Drawer ──
  modal:
    rounded: "{rounded.lg}"
    padding: 24px

  drawer:
    width: 600px
    padding: 24px

  # ── Chart Container ──
  chart-container:
    backgroundColor: "{colors.surface}"
    rounded: "{rounded.md}"
    # 图表高度按类型弹性：迷你图 36-60px / 仪表盘 200px / 饼图 200px / 柱状/散点 250px / 趋势线 280px / 复杂图表 320px+

  # ── Filter Bar ──
  filter-bar:
    backgroundColor: "{colors.surface}"
    padding: 16px
    rounded: "{rounded.md}"
    marginBottom: 12px

  # ── StatCard（替代 ProCard StatisticCard，零依赖） ──
  statcard:
    rounded: "{rounded.md}"
    padding: 12px 18px
    valueFontSize: 26px
    valueFontWeight: 600
    valueFontFamily: "Helvetica Neue, -apple-system, sans-serif"
    labelFontSize: 14px
    trendFontSize: 14px
    miniChartHeight: 36px

  stat-trend-up:
    textColor: "{colors.error}"
    icon: "↑"

  stat-trend-down:
    textColor: "{colors.success}"
    icon: "↓"

  # ── ChartCard（替代 ProCard ChartCard，Card + tabList + 图表 + 底部统计） ──
  chartcard:
    rounded: "{rounded.md}"
    padding: 16px
    tabList: "Card 内置 tabList prop"
    chartMinHeight: 280px
    footerMargin: 12px
---

## Overview

Ant Design Pro v3.0 — the enterprise backend design language used by `preview.pro.ant.design`. Dark sidebar, white header, `#f0f2f5` canvas, standard Ant Design 5 default tokens. This is a **zero-override** design system: the Ant Design 5 component defaults ARE the Pro style. We only document the tokens for reference and consistency.

**Key Characteristics:**

- **White sidebar** (`{colors.sidebar-bg}` — #FFFFFF): Clean light theme with `#F0F0F0` right border. Logo area in `{colors.sidebar-active-text}` (#1677FF). 220px default width, light Menu theme.
- **White header** (56px): Solid white with subtle 1px shadow, sits above content. Contains sidebar collapse toggle, breadcrumb, user area.
- **`{colors.canvas}` canvas** (#f0f2f5): The standard content background. 16px padding.
- **Cards**: White surface, `{rounded.md}` (6px) corners, 24px padding, NO border. Clean, minimal elevation.
- **Primary buttons**: Blue (`{colors.primary}` — #1677FF) with 6px radius, 32px height — these are Ant Design 5 defaults.
- **Charts**: Standard 8-color palette, ECharts with Pro-style grid/legend config.

## Colors

### Brand
- **Primary** (`{colors.primary}` — #1677FF): Ant Design 5 default. Primary buttons, links, active states, chart series 1.
- **Primary Hover** (`{colors.primary-hover}` — #4096FF): Hover state.
- **Primary Active** (`{colors.primary-active}` — #0958D9): Press state.
- **Primary BG** (`{colors.primary-bg}` — #E6F4FF): Selected backgrounds.

### Surface
- **Canvas** (`{colors.canvas}` — #f0f2f5): Page background, Pro standard.
- **Surface** (`{colors.surface}` — #FFFFFF): Cards, tables, modals.

### Sidebar (Pro Dark)
- **BG** (`{colors.sidebar-bg}` — #FFFFFF): White sidebar background.
- **Text** (`{colors.sidebar-text}` — `rgba(0,0,0,0.88)`): Menu item text.
- **Active BG** (`{colors.sidebar-active}` — #E6F4FF): Active menu item background.
- **Active Text** (`{colors.sidebar-active-text}` — #1677FF): Active menu item text.
- **Logo BG** (`{colors.sidebar-logo-bg}` — #FFFFFF): Logo area.

### Text
- **Primary** (`{colors.text}` — `rgba(0,0,0,0.88)`): All body text.
- **Secondary** (`{colors.text-secondary}` — `rgba(0,0,0,0.65)`): Labels, descriptions.
- **Tertiary** (`{colors.text-tertiary}` — `rgba(0,0,0,0.45)`): Disabled, placeholders.

### Semantic
- **Success** (`{colors.success}` — #52C41A): Green.
- **Warning** (`{colors.warning}` — #FAAD14): Amber.
- **Error** (`{colors.error}` — #FF4D4F): Red.

### Chart Palette
1. `{colors.chart-1}` (#1677FF) — Blue
2. `{colors.chart-2}` (#52C41A) — Green
3. `{colors.chart-3}` (#FAAD14) — Amber
4. `{colors.chart-4}` (#FF4D4F) — Red
5. `{colors.chart-5}` (#722ED1) — Violet
6. `{colors.chart-6}` (#13C2C2) — Cyan
7. `{colors.chart-7}` (#FA8C16) — Orange
8. `{colors.chart-8}` (#8C8C8C) — Gray

## Typography

### Font Family
System font stack → PingFang SC (macOS) / Microsoft YaHei (Windows). Code uses SF Mono → JetBrains Mono.

### Hierarchy

| Token | Size | Weight | Use |
|-------|:----:|:------:|-----|
| Heading 1 | 38px | 600 | Page title (rare) |
| Heading 2 | 30px | 600 | Section title (rare) |
| Heading 3 | 24px | 600 | Card group title |
| Heading 4 | 20px | 600 | Card title |
| Heading 5 | 16px | 600 | Subsection title |
| Body | **14px** | 400 | **Default** — all body text, table data, form labels |
| Small | 12px | 400 | Help text, captions, tags |

### Principles
- Default font size: **14px**. Never use 16px for body text in enterprise dashboards.
- Max weight: 600 for Chinese. No 700+ in CJK contexts.
- Line height: 1.5714 (Ant Design default).
- Font weight for numbers in stat cards: 600 (use Helvetica Neue for tabular figures).

## Layout

### Overall Structure

```
┌──────────┬────────────────────────────────────────┐
│ Sidebar  │ Header (56px, white, 1px shadow)        │
│ 220px    ├────────────────────────────────────────┤
│ white    │ Content (`{colors.canvas}` #f0f2f5,   │
│ #FFFFFF  │           16px padding)                 │
│          │  ├── Page header (title + breadcrumb)   │
│          │  ├── Stat cards row (Row > Col × 4)     │
│          │  ├── Chart section (Col 16 + Col 8)     │
│          │  └── Table section (full width)          │
└──────────┴────────────────────────────────────────┘
```

### Spacing

| Token | Value | Use |
|-------|:-----:|-----|
| `{spacing.sm}` | 12px | Card gap (Row `gutter={[12,12]}`) |
| `{spacing.md}` | 16px | Content padding, card padding |

---

## Page Templates

### Template 1: Analysis Dashboard（分析页）

The signature Ant Design Pro analysis page layout. Use for any data overview / monitoring dashboard.

```
┌─────────────────────────────────────────────────────┐
│  Stat Cards Row (Row gutter={[16,16]})               │
│  ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐              │
│  │Stat 1│ │Stat 2│ │Stat 3│ │Stat 4│   ← Col span=6 │
│  │value │ │value │ │value │ │value │              │
│  │+mini │ │+mini │ │+mini │ │+mini │              │
│  │chart │ │chart │ │chart │ │chart │              │
│  └──────┘ └──────┘ └──────┘ └──────┘              │
├─────────────────────────────────────────────────────┤
│  Tab Bar (销售额 | 访问量)                            │
├──────────────────────┬──────────────────────────────┤
│  Trend Chart          │  Rank / Ratio Chart          │
│  (Col span=16)        │  (Col span=8)                │
│  Line + Bar combo     │  Pie or bar list             │
│  Full height 400px    │  Full height 400px           │
├──────────────────────┴──────────────────────────────┤
│  Detail Table (Col span=24)                          │
│  Search keywords / transaction list                  │
└─────────────────────────────────────────────────────┘
```

**Implementation rules:**
- Stat cards: 4 cards per row max. Each card = `<Card>` + `<Statistic>` + optional mini ECharts (height 60-80px, no axis labels).
- Tab bar: `<Tabs>` inside its own `<Card>`.
- Trend chart occupies 16/24 columns (`<Col span={16}>`); rank chart occupies 8/24.
- Bottom table occupies full width (`<Col span={24}>`).
- Vertical spacing between rows: `{spacing.lg}` (24px).

### Template 2: List / Table Page（列表页）

```
┌─────────────────────────────────────────────────────┐
│  Filter Bar                                          │
│  ┌─────────────────────────────────────────────┐   │
│  │ [Input search] [Select filter] [DatePicker]   │   │
│  │ [Query] [Reset]                               │   │
│  └─────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────┤
│  Action Bar                                          │
│  [New Item] [Batch Delete] [Export]                  │
├─────────────────────────────────────────────────────┤
│  Data Table                                          │
│  ┌─────────────────────────────────────────────┐   │
│  │ Col1 │ Col2 │ Col3 │ Status │ Actions    │   │
│  │ ──── │ ──── │ ──── │ ────── │ ────────── │   │
│  │ ...  │ ...  │ ...  │ Tag    │ Edit/Del   │   │
│  └─────────────────────────────────────────────┘   │
│  Pagination (right-aligned)                          │
└─────────────────────────────────────────────────────┘
```

**Implementation rules:**
- Filter bar: `<Card>` with inline `<Form layout="inline">`. Dark backgrounds for filter bars are NOT Pro style.
- Action bar: `<Space>` between filter card and table.
- Table: `<Table>` with `rowKey`, `pagination={{ showSizeChanger: true, showQuickJumper: true }}`.
- Status column: use `<Tag>` with semantic colors.
- Action column: `<Space>` with `<Button type="link">` for Edit/Delete.

### Template 3: Form Page（表单页）

```
┌─────────────────────────────────────────────────────┐
│  Form Card                                           │
│  ┌─────────────────────────────────────────────┐   │
│  │  Section Title                                │   │
│  │  [Input field] [Input field]                  │   │
│  │  [Input field] [Input field]                  │   │
│  │  ─────────────────────────────────           │   │
│  │  Section Title                                │   │
│  │  [TextArea full width]                        │   │
│  │  [Upload area]                                │   │
│  └─────────────────────────────────────────────┘   │
│                                                     │
│  Sticky Bottom Bar                                   │
│  ┌─────────────────────────────────────────────┐   │
│  │                    [Cancel] [Submit]           │   │
│  └─────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
```

**Implementation rules:**
- Form layout: `Form layout="vertical"` or grouped sections with `<Divider>` + section titles.
- Form card: `<Card>` with 24px padding.
- Submit bar: sticky to bottom, `position: sticky; bottom: 0; background: #fff; border-top: 1px solid #f0f0f0; padding: 12px 0; text-align: right;`.

---

## Composite Card Pattern（复合卡片）

The signature pattern of Ant Design Pro analysis pages. A card that combines: title + tab switch + chart area + footer stats row.

### Structure

```
┌──────────────────────────────────────────┐
│ Card Title          [Tab1] [Tab2]        │  ← header
├──────────────────────────────────────────┤
│                                          │
│         Chart / Content Area             │  ← body
│         (ReactECharts / Table)           │
│                                          │
├──────────────────────────────────────────┤
│  Stat Label 1    Stat Label 2    Stat 3  │  ← footer
│  12,345          67.8%           ...     │
└──────────────────────────────────────────┘
```

### Implementation

```tsx
<Card
  title="Card Title"
  tabList={[
    { key: 'tab1', tab: 'Tab 1' },
    { key: 'tab2', tab: 'Tab 2' },
  ]}
  activeTabKey={activeTab}
  onTabChange={setActiveTab}
>
  <ReactECharts option={chartOption} style={{ height: 400 }} />
  <Row gutter={[12, 12]} style={{ marginTop: 12 }}>
    <Col span={8}>
      <Statistic title="指标1" value={12345} suffix="件" />
    </Col>
    <Col span={8}>
      <Statistic title="指标2" value={67.8} suffix="%" />
    </Col>
    <Col span={8}>
      <Statistic title="指标3" value={890} />
    </Col>
  </Row>
</Card>
```

### Rules
- Header: Card's built-in `title` + `tabList` props. Do NOT manually build tab bars inside card body.
- Body: One main chart/visualization, full card width.
- Footer: `<Row>` with `<Col>` + `<Statistic>` components. Separated from body by `marginTop: 24` or `<Divider>`.
- If only one tab: omit `tabList`, keep `title` only.

---

## Components

### Sidebar
White sidebar (`#FFFFFF`), 220px wide, `#F0F0F0` right border. Logo area at top (64px, `#1677FF` text). Menu: `theme="light"`, items 40px height. Hover: `#F5F5F5`. Active: `#E6F4FF` background, `#1677FF` text. Collapsible to 80px.

### Header
56px tall, white background, 1px bottom shadow. Contains: sidebar collapse trigger, breadcrumb, right-side user area (avatar + name). NOT transparent, NOT backdrop-blur.

### Cards
White surface, 6px radius, 16px padding, NO border. Cards sit directly on `{colors.canvas}` canvas with 12px gaps between cards.

### Buttons
Ant Design 5 defaults. Primary: `#1677FF` fill, 6px radius, 32px height. Default: white + border. Danger: red fill. Sizes: small 24px / middle 32px / large 40px.

### Table
White card-embedded. Header: `#FAFAFA` background, 14px/600. Body rows: 48px height. Row hover: `#FAFAFA`. Pagination: right-aligned below table.

### Forms
Inputs: 32px height, 6px radius, 1px `#D9D9D9` border. Focus: `#1677FF` border + `0 0 0 2px rgba(22,119,255,0.1)` glow. Labels: 14px/400, top-aligned (`layout="vertical"`).

### Tags
22px height, 4px radius, 12px font size. Five variants: default (gray), success (green), warning (amber), error (red), processing (blue).

### Modals
White surface, 8px radius, 24px padding. Footer: right-aligned buttons.

### Drawer
600px width, right-side. White surface with left rounded corners.

### Charts
Inside white cards, NO separate border or shadow on the chart itself. All ECharts instances bound to container resize.

### Filter Bar
White `<Card>` with 16px padding. Use `<Space>` for inline filters, not `<Form layout="inline">`. Query + Reset buttons right-aligned. Margin bottom: 12px.

### Statistic Card（StatCard 组件）
See AI Injection Reference Section 5 for the complete StatCard component template. White `<Card>` (12px 18px body padding). Value: 26px/600 Helvetica Neue. Trend: red ↑ for up, green ↓ for down. Mini sparkline: 36px height, no axes. See `{components.statcard}` for exact values.

---

## Charts

### ECharts Base Config

```ts
const PRO_CHART_COLORS = ['#1677FF', '#52C41A', '#FAAD14', '#FF4D4F', '#722ED1', '#13C2C2', '#FA8C16', '#8C8C8C'];

const BASE_CHART_OPTION = {
  grid: { top: 40, bottom: 24, left: 0, right: 0, containLabel: true },
  legend: { bottom: 0 },
  color: PRO_CHART_COLORS,
  tooltip: { backgroundColor: 'rgba(0,0,0,0.75)', borderWidth: 0, textStyle: { color: '#fff', fontSize: 12 } },
};
```

### Chart Type Selection

| Data purpose | Chart type | Notes |
|-------------|-----------|-------|
| Time-series trend | Line / Area | X-axis = time, smooth lines |
| Category comparison | Bar (vertical) | Sorted descending |
| Part-to-whole | Pie / Donut | Max 6 slices, center label |
| Multi-metric radar | Radar | Max 5 axes |
| Distribution | Scatter / Bubble | 2-3 dimensions |
| Ranking | Horizontal bar | Sorted ascending, top-N |

### Mini Charts (Stat Cards)

For stat card sparklines: `<ReactECharts>` at height 60-80px, hide axes + grid + legend. Only show the line/bar body. Use the card's own value as the last data point.

---

## Do's and Don'ts

### Do
- Use white sidebar (`#FFFFFF`) with light Menu theme.
- Use `{colors.canvas}` (`#f0f2f5`) for content background.
- Use 14px as the default body font size.
- Use 6px border radius for cards, inputs, buttons.
- Use 16px padding inside cards, 12px gap between cards.
- Use `<Card>` `title` + `tabList` props for composite cards with tabs.
- Keep stat cards at 4 per row max, Col span=6 each on 24-col grid.
- Use `<Statistic>` for stat values (26px, 600 weight, Helvetica Neue).
- Use mini ECharts (36-60px height) inside stat cards for sparklines.
- Make all charts responsive with resize listener.

### Don't
- Never use 16px body font for enterprise dashboards.
- Never use glass-morphism (`backdrop-filter: blur()`) on header.
- Never omit the resize handler on chart components.
- Never use `Math.random()` in mock data — use static values for deterministic rendering.

## Responsive Behavior

| Name | Width | Changes |
|------|-------|---------|
| Desktop | ≥ 1200px | Full layout, sidebar 220px |
| Laptop | 992-1200px | Sidebar collapsed to 80px |
| Tablet | 768-992px | Sidebar hidden, hamburger menu |
| Mobile | < 768px | Full mobile, no sidebar |

---

## AI Injection Reference

> **Target audience**: AI code-generation agents (Claude Code, etc.).
> **Usage**: When generating prototype code based on this DESIGN.md, use the templates below.
> **Principle**: Use Ant Design 6 default tokens. Only override Layout/Menu for white sidebar theme. StatCard and ChartCard use standard antd components (zero ProCard dependency).
> **⚠️ antd v6 API**: Use `styles={{ body: {...} }}` instead of deprecated `bodyStyle`.

### 1. ConfigProvider Theme（Ant Design 6）

Copy this into `index.tsx` as the `theme` of `<ConfigProvider>`:

```tsx
import { ConfigProvider } from 'antd';
import zhCN from 'antd/locale/zh_CN';

const themeConfig = {
  token: {
    colorPrimary: '#1677FF',
    borderRadius: 6,
    fontFamily: "-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'PingFang SC', 'Microsoft YaHei', sans-serif",
    fontSize: 14,
  },
  components: {
    Layout: {
      siderBg: '#FFFFFF',
      triggerBg: '#F5F5F5',
      triggerColor: 'rgba(0,0,0,0.65)',
    },
    Menu: {
      itemBg: '#FFFFFF',
      itemHoverBg: '#F5F5F5',
      itemSelectedBg: '#E6F4FF',
      itemColor: 'rgba(0,0,0,0.88)',
      itemSelectedColor: '#1677FF',
    },
    Card: {
      paddingLG: 16,
    },
  },
};

// Usage:
<ConfigProvider theme={themeConfig} locale={zhCN}>
  <App />
</ConfigProvider>
```

### 2. Layout Skeleton（index.tsx）

White sidebar + white header + #f0f2f5 canvas. Copy this as the base structure:

```tsx
import { Layout, Menu, Avatar, Dropdown, Breadcrumb, Button } from 'antd';
import {
  DashboardOutlined,
  BarChartOutlined,
  TableOutlined,
  FormOutlined,
  MenuFoldOutlined,
  MenuUnfoldOutlined,
  UserOutlined,
  SettingOutlined,
  LogoutOutlined,
} from '@ant-design/icons';
import { useState } from 'react';

const { Header, Sider, Content } = Layout;

// ── Sidebar Menu Config ──
const menuItems = [
  {
    key: 'group-1',
    label: '仪表盘',
    type: 'group',
    children: [
      { key: 'dashboard', icon: <DashboardOutlined />, label: '分析页' },
      { key: 'monitor', icon: <BarChartOutlined />, label: '监控页' },
    ],
  },
  {
    key: 'group-2',
    label: '数据管理',
    type: 'group',
    children: [
      { key: 'list', icon: <TableOutlined />, label: '列表页' },
      { key: 'form', icon: <FormOutlined />, label: '表单页' },
    ],
  },
];

function ProLayoutShell({ children, currentPage, onPageChange }: {
  children: React.ReactNode;
  currentPage: string;
  onPageChange: (page: string) => void;
}) {
  const [collapsed, setCollapsed] = useState(false);

  return (
    <Layout style={{ minHeight: '100vh' }}>
      {/* ── White Sidebar ── */}
      <Sider
        collapsible
        collapsed={collapsed}
        onCollapse={setCollapsed}
        trigger={null}
        width={220}
        style={{
          background: '#FFFFFF',
          borderRight: '1px solid #F0F0F0',
          overflow: 'auto',
        }}
      >
        {/* Logo */}
        <div style={{
          height: 64,
          display: 'flex',
          alignItems: 'center',
          padding: '0 24px',
          background: '#FFFFFF',
          color: '#1677FF',
          fontWeight: 600,
          fontSize: 18,
          whiteSpace: 'nowrap',
          overflow: 'hidden',
          borderBottom: '1px solid #F0F0F0',
        }}>
          {collapsed ? '📊' : '📊 智能报表平台'}
        </div>

        {/* Menu */}
        <Menu
          theme="light"
          mode="inline"
          selectedKeys={[currentPage]}
          items={menuItems}
          onClick={({ key }) => onPageChange(key)}
        />
      </Sider>

      {/* ── Main Area ── */}
      <Layout>
        {/* White Header */}
        <Header style={{
          background: '#fff',
          padding: '0 24px',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'space-between',
          height: 56,
          boxShadow: '0 1px 4px rgba(0,0,0,0.08)',
          zIndex: 9,
          position: 'sticky',
          top: 0,
        }}>
          <div style={{ display: 'flex', alignItems: 'center', gap: 16 }}>
            <Button
              type="text"
              icon={collapsed ? <MenuUnfoldOutlined /> : <MenuFoldOutlined />}
              onClick={() => setCollapsed(!collapsed)}
            />
            <Breadcrumb
              items={[
                { title: '首页' },
                { title: menuItems.find(g => g.children?.find(i => i.key === currentPage))?.label || '' },
                { title: menuItems.flatMap(g => g.children || []).find(i => i.key === currentPage)?.label || '' },
              ].filter(b => b.title)}
            />
          </div>

          <Dropdown menu={{
            items: [
              { key: 'profile', icon: <UserOutlined />, label: '个人中心' },
              { key: 'settings', icon: <SettingOutlined />, label: '设置' },
              { type: 'divider' },
              { key: 'logout', icon: <LogoutOutlined />, label: '退出登录' },
            ],
          }}>
            <Space style={{ cursor: 'pointer' }}>
              <Avatar size={32} icon={<UserOutlined />} />
              <span>Admin</span>
            </Space>
          </Dropdown>
        </Header>

        {/* Content */}
        <Content style={{ padding: 24, background: '#f0f2f5', minHeight: 'calc(100vh - 56px)' }}>
          {children}
        </Content>
      </Layout>
    </Layout>
  );
}
```

### 3. CSS Custom Properties (`style.css`)

```css
@import "tailwindcss";

:root {
  /* ── Brand ── */
  --color-primary: #1677FF;
  --color-primary-hover: #4096FF;
  --color-primary-active: #0958D9;
  --color-primary-bg: #E6F4FF;

  /* ── Surface ── */
  --color-canvas: #f0f2f5;
  --color-surface: #FFFFFF;

  /* ── Sidebar ── */
  --color-sidebar-bg: #FFFFFF;
  --color-sidebar-hover: #F5F5F5;
  --color-sidebar-active: #E6F4FF;
  --color-sidebar-text: rgba(0,0,0,0.88);
  --color-sidebar-active-text: #1677FF;
  --color-sidebar-logo-bg: #FFFFFF;
  --color-sidebar-logo-text: #1677FF;

  /* ── Header ── */
  --color-header-bg: #FFFFFF;
  --header-shadow: 0 1px 4px rgba(0,0,0,0.08);
  --header-height: 56px;

  /* ── Text ── */
  --color-text: rgba(0,0,0,0.88);
  --color-text-secondary: rgba(0,0,0,0.65);
  --color-text-tertiary: rgba(0,0,0,0.45);

  /* ── Semantic ── */
  --color-success: #52C41A;
  --color-warning: #FAAD14;
  --color-error: #FF4D4F;
  --color-info: #1677FF;

  /* ── Border ── */
  --color-border: #D9D9D9;

  /* ── Typography ── */
  --font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'PingFang SC', 'Microsoft YaHei', sans-serif;
  --font-size-base: 14px;

  /* ── Radius ── */
  --radius-md: 6px;
  --radius-lg: 8px;

  /* ── Spacing ── */
  --space-md: 16px;
  --space-lg: 24px;

  /* ── Shadows ── */
  --shadow-card: 0 1px 2px 0 rgba(0,0,0,0.03), 0 1px 6px -1px rgba(0,0,0,0.02), 0 2px 4px 0 rgba(0,0,0,0.02);

  /* ── Layout ── */
  --sidebar-width: 220px;
  --sidebar-collapsed-width: 80px;
  --chart-min-height: 320px;
}
```

### 4. Analysis Page Skeleton (JSX)

The standard Pro analysis dashboard page structure:

```tsx
import { Row, Col, Card, Statistic, Tabs, Table } from 'antd';
import { ArrowUpOutlined, ArrowDownOutlined } from '@ant-design/icons';
import ReactECharts from 'echarts-for-react';

function AnalysisDashboard() {
  const [activeTab, setActiveTab] = useState('sales');

  return (
    <div>
      {/* ── Row 1: Stat Cards ── */}
      <Row gutter={[16, 16]} style={{ marginBottom: 24 }}>
        {[1,2,3,4].map(i => (
          <Col xs={24} sm={12} lg={6} key={i}>
            <Card style={{ borderRadius: 6 }}>
              <Statistic
                title="总销售额"
                value={126560}
                prefix="¥"
                precision={2}
                valueStyle={{ fontSize: 30, fontWeight: 600 }}
              />
              <div style={{ marginTop: 8, fontSize: 14 }}>
                <span style={{ color: '#FF4D4F' }}>
                  <ArrowUpOutlined /> 12.5%
                </span>
                <span style={{ color: 'rgba(0,0,0,0.45)', marginLeft: 8 }}>较昨日</span>
              </div>
              {/* Mini sparkline */}
              <ReactECharts
                option={{
                  grid: { top: 8, bottom: 0, left: 0, right: 0 },
                  xAxis: { show: false, data: [1,2,3,4,5,6,7] },
                  yAxis: { show: false },
                  series: [{
                    type: 'line',
                    smooth: true,
                    showSymbol: false,
                    lineStyle: { width: 2, color: '#1677FF' },
                    areaStyle: { color: { type: 'linear', x: 0, y: 0, x2: 0, y2: 1,
                      colorStops: [{offset:0,color:'rgba(22,119,255,0.15)'},{offset:1,color:'rgba(22,119,255,0)'}]}},
                    data: [30,40,35,50,49,60,70],
                  }],
                }}
                style={{ height: 60, marginTop: 12 }}
              />
            </Card>
          </Col>
        ))}
      </Row>

      {/* ── Row 2: Composite Card with Tabs + Chart ── */}
      <Card
        title="销售趋势"
        tabList={[
          { key: 'sales', tab: '销售额' },
          { key: 'visits', tab: '访问量' },
        ]}
        activeTabKey={activeTab}
        onTabChange={setActiveTab}
        style={{ marginBottom: 24, borderRadius: 6 }}
      >
        <Row gutter={24}>
          <Col xs={24} lg={16}>
            <ReactECharts
              option={{
                grid: { top: 40, bottom: 24, left: 0, right: 0, containLabel: true },
                legend: { bottom: 0 },
                color: ['#1677FF', '#52C41A'],
                xAxis: { type: 'category', data: ['1月','2月','3月','4月','5月','6月'] },
                yAxis: { type: 'value' },
                series: [
                  { name: '销售额', type: 'line', smooth: true, data: [120,200,150,180,220,260] },
                ],
              }}
              style={{ height: 400 }}
            />
          </Col>
          <Col xs={24} lg={8}>
            <ReactECharts
              option={{
                grid: { top: 0, bottom: 0, left: 0, right: 0 },
                legend: { show: false },
                color: ['#1677FF','#52C41A','#FAAD14','#FF4D4F'],
                series: [{
                  type: 'pie',
                  radius: ['50%','80%'],
                  center: ['50%','50%'],
                  data: [
                    { value: 335, name: '分类A' },
                    { value: 310, name: '分类B' },
                    { value: 234, name: '分类C' },
                    { value: 135, name: '分类D' },
                  ],
                  label: { show: false },
                  emphasis: { label: { show: true } },
                }],
              }}
              style={{ height: 400 }}
            />
          </Col>
        </Row>
      </Card>

      {/* ── Row 3: Detail Table ── */}
      <Card title="明细数据" style={{ borderRadius: 6 }}>
        <Table
          columns={[
            { title: '排名', dataIndex: 'rank', width: 80 },
            { title: '关键词', dataIndex: 'keyword' },
            { title: '搜索次数', dataIndex: 'count', sorter: (a,b) => a.count - b.count },
            { title: '转化率', dataIndex: 'rate', render: v => `${v}%` },
            {
              title: '状态',
              dataIndex: 'status',
              render: s => <Tag color={s==='active'?'green':'default'}>{s==='active'?'活跃':'停用'}</Tag>,
            },
          ]}
          dataSource={[...]}
          rowKey="id"
          pagination={{ showSizeChanger: true, showQuickJumper: true }}
        />
      </Card>
    </div>
  );
}
```

### 5. StatCard Component Template（替代 ProCard StatisticCard）

Standard component for all stat cards. Copy-paste, fill in props:

```tsx
import { Card, Statistic } from 'antd';
import { ArrowUpOutlined, ArrowDownOutlined } from '@ant-design/icons';
import ReactECharts from 'echarts-for-react';

interface StatCardProps {
  title: string;
  value: number;
  suffix?: string;
  trend: number;       // positive = up, negative = down
  miniData: number[];   // 7-30 data points for mini sparkline
}

const StatCard: React.FC<StatCardProps> = ({ title, value, suffix, trend, miniData }) => {
  const isUp = trend > 0;
  return (
    <Card style={{ borderRadius: 6 }} styles={{ body: { padding: '12px 18px' } }}>
      <Statistic
        title={title}
        value={value}
        suffix={suffix}
        valueStyle={{ fontSize: 26, fontWeight: 600, fontFamily: 'Helvetica Neue, -apple-system, sans-serif' }}
      />
      <div style={{ marginTop: 4, fontSize: 14 }}>
        <span style={{ color: isUp ? '#FF4D4F' : '#52C41A' }}>
          {isUp ? <ArrowUpOutlined /> : <ArrowDownOutlined />} {Math.abs(trend)}%
        </span>
        <span style={{ color: 'rgba(0,0,0,0.45)', marginLeft: 8 }}>较昨日</span>
      </div>
      <ReactECharts
        option={{
          grid: { top: 4, bottom: 0, left: 0, right: 0 },
          xAxis: { show: false, data: miniData.map((_, i) => i) },
          yAxis: { show: false },
          series: [{
            type: 'line', smooth: true, showSymbol: false, data: miniData,
            lineStyle: { width: 2, color: isUp ? '#FF4D4F' : '#52C41A' },
            areaStyle: {
              color: {
                type: 'linear', x: 0, y: 0, x2: 0, y2: 1,
                colorStops: [
                  { offset: 0, color: isUp ? 'rgba(255,77,79,0.15)' : 'rgba(82,196,26,0.15)' },
                  { offset: 1, color: 'rgba(255,255,255,0)' },
                ],
              },
            },
          }],
        }}
        style={{ height: 36, marginTop: 4 }}
      />
    </Card>
  );
};
```

### 6. ChartCard Pattern（替代 ProCard ChartCard）

Card with built-in tab switching + chart + optional footer stats:

```tsx
import { Card, Row, Col, Statistic } from 'antd';
import ReactECharts from 'echarts-for-react';

<Card
  title="销售趋势"
  tabList={[
    { key: 'views', tab: '查看量' },
    { key: 'push', tab: '推送量' },
  ]}
  activeTabKey={activeTab}
  onTabChange={setActiveTab}
  style={{ borderRadius: 6, marginBottom: 12 }}
>
  <ReactECharts option={chartOption} style={{ height: 280 }} />

  {/* Optional: footer stats row */}
  <Row gutter={[12, 12]} style={{ marginTop: 12 }}>
    <Col span={8}>
      <Statistic title="指标1" value={12345} suffix="件" />
    </Col>
    <Col span={8}>
      <Statistic title="指标2" value={67.8} suffix="%" />
    </Col>
    <Col span={8}>
      <Statistic title="指标3" value={890} />
    </Col>
  </Row>
</Card>
```

**Rules**:
- Use Card's built-in `tabList` prop — do NOT manually build tabs
- Chart height: 280px for trend charts, adjustable per chart type
- Footer stats: `<Row>` with `<Statistic>`, separated from chart by `marginTop: 12`

### 7. Component Style Quick Reference

| Token | Ant Design Component | Key Style |
|-------|---------------------|-----------|
| White sidebar | `<Layout.Sider>` | `style={{ background: '#FFFFFF', borderRight: '1px solid #F0F0F0' }}` |
| White header | `<Layout.Header>` | `style={{ background: '#fff', height: 56, boxShadow: '0 1px 4px rgba(0,0,0,0.08)' }}` |
| Canvas | `<Layout.Content>` | `style={{ background: '#f0f2f5', padding: 16 }}` |
| Card | `<Card>` | `style={{ borderRadius: 6 }}`, `styles={{ body: { padding: 16 } }}` |
| Composite card | `<Card title tabList>` | Card's built-in props, chart height 280px |
| Stat card | `<StatCard>` component | See section 5 — Card + Statistic + mini ECharts, 12px 18px body padding |
| Button primary | `<Button type="primary">` | Ant Design default |
| Table | `<Table>` | `pagination={{ showSizeChanger: true }}` |
| Tag | `<Tag>` | Default Ant Design colors |
| Filter bar | `<Card>` + `<Space>` | 16px padding, 12px marginBottom |
| Chart | `<ReactECharts>` inside `<Card>` | Height by type: mini 36-60px / gauge 200px / pie 200px / bar 250px / trend 280px |
| Modal | `<Modal>` | Ant Design default |
| Drawer | `<Drawer>` | `width={600}` |

---

## Page Template Selection

When generating a page, match its type to one of the three templates:

| Page type | Template | Key pattern |
|-----------|----------|------------|
| Dashboard / 分析 / 总览 / 监控 | **Analysis Dashboard** | Stat cards row → composite card with tabs → chart + rank → detail table |
| 列表 / 管理 / 查询 / 搜索 | **List / Table** | Filter bar → action bar → data table → pagination |
| 设置 / 配置 / 新建 / 编辑 | **Form** | Form card with sections → sticky submit bar |
| 详情 / 预览 | **List/Table variant** | Single-card layout, no filter bar, back button in breadcrumb |
| 对话 / 聊天 | **Custom** | Not covered by Pro templates — design per PRD |
| 编辑器 / 画布 | **Custom** | Not covered by Pro templates — design per PRD |
