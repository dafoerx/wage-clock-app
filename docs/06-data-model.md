# 数据模型

首版建议使用本地存储。Swift 项目中可用 `UserDefaults` 存轻量设置，后续扩展到 SwiftData 或 Core Data。

## 1. WorkProfile

工作配置。

```json
{
  "id": "uuid",
  "name": "主业",
  "salaryType": "monthly",
  "salaryAmount": 12000,
  "currency": "CNY",
  "workDays": [1, 2, 3, 4, 5],
  "startTime": "09:00",
  "endTime": "18:00",
  "enableLunchBreak": true,
  "lunchStart": "12:00",
  "lunchEnd": "13:30",
  "payday": 10,
  "createdAt": "2026-06-10T00:00:00Z",
  "updatedAt": "2026-06-10T00:00:00Z"
}
```

字段说明：

- `salaryType`：`monthly`、`daily`、`hourly`。
- `workDays`：周一到周日建议用 1 到 7。
- `payday`：每月发薪日，可选。

## 2. ExpenseRecord

消费换算记录。

```json
{
  "id": "uuid",
  "title": "奶茶",
  "amount": 35,
  "currency": "CNY",
  "requiredWorkMinutes": 42,
  "profileId": "uuid",
  "createdAt": "2026-06-10T00:00:00Z"
}
```

## 3. UserSettings

用户设置。

```json
{
  "selectedProfileId": "uuid",
  "themeId": "default",
  "widgetStyle": "simple",
  "hasCompletedOnboarding": true,
  "proStatus": "free",
  "createdAt": "2026-06-10T00:00:00Z",
  "updatedAt": "2026-06-10T00:00:00Z"
}
```

## 4. ProEntitlement

付费权益状态。

```json
{
  "isPro": false,
  "productId": null,
  "expiresAt": null,
  "lastVerifiedAt": null
}
```

## 5. OvertimeRecord

加班记录，V1.1。

```json
{
  "id": "uuid",
  "profileId": "uuid",
  "date": "2026-06-10",
  "startTime": "18:00",
  "endTime": "20:00",
  "multiplier": 1.5,
  "income": 120,
  "createdAt": "2026-06-10T00:00:00Z"
}
```

## 6. 存储建议

V1.0：

- 单个工作配置用 `UserDefaults` 即可。
- 消费记录如果数量不大，也可先用 JSON 编码后存本地。

V1.1 以后：

- 多工作配置。
- 消费记录。
- 加班记录。
- 月度统计缓存。

这些数据建议迁移到 SwiftData。

## 7. 隐私原则

- 工资数据默认只存本地。
- 不做默认云同步。
- 不采集具体工资金额用于分析。
- 埋点只记录功能行为，不记录用户输入金额。
