---
name: branch-aware-commits
description: Always use this skill for every git commit operation to ensure commit messages follow branch-based prefix conventions and that the project still restores, builds, and tests correctly after dependency cleanup.
---

# Branch-Aware Commits

**執行此 skill 時，請立即遵照以下說明執行 commit**

## Overview

確保 commit message 根據當前 branch 名稱自動加上正確的前綴。當 branch 名稱**任意位置**包含 `VSTS\d+` 模式時，commit message 必須以擷取到的 VSTS ticket 號碼加上 ` - ` 作為前綴。

**核心原則**: 先取得 branch 名稱 → 判斷是否符合規則 → 決定 commit message 格式。

## When to Use

- 執行 `git commit` 時
- 使用任何工具產生 commit message 時
- 當 branch 名稱可能包含 VSTS ticket 編號時
- 當執行模式為 autopilot 時，請自動協助完成 commit 與 push 到 origin

## Autopilot Mode Behavior

- 若目前為 autopilot 模式，完成變更後請自動處理 commit 與 push，無需額外等待使用者確認。
- 先依照上述 branch 規則決定 commit message 前綴，再執行 `git add`、`git commit` 與 `git push`。
- 若建置或驗證流程失敗，先回報失敗原因並暫停自動提交；若已有可保留的變更，則可先提交一版後再處理修正。

## Core Pattern

### 步驟

1. **取得當前 branch 名稱並擷取 VSTS ticket 號碼**:
```bash
git branch --show-current | grep -oP 'VSTS\d+'
```
   - 若有輸出（如 `VSTS1234`）→ 該輸出即為前綴
   - 若無輸出（空字串）→ 不符合規則，不加前綴

2. **判斷 branch 名稱是否包含 `VSTS\d+` 模式（任意位置）**:
   - 符合範例: `VSTS1234`, `feature/VSTS99-login`, `fix-VSTS12345-bug`
   - 不符合範例: `feature/login`, `main`, `develop`, `vsts-1234`（大小寫敏感）

3. **組成 commit message**:
   - ✅ 符合規則 → `VSTS<數字> - <commit 描述>`
   - ❌ 不符合規則 → 正常 commit message，不加前綴，直接完成 commit

4. **不符合規則時的行為**:
   - 當 branch 名稱不符合 `VSTS\d+` 模式時，不需要加任何前綴
   - 直接使用原始的 commit 描述作為 commit message
   - 不需要額外確認或提示使用者，直接執行 commit

5. **每次 commit 前必須完成的驗證**:
   - 在提交前，務必確認專案可正確完成其標準的建置與驗證流程。
   - 這包括但不限於：
     1. 重新建置專案
     2. 執行測試或其他必要驗證
   - 若其中任一步驟失敗，應先說明失敗原因，不要自動修正
   - 接著先提交一版目前可保留的變更，之後再針對失敗原因進行修正，最後重新 commit 一次
   - 若專案使用不同的套件管理或建置工具，請以其對應的 restore/build/test 或等價流程替代，但原則相同：清除依賴後仍必須成功驗證

### 範例

```bash
# Branch: VSTS1234
git commit -m "VSTS1234 - 新增使用者登入功能"

# Branch: feature/VSTS567-login
git commit -m "VSTS567 - 修正 API 回應格式"

# Branch: fix-VSTS99-bug
git commit -m "VSTS99 - 修正錯誤"

# Branch: feature/login (不符合規則，不加前綴)
git commit -m "新增使用者登入功能"
```

## Quick Reference

| Branch 名稱 | 是否符合 | Commit Message 格式 |
|---|---|---|
| `VSTS1234` | ✅ | `VSTS1234 - <描述>` |
| `VSTS99` | ✅ | `VSTS99 - <描述>` |
| `feature/VSTS12345-login` | ✅ | `VSTS12345 - <描述>` |
| `fix-VSTS567-bug` | ✅ | `VSTS567 - <描述>` |
| `feature/login` | ❌ | `<描述>` |
| `main` | ❌ | `<描述>` |
| `vsts-1234` | ❌ | `<描述>` |
| `VSTS` | ❌ | `<描述>`（無數字） |

## Common Mistakes

| 錯誤 | 正確 |
|---|---|
| `VSTS1234 新增功能` (缺少 ` - `) | `VSTS1234 - 新增功能` |
| `vsts1234 - 新增功能` (小寫) | `VSTS1234 - 新增功能` |
| 忘記先執行 `git branch --show-current` | 每次 commit 前必須先確認 branch 名稱 |
| 對不符合規則的 branch 也加前綴 | 僅在符合 `VSTS\d+` 時加前綴 |
