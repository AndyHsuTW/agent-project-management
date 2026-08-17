# PM 核心術語

> 狀態：M1 / PM-P01 / PLAN01 — 中文化修訂完成，待審查
>
> 用途：定義 AI 輔助專案管理 Skills 使用的權威術語。所有 PM Skills、轉接器（adapter）、範例與專案產出物都應一致使用這些術語；除非後續經核准的契約版本明確修改定義。
>
> 文件語言：以繁體中文為主。實際 GitHub 欄位值、狀態值、固定結果代碼，或難以精準翻譯的術語，保留英文原文或於首次出現時中英並列。

## 1. 術語原則

1. 同一個術語在 PM Skill 套件中只能有一個權威（canonical）意義。
2. 若相似概念會導致不同的專案管理決策，就必須明確區分。
3. PM 語意不依賴 GitHub Projects、Plane、Jira、Markdown 等特定儲存工具。
4. 發現新資訊不代表該工作已被接受進入執行範圍。
5. 緊急不代表屬於目前範圍。
6. 工作包（Package）的識別碼不代表執行順序。
7. 衍生欄位或投影資料不得成為第二份具權威性的結構歸屬／執行歸屬紀錄。
8. 領域資源名稱與工作流程 `Status` 不得具有互相混淆的語意。
9. 工作流程狀態變更屬於受治理的操作；UI 上能修改欄位，不代表具有狀態轉換權限（Transition Authority）。
10. 資源建立必須完成並驗證必要的結構關係後，才算真正建立完成。

---

## 2. 結構型資源

### 專案（Project）

被管理工作的最高層級範圍。

專案提供持久的管理脈絡，里程碑（Milestone）、工作包（Package）、規劃項目（Planning Item）、發現（Discovery）、需求（Request）、探索任務（Spike）與專案待辦池（Project Backlog）都存在於這個脈絡中。

專案也可以直接承接已接受的工作包；這類工作包的權威**執行歸屬（Execution Placement）**是「專案層級已核准執行」，且不屬於任何里程碑。

專案本身不是工作流程 `Status`，也不是交付邊界。

### 里程碑（Milestone）

專案內具有明確邊界的交付目標，由**目標（Goal）**、**範圍（Scope）**與**結束條件（Exit Criteria）**共同治理。

里程碑包含執行歸屬指向該里程碑的已接受工作包。若變更會改動已核准的里程碑基準範圍，必須經過明確的專案管理決策。

里程碑不只是日期容器，也不能因為某項工作是在該里程碑執行期間被發現，就自動把該工作歸入這個里程碑。

### 工作包（Package）

一個已被接受、可獨立追蹤且具有穩定識別的工作單位。

工作包應具有穩定 ID、明確的目標或交付物，以及足以獨立追蹤的工作邊界；一個工作包可以包含多個規劃項目。

每個已接受的工作包必須且只能有一個權威**執行歸屬（Execution Placement）**：

- 指向某一個里程碑；或
- 專案層級已核准執行，且不屬於任何里程碑。

範例：

- `PM-P01 — Core Model & Contracts`
- `M7-ARCH01 — Campaign identity ownership correction`

### 規劃項目（Planning Item）

工作包底下可獨立追蹤的具體工作拆解。

規劃項目在需要獨立追蹤時，可以擁有自己的 `Status`、驗收條件（Acceptance Criteria）、阻塞狀態、討論與實作細節。

每個規劃項目必須且只能有一個**父工作包（Parent Package）**。它繼承父工作包的執行歸屬，以及存在時的里程碑範圍歸屬。沒有父工作包的規劃項目屬於孤兒（orphan）／建立未完成狀態，不得視為建立成功。

### 清單項目（Checklist Item）

放在工作包或規劃項目內的輕量執行步驟，本身不具有獨立生命週期。

若某個步驟需要自己的狀態、討論、阻塞或驗收紀錄，就應改用規劃項目。

