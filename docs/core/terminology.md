# PM 核心術語

> 狀態：M1 / PM-P01 / PLAN01 — 中文化修訂完成，待審查
>
> 用途：定義 AI 輔助專案管理 Skills 使用的 canonical（權威）術語。所有 PM skills、adapter、範例與專案產出物都應一致使用這些術語；除非後續經核准的 contract 版本明確修改定義。

## 1. 術語原則

1. 同一個術語在 PM skill suite 中只能有一個 canonical 意義。
2. 若相似概念會導致不同的專案管理決策，就必須明確區分。
3. PM 語意不依賴 GitHub Projects、Plane、Jira、Markdown 等特定儲存工具。
4. 發現新資訊不代表該工作已被接受進入執行範圍。
5. 緊急不代表屬於目前範圍。
6. 工作包（Package）的識別碼不代表執行順序。
7. 衍生欄位或投影資料不得成為第二份具權威性的歸屬／執行歸屬紀錄。
8. Domain resource 名稱與 workflow `Status` 不得具有互相混淆的語意。
9. Workflow 狀態變更屬於受治理的操作；UI 上能修改欄位，不代表具有狀態轉換權限（Transition Authority）。
10. 資源建立必須完成並驗證必要的結構關係後，才算真正建立完成。

---

## 2. 結構型資源

### 專案（Project）

被管理工作的最高層級範圍。

Project 提供持久的專案脈絡，Milestone、Package、Planning Item、Discovery、Request、Spike 與 Project Backlog 都存在於這個脈絡中。

Project 也可以直接承接已接受的 Package；這類 Package 的 canonical **執行歸屬（Execution Placement）** 是「專案層級已核准執行」，且不屬於任何 Milestone。

Project 本身不是 workflow `Status`，也不是交付邊界。

### 里程碑（Milestone）

Project 內具有明確邊界的交付目標，由**目標（Goal）**、**範圍（Scope）**與**結束條件（Exit Criteria）**共同治理。

Milestone 包含 Execution Placement 指向該 Milestone 的已接受 Package。若變更會改動已核准的 Milestone baseline，必須經過明確的專案管理決策。

Milestone 不只是日期容器，也不能因為某項工作是在該 Milestone 執行期間被發現，就自動把該工作歸入這個 Milestone。

### 工作包（Package）

一個已被接受、可獨立追蹤且具有穩定識別的工作單位。

Package 應具有穩定 ID、明確的 Goal 或 deliverable，以及足以獨立追蹤的工作邊界；一個 Package 可以包含多個 Planning Item。

每個已接受的 Package 必須且只能有一個 canonical **執行歸屬（Execution Placement）**：

- 指向某一個 Milestone；或
- 專案層級已核准執行，且不屬於任何 Milestone。

範例：

- `PM-P01 — Core Model & Contracts`
- `M7-ARCH01 — Campaign identity ownership correction`

### 規劃項目（Planning Item）

Package 底下可獨立追蹤的具體工作拆解。

Planning Item 在需要獨立追蹤時，可以擁有自己的 `Status`、驗收條件（Acceptance Criteria）、阻塞狀態、討論與實作細節。

每個 Planning Item 必須且只能有一個**父工作包（Parent Package）**。它繼承 Parent Package 的 Execution Placement，以及存在時的 Milestone scope membership。沒有 Parent Package 的 Planning Item 屬於孤兒（orphan）／建立未完成狀態，不得視為建立成功。

### 清單項目（Checklist Item）

放在 Package 或 Planning Item 內的輕量執行步驟，本身不具有獨立生命週期。

若某個步驟需要自己的狀態、討論、阻塞或驗收紀錄，就應改用 Planning Item。

---

## 3. 輸入與執行中出現的工作資源

### 發現（Discovery）

在規劃、執行、審查或事件處理期間，新觀察到、學習到或懷疑可能影響專案工作的資訊紀錄。

