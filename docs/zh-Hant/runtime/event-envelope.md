# 事件封裝（Canonical）

**Language:** 繁體中文 (zh-Hant)
**Applies to:** current (pre-versioned)

!!! info "MVP 合約"
    本頁記錄 MVP 鎖定的合約內容，不新增或改變語義。

## 這是什麼
本頁定義 MVP 的事件封裝（event envelope）。事件是事實，不是指令；事件日誌是發生過的權威記錄。

## 為什麼需要
系統事件定義真相；嚴格的封裝欄位與序列化順序讓重播與稽核具備可重現性。

## 規則（MVP locked）
- 每個封裝必填欄位：`domain`, `type`, `payload`, `event_id`, `parent_event_id`, `correlation_id`, `source`，以及執行期中繼資料（runtime version、InitKit hash、agent identifiers）與 EMS signature。
- `target` 為可選，除非需要阻塞式回應。
- Guardian 產生根 `correlation_id`；所有下游封裝必須原樣複製，並可附加 `span_id`。
- 事件以序號追加；阻塞互動必須遵守因果順序（核准不得先於提案）。
- 發送者權限固定：Guardian 負責 lifecycle gates，Agent Factory 負責 instantiation events，Engineering Agent 負責 execution telemetry，repository bridges 負責 commit events。

## 最小範例
```json
{
  "domain": "Lifecycle",
  "type": "ExecutionArmed",
  "event_id": "evt-001",
  "parent_event_id": "evt-000",
  "correlation_id": "corr-sprint1",
  "span_id": "span-a",
  "source": "Guardian",
  "target": "EventBus",
  "payload": {"sprint_id": "SPRINT1"},
  "runtime_version": "v0",
  "initkit_hash": "initkit-abc",
  "agent_id": "guardian-1",
  "signature": "ems:..."
}
```

## 常見失敗模式
- 遺漏 `event_id`、`parent_event_id` 或 `correlation_id`。
- 在提案或 review 事件出現前就發出核准事件。
- 接受非權威來源的封裝事件。

Translation status: Complete
