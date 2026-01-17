# PlexiMesh 與 AWACS — 使用者/操作指南

**Language:** 繁體中文 (zh-Hant)
**Applies to:** current (pre-versioned)

## 問題
缺乏硬治理的分散式 AI 執行很快就會漂移：角色混淆、代理自行指派任務，觀測變成事後猜測。傳統工作流程引擎會自動化佇列，但缺少憲制控制、Canon 文件與可驗證軌跡。操作者需要一個網格，既能防止範圍漂移、讓人類站在意圖邊界，也能讓代理在監督下產出實際程式碼，同時提供唯讀觀測視窗。

## 解法
PlexiMesh Runtime 提供具治理的執行基座，承載 Guardian、PM、Engineering、Librarian、Voodoo 等代理。它落實 InitKit 法律、強制角色不可變，並以事件優先骨幹（EMS signatures 與附加式日誌）路由所有代理互動。AWACS（唯讀觀測平面）映射 runtime 的事實——事件、決策日誌與 Canon 參照——讓人類與下游系統可視化時間線，而不觸碰即時執行。

## 運作方式

### Canon（由 Librarian 管理）
- Canon 是法律、sprint 計畫與任務產物的不可變真相來源（例如 `canon/tasks/ENG-S1-TASK-001-runtime-skeleton-validation.md`）。  
- 工程師與操作者必須將 Canon 視為唯讀；Guardian 在授權任何 sprint 或任務前必須驗證 Canon。  
- Canon 內容在需要時同步到 runtime（例如 runtime repo 會包含 ENG-S1 任務產物供 Engineering Agent 離線參照）。

### 事件匯流排與訊息
- Go runtime 於 `pkg/event/metadata.go` 註冊所有 canonical 事件型別，涵蓋 lifecycle、intent、tasking、verification、telemetry、violations 等。  
- 所有封裝包含 domain/type、payload、runtime metadata、correlation IDs 與 EMS signatures 以供路由與重播。  
- 事件是事實，不是指令；狀態轉換只有在 Guardian 發出時才成立，而事件 sink 記錄其證據。

### 執行流程（Guardian → PM → Engineering → Guardian）
- Sprint 1 執行模型記錄權威握手：Guardian 啟動執行、引用 PM 產物、Agent Factory 實例化惰性 Engineering Agent、Guardian 發出有界任務，代理完成工作並等待 Guardian 核准或拒絕後才可提交。詳見 [Sprint 1 執行模型](sprint-1-execution-model.md)。  
- 每個邊界都會發出必要事件（例如 `Lifecycle.ExecutionArmed`、`Tasking.AgentTaskIssued`、`Verification.TaskReadyForReview` 等），使 AWACS 與稽核者得以端到端追蹤。

### Agent Factory 與 Runner
- Agent Factory 以能力清單、InitKit 上下文與 harness wiring 實例化代理，但在 Guardian 指派任務前保持惰性。  
- Runner 強制生命週期順序（`Init → Start → HandleEvent → Shutdown`），確保代理無法在執行中獲得新權限。

### AWACS（觀測平面）
- 在 runtime 文件中定義為事件、決策日誌與 Canon 參照的唯讀消費者。  
- AWACS 呈現 correlation chain 與生命週期標記，但**絕不**發出指令或修改 Canon。  
- 因每個封裝都帶有 correlation IDs，AWACS 能在不接觸程式碼的情況下視覺化 sprint lane。

### 哲學與體驗
- InitKit 文件記錄經驗：AI 是工具而非架構師；Guardian 核准是必須的弧線；確定性勝過即興。  
- 治理是結構性的：EMS signatures、附加式日誌、角色不可變與 Canon 狀態標頭避免模糊，而非僅提供警告。  
- AWACS 存在是因為過往系統把可觀測性隱藏在特權 API 後；PlexiMesh 將觀測與行動分離以維持安全性保證。

## 步驟
1. **確認 Canon 就緒**  
   - Librarian 發佈或更新 sprint/任務產物。  
   - Guardian 在任何代理工作前驗證 Canon 狀態（Canonical vs Deferred）。

2. **啟動執行**  
   - Guardian 發出 lifecycle `Lifecycle.ExecutionArmed` 並記錄 correlation ID，供 AWACS 與所有代理使用。

3. **透過 Guardian → Agent Factory 指派任務**  
   - Guardian 發出有界任務並引用 Canon IDs；Agent Factory 實例化或啟動 Engineering Agent 並帶入 InitKit snapshot。

4. **代理在 Harness 下執行**  
   - Agent 只修改授權檔案（例如確保 `/runtime/runtime.go` 符合 Canon），並發送遙測事件，如 `Tasking.TaskInProgress` 或 `Tasking.TaskBlocked`。

5. **Guardian 審查與提交**  
   - Agent Factory 封裝 diff 並發出 `Verification.TaskReadyForReview`；Guardian 以 `Lifecycle.ExecutionApproved` 或 `Lifecycle.ExecutionRejected` 核准或拒絕。  
   - 若核准，commit metadata 會嵌入 correlation ID，並發出 `Repository.CommitPublished` 事件。

6. **透過 AWACS 觀測**  
   - 操作者使用 AWACS 時間線或附加式日誌確認必要事件皆已發生且 Canon 參照一致。

## 範例
- **Runtime Skeleton Validation（ENG-S1-TASK-001）：**  
  - Canon 任務要求建立 `/runtime/runtime.go` 並包含特定 struct 與 constructor。  
  - Engineering Agent 依步驟 1–5 執行，確保 Guardian 核准並發出完整事件序列。  
  - AWACS（或日誌）顯示從 `Lifecycle.ExecutionArmed` 到 `Lifecycle.SprintSliceCompleted` 的完整流程與一致的 correlation IDs。

- **文件發布：**  
  - Documentation Agent 將 Canon 產物同步至 `docs/` 以便閱讀，並標記 Canon 為權威來源。  
  - 操作者在啟用 AWACS 監控前交叉比對文件，以確保設定符合現行治理規則。

## 常見陷阱
- **略過 Canon 驗證：** 未確認 Canon 狀態就啟動執行會被 Guardian 拒絕；務必先檢查 Librarian 產物。
- **把事件當指令：** 下游系統不得將 `Verification.TaskReadyForReview` 視為核准；只有 Guardian 決策能授權提交。
- **角色漂移：** 代理角色不可變；嘗試將 Engineering Agent 轉為 Guardian 違反 Guardian Law 並會中止執行。
- **觀測越權：** AWACS 必須保持唯讀；將 AWACS 輸出接回 runtime 控制路徑會破壞安全假設。
- **遺漏 Correlation IDs：** 忘記在提交或事件中傳遞 sprint correlation ID 會導致 AWACS 時間線斷裂並觸發稽核。

Translation status: Complete