Discovery 先保存證據與脈絡，再決定要不要產生工作。Discovery **不是已接受工作**，本身不會自動修改 Milestone、Package、Priority 或時程。

> **發現 ≠ 接受為工作（DISCOVER ≠ ACCEPT）**

### 需求（Request）

尚未被接受進入任何執行範圍的新需求、想法、功能、變更或一次性工作。

Request 經過 triage 後，可以被接受、保留在 Project Backlog、Reject，或轉成其他工作資源。

### 專案待辦池（Project Backlog）

保存「可能有價值，但目前尚未承諾進入 active Milestone 或其他已核准執行範圍」工作的 Project-level collection。

Project Backlog 是**範圍／承諾歸屬概念**，不是 workflow `Status`。

> **Project Backlog ≠ `Status = Backlog`**

### 探索任務（Spike）

當證據不足以做出負責任的實作或範圍決策時，用來降低不確定性的有限範圍調查工作。

Spike 應具有明確問題、調查邊界、預期產出與完成條件。Spike 不會自動授權 production implementation。

> **未知 ≠ 立即實作（UNKNOWN ≠ IMPLEMENT NOW）**

---

## 4. 決策術語

### 範圍變更（Scope Change）

因新發現的工作是 Milestone 達成既有 Goal / Exit Criteria 所必需，或經授權後修改 Milestone 目標，而改變 Milestone 已接受 Scope 的決策。

核心判斷問題：

> 如果不做這項工作，Milestone 還能滿足已核准的 Goal 與 Exit Criteria 嗎？

若答案為「不能」，且已有足夠證據，該工作就是 Scope Change 候選。

### 中斷工作（Interruption）

暫停或降低目前工作的優先順序，改為立即處理另一項緊急工作，**但不宣告該緊急工作屬於目前 Milestone Scope** 的決策。

經核准的 Interruption 可以形成一個 Execution Placement 為「專案層級已核准執行」的 Package。

> **緊急 ≠ 屬於目前範圍（URGENT ≠ IN SCOPE）**

### 不採納（Reject）

決定不為目前形式的 Discovery 或 Request 建立或保留可執行工作。

重複、無效、已解決、已被其他 accepted work 涵蓋或明確決定不做的工作可以 Reject。具有價值但不緊急的工作，應進 Project Backlog，而不是只因為不緊急就 Reject。

---

## 5. 規劃與交付術語

### 目標（Goal）

Project、Milestone、Package 或 Spike 想達成的成果。Goal 說明「為什麼存在這項工作、希望得到什麼結果」，而不是詳細實作步驟。

### 範圍（Scope）

目前被接受屬於某個 Project、Milestone 或 Package 的工作邊界。Scope 只能透過明確決策改變，不能因為執行期間發現新工作就默默擴張。

### 結束條件（Exit Criteria）

Milestone 要被視為成功完成前，必須成立的條件。

> Exit Criteria 回答：**這個 Milestone 可以關閉了嗎？**

### 驗收條件（Acceptance Criteria）

Package、Planning Item 或其他 deliverable 要被接受為完成前，必須可驗證成立的條件。

> Acceptance Criteria 回答：**這個具體工作項目做對、做完了嗎？**

> **Exit Criteria 決定 Milestone 能否結束；Acceptance Criteria 決定特定工作項目能否被接受。**

### 審查結果（Review Result）

Reviewer 針對 Acceptance Criteria 或其他 review target 給出的明確結果。Review Result 是 reviewer recommendation，**不是 workflow `Status`**。

Canonical result token：

- `ACCEPT`
- `REVISE`

`REVISE` 通常會讓工作從 `Review` 回到 `In Progress`；不需要額外建立 `Revision Required` Status。

完整 review response、finding、re-review 與 transition matrix 由 `docs/core/workflow-contract.md` / PLAN07 定義。

---

## 6. 排程與執行維度

### 狀態（Status）

被追蹤工作項目目前所在的 workflow state。