---

## 3. 輸入與執行中出現的工作資源

### 發現（Discovery）

在規劃、執行、審查或事件處理期間，新觀察到、學習到或懷疑可能影響專案工作的資訊紀錄。

發現先保存證據與脈絡，再決定要不要產生工作。發現**不是已接受工作**，本身不會自動修改里程碑、工作包、優先級或時程。

> **發現 ≠ 接受為工作（DISCOVER ≠ ACCEPT）**

### 需求（Request）

尚未被接受進入任何執行範圍的新需求、想法、功能、變更或一次性工作。

需求經過分類判定（triage）後，可以被接受、保留在專案待辦池、不採納（Reject），或轉成其他工作資源。

### 專案待辦池（Project Backlog）

保存「可能有價值，但目前尚未承諾進入 active Milestone 或其他已核准執行範圍」工作的專案層級集合。

專案待辦池是**範圍／承諾歸屬概念**，不是工作流程 `Status`。

> **Project Backlog ≠ `Status = Backlog`**

### 探索任務（Spike）

當證據不足以做出負責任的實作或範圍決策時，用來降低不確定性的有限範圍調查工作。

探索任務應具有明確問題、調查邊界、預期產出與完成條件。探索任務不會自動授權正式環境實作。

> **未知 ≠ 立即實作（UNKNOWN ≠ IMPLEMENT NOW）**

---

## 4. 決策術語

### 範圍變更（Scope Change）

因新發現的工作是里程碑達成既有目標／結束條件所必需，或經授權後修改里程碑目標，而改變里程碑已接受範圍的決策。

核心判斷問題：

> 如果不做這項工作，里程碑還能滿足已核准的目標與結束條件嗎？

若答案為「不能」，且已有足夠證據，該工作就是範圍變更候選。

### 中斷工作（Interruption）

暫停或降低目前工作的優先順序，改為立即處理另一項緊急工作，**但不宣告該緊急工作屬於目前里程碑範圍**的決策。

經核准的中斷工作可以形成一個執行歸屬為「專案層級已核准執行」的工作包。

> **緊急 ≠ 屬於目前範圍（URGENT ≠ IN SCOPE）**

### 不採納（Reject）

決定不為目前形式的發現或需求建立或保留可執行工作。

重複、無效、已解決、已被其他已接受工作涵蓋或明確決定不做的工作可以不採納。具有價值但不緊急的工作，應進專案待辦池，而不是只因為不緊急就不採納。

---

## 5. 規劃與交付術語

### 目標（Goal）

專案、里程碑、工作包或探索任務想達成的成果。目標說明「為什麼存在這項工作、希望得到什麼結果」，而不是詳細實作步驟。

### 範圍（Scope）

目前被接受屬於某個專案、里程碑或工作包的工作邊界。範圍只能透過明確決策改變，不能因為執行期間發現新工作就默默擴張。

### 結束條件（Exit Criteria）

里程碑要被視為成功完成前，必須成立的條件。

> 結束條件回答：**這個里程碑可以關閉了嗎？**

### 驗收條件（Acceptance Criteria）

工作包、規劃項目或其他交付物要被接受為完成前，必須可驗證成立的條件。

> 驗收條件回答：**這個具體工作項目做對、做完了嗎？**

> **結束條件決定里程碑能否結束；驗收條件決定特定工作項目能否被接受。**

### 審查結果（Review Result）

審查者（Reviewer）針對驗收條件或其他審查目標給出的明確結果。審查結果是 Reviewer 的建議，**不是工作流程 `Status`**。

固定結果代碼：

- `ACCEPT`
- `REVISE`

`REVISE` 通常會讓工作從 `Review` 回到 `In Progress`；不需要額外建立 `Revision Required` Status。

完整審查回覆、finding、重新審查（re-review）與狀態轉換矩陣由 `docs/core/workflow-contract.md` / PLAN07 定義。

