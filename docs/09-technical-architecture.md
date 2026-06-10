# iOS 技术架构建议

目标：用最小复杂度完成 MVP，同时给小组件、付费和后续扩展留出空间。

## 1. 技术选型

推荐：

- UI：SwiftUI。
- 状态管理：原生 `Observable` / `ObservableObject`。
- 本地存储：MVP 用 `UserDefaults` + Codable。
- 后续数据量增加后：迁移 SwiftData。
- 小组件：WidgetKit。
- 付费：StoreKit 2。
- 日期时间：Foundation Calendar / DateComponents。

不建议首版引入：

- 自建后端。
- 登录系统。
- 第三方数据库。
- 复杂埋点 SDK。
- 跨平台框架。

## 2. 模块划分

建议模块：

- `App`：启动、路由、全局状态。
- `Models`：数据模型。
- `Services`：计算、存储、付费状态。
- `Features/Home`：首页。
- `Features/Onboarding`：首次设置。
- `Features/Converter`：消费换算。
- `Features/Settings`：设置。
- `Features/Paywall`：Pro 页面。
- `Widgets`：桌面小组件。

## 3. 核心服务

### WageCalculator

职责：

- 计算当前工作状态。
- 计算今日已赚金额。
- 计算下班倒计时。
- 计算消费换算结果。

输入：

- `WorkProfile`
- 当前时间 `Date`

输出：

- `WorkStatus`
- `DailyWageSnapshot`
- `ExpenseConversionResult`

### ProfileStore

职责：

- 保存工作配置。
- 读取当前配置。
- 后续支持多配置。

MVP：

- 单配置存储在 `UserDefaults`。

V1.1：

- 多配置可以迁移到 SwiftData。

### EntitlementStore

职责：

- 读取 StoreKit 2 购买状态。
- 判断 Pro 权益。
- 提供恢复购买。

### WidgetDataStore

职责：

- 将首页需要的小组件数据写入 App Group。
- Widget Extension 读取相同数据。

## 4. App Group

因为小组件需要读取主 App 数据，建议创建 App Group。

存储内容：

- 当前工作配置。
- 最近一次计算快照。
- 主题 ID。
- Pro 状态简化值。

注意：

- 小组件不能依赖主 App 实时运行。
- WidgetKit 刷新频率受系统控制。
- 小组件显示应允许一定延迟，文案避免承诺秒级实时。

## 5. 小组件刷新策略

首页前台：

- 每秒刷新 UI。

小组件：

- 使用 Timeline 生成未来若干时间点。
- 对于倒计时和金额，建议每 5 到 15 分钟一个 timeline entry。
- 小组件点击打开 App。

折中方案：

- 小组件显示近似金额。
- 主 App 打开后显示实时金额。

## 6. 付费实现

StoreKit 2 商品建议：

- `pro_lifetime`
- `pro_yearly`
- `pro_monthly`

首版如果只做买断：

- 仅配置 `pro_lifetime`。

付费状态：

- 本地缓存。
- 启动时刷新交易状态。
- 购买后立即更新 UI。

## 7. 埋点建议

不记录工资金额。

可以记录：

- onboarding_started
- onboarding_completed
- home_viewed
- converter_used
- widget_guide_viewed
- paywall_viewed
- purchase_started
- purchase_completed

## 8. 测试重点

单元测试：

- 月薪计算。
- 日薪计算。
- 时薪计算。
- 午休扣除。
- 休息日。
- 跨天边界。
- 消费换算。

手动测试：

- 小组件展示。
- 主题切换。
- 购买恢复。
- 修改工资后重新计算。

## 9. 最小工程结构

```text
WageClock/
  WageClockApp.swift
  Models/
  Services/
  Features/
    Onboarding/
    Home/
    Converter/
    Settings/
    Paywall/
  Widgets/
  Resources/
  Tests/
```