M1 canonical Status 值：

- **`Backlog`** — 已追蹤，但目前尚未準備好執行。
- **`Ready`** — 已接受且現在可以開始。
- **`In Progress`** — 正在執行。
- **`Blocked`** — 因尚未解除的限制而無法繼續。
- **`Review`** — 實作或文件已完成，等待驗證或接受。
- **`Done`** — 所需驗收已完成，工作結束。

Status 不代表 Scope、commitment、Priority、Execution Placement，也不代表具有修改狀態的權限。已經屬於 Milestone 的 Package 在等待執行時，仍可以合法處於 `Status = Backlog`。

### 行動負責角色（Action Owner）

工作項目處於某個 Status 時，預期執行下一個有效動作的角色。

> Action Owner 回答：**現在輪到誰行動？**

Action Owner 不一定等於 GitHub assignee、issue creator、structural owner 或目前查看者。Status 對 Action Owner 的 authoritative mapping 由 PLAN07 定義。

### 狀態轉換權限（Transition Authority）

依角色或政策，被允許執行某一條 workflow Status transition 的權限。

> Transition Authority 回答：**誰有權把這個工作從目前 Status 切換到目標 Status？**

技術上可以編輯 GitHub Project 欄位，不代表具有 Transition Authority。完整 transition matrix 由 PLAN07 定義。

### 優先級（Priority）

用來比較可執行工作相對重要性的維度。Canonical Project 值可使用 `P0 Critical`、`P1 High`、`P2 Normal`、`P3 Low`。

Priority 不等於預定執行順序。

### 緊急度（Urgency）

延遲工作所造成的時間敏感度或後果。Urgency 是 triage 與優先排序的輸入；高度緊急也不代表該工作屬於目前 Milestone Scope。

### 執行順序（Sequence）

同層級 Package 或其他明確排序資源之間的預定相對執行位置。Sequence 是可調整的 planning metadata；Package ID 不因 Sequence 改變而改名。

> **Package ID ≠ 執行順序（PACKAGE ID ≠ EXECUTION ORDER）**

---

## 7. 關係術語

### 父／子關係（Parent / Child）

上層資源包含下層工作的結構拆解關係。

對已接受工作而言，Package 有兩條合法的 canonical 執行歸屬路徑：

```text
Project
├─ Milestone
│  └─ Package
│     └─ Planning Item
│        └─ Checklist Item
│
└─ Package [專案層級已核准執行]
   └─ Planning Item
      └─ Checklist Item
```

已接受 Package 的 incoming structural relation 就是它的 **Execution Placement**；`Package → Planning Item → Checklist Item` 則是 Parent / Child decomposition。

Discovery 不會因為是在某個 Milestone 中被發現，就自動成為該 Milestone 的 child。

### 執行歸屬（Execution Placement）

Package 專用的 canonical structural placement relation，用來回答：

> 這個已接受的 Package 現在正式屬於哪一個已核准執行範圍？

每個已接受 Package 必須且只能有一個 Execution Placement：

```text
Milestone:<id> → Package
```

或：

```text
Project → Package [專案層級已核准執行]
```

對 Package 而言：

> **Execution Placement = authoritative structural placement**

Execution Placement **不是第二份 ownership record**。Adapter 不得另外維護一份同樣具有 authoritative 性質的 `Package Owner`、`Structural Owner` 或其他重複表示相同歸屬的關係。

Planning Item 繼承 Parent Package 的 Execution Placement，不建立競爭性的第二份 canonical placement。

Placement integrity、建立驗證、與 Project Backlog 的互斥關係及 storage-adapter 規則，由 `docs/core/resource-model.md` / PLAN02 負責。

### 父工作包（Parent Package）

結構上直接擁有某一個 Planning Item 的唯一 Package。

對 `Work Type = Planning` 而言，Parent Package 為必填且唯一。

> **每個 Planning Item 必須且只能有一個 Parent Package。**