---

## 6. 排程與執行維度

### 狀態（Status）

被追蹤工作項目目前所在的工作流程狀態。

M1 權威 Status 值：

- **`Backlog`** — 已追蹤，但目前尚未準備好執行。
- **`Ready`** — 已接受且現在可以開始。
- **`In Progress`** — 正在執行。
- **`Blocked`** — 因尚未解除的限制而無法繼續。
- **`Review`** — 實作或文件已完成，等待驗證或接受。
- **`Done`** — 所需驗收已完成，工作結束。

Status 不代表範圍、承諾、優先級、執行歸屬，也不代表具有修改狀態的權限。已經屬於里程碑的工作包在等待執行時，仍可以合法處於 `Status = Backlog`。

### 行動負責角色（Action Owner）

工作項目處於某個 Status 時，預期執行下一個有效動作的角色。

> 行動負責角色回答：**現在輪到誰行動？**

行動負責角色不一定等於 GitHub assignee、Issue 建立者、結構擁有者或目前查看者。Status 對 Action Owner 的權威對應由 PLAN07 定義。

### 狀態轉換權限（Transition Authority）

依角色或政策，被允許執行某一條工作流程 Status transition 的權限。

> 狀態轉換權限回答：**誰有權把這個工作從目前 Status 切換到目標 Status？**

技術上可以編輯 GitHub Project 欄位，不代表具有 Transition Authority。完整狀態轉換矩陣由 PLAN07 定義。

### 優先級（Priority）

用來比較可執行工作相對重要性的維度。實際 Project 欄位值可使用 `P0 Critical`、`P1 High`、`P2 Normal`、`P3 Low`。

優先級不等於預定執行順序。

### 緊急度（Urgency）

延遲工作所造成的時間敏感度或後果。緊急度是分類判定與優先排序的輸入；高度緊急也不代表該工作屬於目前里程碑範圍。

### 執行順序（Sequence）

同層級工作包或其他明確排序資源之間的預定相對執行位置。Sequence 是可調整的規劃中繼資料；Package ID 不因 Sequence 改變而改名。

> **Package ID ≠ 執行順序（PACKAGE ID ≠ EXECUTION ORDER）**

---

## 7. 關係術語

### 父／子關係（Parent / Child）

上層資源包含下層工作的結構拆解關係。

對已接受工作而言，工作包有兩條合法的權威執行歸屬路徑：

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

已接受工作包的 incoming structural relation 就是它的**執行歸屬（Execution Placement）**；`Package → Planning Item → Checklist Item` 則是父／子結構拆解。

發現不會因為是在某個里程碑中被發現，就自動成為該里程碑的子項目。

### 執行歸屬（Execution Placement）

工作包專用的權威結構歸屬關係，用來回答：

> 這個已接受的工作包現在正式屬於哪一個已核准執行範圍？

每個已接受工作包必須且只能有一個執行歸屬：

```text
Milestone:<id> → Package
```

或：

```text
Project → Package [專案層級已核准執行]
```

對工作包而言：

> **Execution Placement = authoritative structural placement**

執行歸屬**不是第二份結構歸屬紀錄**。轉接器不得另外維護一份同樣具有權威性的 `Package Owner`、`Structural Owner` 或其他重複表示相同歸屬的關係。

規劃項目繼承父工作包的執行歸屬，不建立競爭性的第二份權威歸屬。

執行歸屬完整性、建立驗證、與專案待辦池的互斥關係及儲存轉接規則，由 `docs/core/resource-model.md` / PLAN02 負責。

### 父工作包（Parent Package）

結構上直接擁有某一個規劃項目的唯一工作包。

對 `Work Type = Planning` 而言，Parent Package 為必填且唯一。

> **每個 Planning Item 必須且只能有一個 Parent Package。**

Issue body 中的 `Parent Package: #1` 文字只是文件說明，不能取代真正的父子結構關係。

