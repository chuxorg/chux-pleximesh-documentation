# MVP 必要事件

**Language:** 繁體中文 (zh-Hant)
**Applies to:** current (pre-versioned)

!!! info "MVP 合約"
    本頁記錄 MVP 鎖定的合約內容，不新增或改變語義。

## 這是什麼
本頁列出受治理執行所需的 MVP 事件類型，並區分必要、可選與超出範圍的事件。

## 為什麼需要
必要事件確保完整且可稽核的執行軌跡；可選遙測提升可觀測性，但不能取代 gate 事件。

## 規則（MVP locked）
- 必要事件（每次執行必須存在）：
  - `Lifecycle.ExecutionArmed`
  - `Tasking.FactoryInvocationRequested`
  - `Tasking.FactoryInvocationAccepted`
  - `Lifecycle.AgentInstantiated`
  - `Tasking.AgentTaskIssued`
  - `Verification.TaskReadyForReview`
  - `Lifecycle.ExecutionApproved` OR `Lifecycle.ExecutionRejected`
  - `Repository.CommitPublished`（僅適用於核准通過的執行）
  - `Lifecycle.SprintSliceCompleted`
- 必要的條件事件：
  - `Tasking.RevisionRequired`（Guardian 決定為 REVISE 時）
  - `system.run.aborted`（Guardian 決定為 REJECT 時）
  - Violation events（當預期事件或確認失敗時）
- 可選遙測事件：
  - `Tasking.TaskInProgress`
  - `Tasking.TaskBlocked`
  - `Tasking.ClarificationRequested`
- MVP 超出範圍：任何未列於上方的事件類型。

## 最小範例
```yaml
required:
  - Lifecycle.ExecutionArmed
  - Tasking.FactoryInvocationRequested
  - Tasking.FactoryInvocationAccepted
  - Lifecycle.AgentInstantiated
  - Tasking.AgentTaskIssued
  - Verification.TaskReadyForReview
  - Lifecycle.ExecutionApproved
  - Repository.CommitPublished
  - Lifecycle.SprintSliceCompleted
optional:
  - Tasking.TaskInProgress
  - Tasking.TaskBlocked
  - Tasking.ClarificationRequested
```

## 常見失敗模式
- 將可選遙測當作必要 gate 事件的替代。
- 必要事件名稱非正規格式。
- 未經 Guardian 核准就把新事件型別加入 MVP 合約。

Translation status: Complete