Issue body 中的 `Parent Package: #1` 文字只是文件說明，不能取代真正的 structural Parent relationship。

### 發現於（Discovered In）

紀錄 Discovery 是在哪個 Project、Milestone、Package、Planning Item、執行、review 或 incident activity 中被發現的 provenance（來源脈絡）。

`Discovered In` 不定義 scope ownership，也不定義 Execution Placement。

> **在 Milestone 中發現 ≠ 屬於該 Milestone（FOUND DURING MILESTONE ≠ BELONGS TO MILESTONE）**

### 相依關係（Dependency）

一項工作會影響另一項工作能否或何時繼續的關係，例如 blocks、blocked by、requires、prerequisite for。

Dependency 與 Parent / Child、Execution Placement 都是不同概念。

### 結構歸屬（Ownership）

決定某個資源在 canonical PM model 中正式屬於哪裡的結構關係。

Ownership 必須有單一 authoritative source of truth。對已接受 Package 而言，這份來源就是 Execution Placement；衍生 metadata 不得形成互相矛盾的 ownership record。

---

## 8. 核准與決策狀態術語

### 建議（Recommendation）

Agent 依據現有證據提出的專案管理建議。除非 policy 明確允許 autonomous execution，Recommendation 本身不等於已核准決策。

### 核准（Approval）

在執行受治理的專案變更前，依 policy 所需要的明確授權。

### 人工核准關卡（Human Approval Gate）

在適用的人類授權要求完成前，阻止 Agent 執行受治理變更的 policy boundary。

本文件只定義術語。Authoritative approval matrix 與 autonomous-action policy 由 `docs/core/approval-policy.md` / PLAN05 定義。

---

## 9. Canonical 概念邊界

| 概念 A | 概念 B | Canonical 邊界 |
|---|---|---|
| Milestone | Package | Milestone 是交付邊界；Package 是可追蹤的已接受工作單位。 |
| Package | Execution Placement | Package 是工作資源；Execution Placement 說明它正式屬於哪個已核准執行範圍。 |
| Execution Placement | Ownership | 對已接受 Package 而言，Execution Placement 就是 authoritative structural ownership / placement，而不是額外的一份紀錄。 |
| Package | Planning Item | Package 是穩定工作單位；Planning Item 是其可獨立追蹤的拆解。 |
| Planning Item | Parent Package | 每個 Planning Item 必須且只能屬於一個 Parent Package。 |
| Planning Item | Checklist Item | Planning Item 可有獨立生命週期；Checklist Item 是輕量細節。 |
| Discovery | 已接受工作 | Discovery 保存資訊；triage 決定是否接受為工作。 |
| Request | Project Backlog | Request 是新提出工作；Project Backlog 保存有價值但尚未承諾的工作。 |
| Project Backlog | `Status = Backlog` | Project Backlog 描述 commitment / placement；`Status = Backlog` 描述 workflow readiness。 |
| Project Backlog | Execution Placement | Project Backlog 位於 approved execution 之外；Execution Placement 適用於已接受 Package。 |
| Scope Change | Interruption | Scope Change 修改目前 Milestone Scope；Interruption 改變立即執行優先順序，但目前 Milestone Scope 不變。 |
| Scope Change | Spike | Scope Change 是已有足夠證據後的決策；Spike 用來取得證據。 |
| Project Backlog | Reject | Project Backlog 保存可能有價值的工作；Reject 則停止把它當成可執行工作追蹤。 |
| Status | Action Owner | Status 描述 workflow state；Action Owner 說明現在誰應該行動。 |
| Action Owner | Transition Authority | Action Owner 說明誰應行動；Transition Authority 說明誰有權切換狀態。 |
| Status | Priority | Status 是生命週期狀態；Priority 是相對重要性。 |
| Priority | Urgency | Priority 是相對排序；Urgency 是時間敏感度。 |
| Priority | Sequence | Priority 影響偏好；Sequence 記錄預定順序。 |
| Review Result | Status | Review Result 是審查結果；Status 是 workflow state。 |
| Exit Criteria | Acceptance Criteria | Exit Criteria 決定 Milestone 能否結束；Acceptance Criteria 驗證特定工作。 |
| Parent / Child | Dependency | Parent / Child 是結構拆解；Dependency 是執行限制。 |
| Discovered In | Ownership | Discovered In 記錄 provenance；Ownership 決定正式結構歸屬。 |
| Package ID | Sequence | ID 是穩定識別；Sequence 是可變動順序。 |