### 發現於（Discovered In）

紀錄 Discovery 是在哪個 Project、Milestone、Package、Planning Item、執行、審查或 incident activity 中被發現的來源脈絡（provenance）。

`Discovered In` 不定義範圍歸屬，也不定義執行歸屬。

> **在 Milestone 中發現 ≠ 屬於該 Milestone（FOUND DURING MILESTONE ≠ BELONGS TO MILESTONE）**

### 相依關係（Dependency）

一項工作會影響另一項工作能否或何時繼續的關係，例如 `blocks`、`blocked by`、`requires`、`prerequisite for`。

相依關係與父／子關係、執行歸屬都是不同概念。

### 結構歸屬（Ownership）

決定某個資源在權威 PM 模型中正式屬於哪裡的結構關係。

結構歸屬必須有單一權威來源（source of truth）。對已接受工作包而言，這份來源就是執行歸屬；衍生中繼資料不得形成互相矛盾的結構歸屬紀錄。

---

## 8. 核准與決策狀態術語

### 建議（Recommendation）

Agent 依據現有證據提出的專案管理建議。除非政策明確允許自主執行，Recommendation 本身不等於已核准決策。

### 核准（Approval）

在執行受治理的專案變更前，依政策所需要的明確授權。

### 人工核准關卡（Human Approval Gate）

在適用的人類授權要求完成前，阻止 Agent 執行受治理變更的政策邊界。

本文件只定義術語。權威 approval matrix 與自主動作政策由 `docs/core/approval-policy.md` / PLAN05 定義。

---

## 9. 權威概念邊界

| 概念 A | 概念 B | 權威邊界 |
|---|---|---|
| Milestone | Package | Milestone 是交付邊界；Package 是可追蹤的已接受工作單位。 |
| Package | Execution Placement | Package 是工作資源；Execution Placement 說明它正式屬於哪個已核准執行範圍。 |
| Execution Placement | Ownership | 對已接受 Package 而言，Execution Placement 就是權威結構歸屬，而不是額外的一份紀錄。 |
| Package | Planning Item | Package 是穩定工作單位；Planning Item 是其可獨立追蹤的拆解。 |
| Planning Item | Parent Package | 每個 Planning Item 必須且只能屬於一個 Parent Package。 |
| Planning Item | Checklist Item | Planning Item 可有獨立生命週期；Checklist Item 是輕量細節。 |
| Discovery | 已接受工作 | Discovery 保存資訊；triage 決定是否接受為工作。 |
| Request | Project Backlog | Request 是新提出工作；Project Backlog 保存有價值但尚未承諾的工作。 |
| Project Backlog | `Status = Backlog` | Project Backlog 描述承諾／歸屬；`Status = Backlog` 描述工作流程準備程度。 |
| Project Backlog | Execution Placement | Project Backlog 位於已核准執行範圍之外；Execution Placement 適用於已接受 Package。 |
| Scope Change | Interruption | Scope Change 修改目前 Milestone 範圍；Interruption 改變立即執行優先順序，但目前 Milestone 範圍不變。 |
| Scope Change | Spike | Scope Change 是已有足夠證據後的決策；Spike 用來取得證據。 |
| Project Backlog | Reject | Project Backlog 保存可能有價值的工作；Reject 則停止把它當成可執行工作追蹤。 |
| Status | Action Owner | Status 描述工作流程狀態；Action Owner 說明現在誰應該行動。 |
| Action Owner | Transition Authority | Action Owner 說明誰應行動；Transition Authority 說明誰有權切換狀態。 |
| Status | Priority | Status 是生命週期狀態；Priority 是相對重要性。 |
| Priority | Urgency | Priority 是相對排序；Urgency 是時間敏感度。 |
| Priority | Sequence | Priority 影響偏好；Sequence 記錄預定順序。 |
| Review Result | Status | Review Result 是審查結果；Status 是工作流程狀態。 |
| Exit Criteria | Acceptance Criteria | Exit Criteria 決定 Milestone 能否結束；Acceptance Criteria 驗證特定工作。 |
| Parent / Child | Dependency | Parent / Child 是結構拆解；Dependency 是執行限制。 |
| Discovered In | Ownership | Discovered In 記錄來源脈絡；Ownership 決定正式結構歸屬。 |
| Package ID | Sequence | ID 是穩定識別；Sequence 是可變動順序。 |

---

## 10. 權威不變條件（Invariants）

除非後續經核准的契約版本明確修改，所有 PM Skills 都必須維持：

1. **發現 ≠ 接受為工作（DISCOVER ≠ ACCEPT）**
2. **緊急 ≠ 屬於目前範圍（URGENT ≠ IN SCOPE）**
3. **在 Milestone 中發現 ≠ 屬於該 Milestone**
4. **未知 ≠ 立即實作（UNKNOWN ≠ IMPLEMENT NOW）**
5. **Package ID ≠ 執行順序**
6. **Project Backlog ≠ `Status = Backlog`**
7. **每個 Planning Item 必須且只能有一個 Parent Package。**
8. **每個已接受 Package 必須且只能有一個 Execution Placement。**
9. **Execution Placement 是已接受 Package 的權威結構歸屬；不是第二份 Ownership record。**
10. Agent 不得默默引入 Scope Change。
11. 不能只因為工作很緊急，就把 Interruption 表示成 Scope Change。
12. Planning Item 不得重複維護可由 Parent Package 繼承的權威 Milestone / Execution Placement 歸屬。
13. Recommendation 與 Approval 是不同概念。
14. Review Result 與工作流程 Status 是不同概念。
15. Status、Priority、Urgency、Sequence、Scope、Execution Placement 是不同的管理維度。
16. Status transition 必須遵守 Transition Authority；技術上的 edit access 不代表 workflow authority。
17. 在必要結構關係與 Project 中繼資料尚未建立完成前，不得回報資源建立成功；若無法完成，必須明確回報 partial failure。

---

## 11. 儲存工具對應說明

權威術語描述的是 PM 領域模型，而不是特定產品。

初始 GitHub 對應：

| 權威概念 | 初始 GitHub 表示方式 |
|---|---|
| Project | GitHub Project |
| Milestone | GitHub Milestone |
| Package | GitHub Issue + `Work Type = Package` |
| Package 的 Execution Placement = Milestone | GitHub 原生 Milestone 關係 |
| Package 的 Execution Placement = 專案層級已核准執行 | 轉接器定義的明確 Project-level representation；不能只靠空 Milestone 判斷 |
| Planning Item | GitHub Sub-issue + `Work Type = Planning` |
| Parent Package | GitHub Parent issue relationship |
| Checklist Item | Markdown task item |
| Project Backlog | 轉接器定義的明確 retained-work placement |
| Status | GitHub Project `Status` field |
| Action Owner | 工作流程契約／可選的轉接器投影 |
| Transition Authority | 工作流程契約／政策；不能從 GitHub UI edit permission 推導 |
| Priority | GitHub Project `Priority` field |
| Sequence | GitHub Project `Sequence` number field |
| Dependency | GitHub 支援的 linkage，或由轉接器管理的明確關係 |

這些對應不會重新定義權威 PM 意義；完整 mapping integrity 由 PLAN02 與後續 GitHub 轉接器負責。

---

## 12. 術語變更控制

修改本文件時必須：

1. 指出新增或修改的術語；
2. 說明原本的模糊或不足；
3. 評估對既有 PM Skills 與轉接器的相容性影響；
4. 必要時同步更新相關權威契約；
5. 保留專案歷史，不得默默重新解讀已接受的舊紀錄。

本文件是 M1 的 terminology source of truth，直到被後續經核准的版本取代。