---

## 10. Canonical 不變條件（Invariants）

除非後續經核准的 contract 版本明確修改，所有 PM skills 都必須維持：

1. **發現 ≠ 接受為工作（DISCOVER ≠ ACCEPT）**
2. **緊急 ≠ 屬於目前範圍（URGENT ≠ IN SCOPE）**
3. **在 Milestone 中發現 ≠ 屬於該 Milestone**
4. **未知 ≠ 立即實作（UNKNOWN ≠ IMPLEMENT NOW）**
5. **Package ID ≠ 執行順序**
6. **Project Backlog ≠ `Status = Backlog`**
7. **每個 Planning Item 必須且只能有一個 Parent Package。**
8. **每個已接受 Package 必須且只能有一個 Execution Placement。**
9. **Execution Placement 是已接受 Package 的 authoritative structural placement；不是第二份 ownership record。**
10. Agent 不得默默引入 Scope Change。
11. 不能只因為工作很緊急，就把 Interruption 表示成 Scope Change。
12. Planning Item 不得重複維護可由 Parent Package 繼承的 canonical Milestone / Execution Placement ownership。
13. Recommendation 與 Approval 是不同概念。
14. Review Result 與 workflow Status 是不同概念。
15. Status、Priority、Urgency、Sequence、Scope、Execution Placement 是不同的 concern。
16. Status transition 必須遵守 Transition Authority；技術上的 edit access 不代表 workflow authority。
17. 在必要 structural relationship 與 Project metadata 尚未建立完成前，不得回報資源建立成功；若無法完成，必須明確回報 partial failure。

---

## 11. 儲存工具對應說明

Canonical vocabulary 描述的是 PM domain，而不是特定產品。

初始 GitHub mapping：

| Canonical 概念 | 初始 GitHub 表示方式 |
|---|---|
| Project | GitHub Project |
| Milestone | GitHub Milestone |
| Package | GitHub Issue + `Work Type = Package` |
| Package 的 Execution Placement = Milestone | GitHub native Milestone relationship |
| Package 的 Execution Placement = 專案層級已核准執行 | Adapter 定義的明確 Project-level representation；不能只靠空 Milestone 判斷 |
| Planning Item | GitHub Sub-issue + `Work Type = Planning` |
| Parent Package | GitHub Parent issue relationship |
| Checklist Item | Markdown task item |
| Project Backlog | Adapter 定義的明確 retained-work placement |
| Status | GitHub Project `Status` field |
| Action Owner | Workflow contract／可選的 adapter projection |
| Transition Authority | Workflow contract / policy；不能從 GitHub UI edit permission 推導 |
| Priority | GitHub Project `Priority` field |
| Sequence | GitHub Project `Sequence` number field |
| Dependency | GitHub 支援的 linkage，或 adapter 管理的 explicit relation |

這些 mapping 不會重新定義 canonical PM 意義；完整 mapping integrity 由 PLAN02 與後續 GitHub adapter 負責。

---

## 12. 術語變更控制

修改本文件時必須：

1. 指出新增或修改的術語；
2. 說明原本的模糊或不足；
3. 評估對既有 PM skills 與 adapters 的相容性影響；
4. 必要時同步更新相關 canonical contracts；
5. 保留專案歷史，不得默默重新解讀已接受的舊紀錄。

本文件是 M1 的 terminology source of truth，直到被後續經核准的版本取代。