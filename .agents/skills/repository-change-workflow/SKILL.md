---
name: repository-change-workflow
description: Git管理リポジトリのファイル追加・変更・移動・削除を、安全確認、検証、日本語commit、origin/mainへのpush、remote SHA照合まで完了する。読み取り専用調査やGit管理外の一時出力だけには使用しない。
---

# Repository Change Workflow

GitHubの `origin/main` を共有済み状態の正本とし、複数端末のlocal copyを安全に同期する。

## 開始

1. 対象repositoryと変更目的を確定する。
2. 最初のGit操作として `git status --short --branch` を実行する。
3. `git branch --show-current`、`git remote -v`、upstream、merge/rebase/bisect等の進行状態を確認する。
4. `origin` が対象repositoryを指し、`origin/main` が存在し、現在branchが `main` であることを確認する。
5. 未commit変更、未追跡file、競合、進行中operationがある場合は、利用者の作業を保持して停止する。
6. 利用者が既存差分を今回taskの入力として明示し、処置を許可した場合だけ、そのfileを含めるか個別にignoreするかを確定して続行してよい。
7. 安全確認後に `git fetch origin main` を実行する。local `main` がbehindなら `git merge --ff-only origin/main` だけを許可する。
8. fast-forwardできない、localとremoteが分岐している、remoteを確認できない場合は、自動merge、rebase、reset、restore、clean、stashを行わず停止する。

実装、`AGENTS.md`、skill変更では、source調査前に現在ユーザーのprofile配下 `.codex/runtime/agent_event_start.md` をUTF-8、LF、自然文2行で上書きする。1行目は要求理解、2行目は次行動。親directoryがなければ一度だけ作成を試す。

## 変更

- 要求と既存実装から対象fileを限定する。
- 作業開始前の差分を今回変更と混同しない。
- 同一fileに無関係な既存hunkがある場合は、今回hunkだけを編集してstageする。安全に分離できなければ停止する。
- 最小差分を維持し、対象外の整形、改名、依存更新、refactorを行わない。
- 既存変更を破棄、隠蔽、上書きしない。stashを端末間引き継ぎに使わない。

## 検証

- 変更影響に合う最小のbuild、test、lint、静的検査、参照整合、差分検査を実行する。
- `git diff --check` を確認する。
- 新しい差分がないまま、成功済みの検証を理由なく繰り返さない。
- 自動確認できない内容は未確認として残す。

## Commit

1. 今回変更したpathだけを `git add -A -- <path...>` でstageする。
2. `git diff --staged --name-status`、`git diff --staged --check`、`git diff --staged` を確認する。
3. stage対象がなければ空commitを作らない。
4. 現在ユーザーのprofile配下 `.codex/runtime/commit_message.md` をUTF-8、LF、末尾改行ありで上書きする。
5. commit messageは次の日本語形式とする。

```text
変更内容が分かる短いタイトル

理由:
変更理由

変更点:
主な変更内容

確認内容:
実行した確認と結果
```

6. `git commit -F <commit-message-path>` を実行する。

## Pushと照合

1. `git push origin main` を実行する。通常開発で別branchへpushしない。
2. dirty状態のpush、force push、rebase、履歴書換えを行わない。
3. push失敗時は認証、network、non-fast-forwardを切り分け、安全に修正できる場合だけ一度再試行する。
4. remote先行や分岐の場合は自動pull、merge、rebase、resetを行わず停止する。
5. `git rev-parse HEAD` と `git ls-remote origin refs/heads/main` のSHAを比較する。
6. SHA不一致は未完了とする。
7. `git status --short --branch` で今回差分が残っていないことを確認する。

## 終了通知

実装、`AGENTS.md`、skill変更の終了時は、現在ユーザーのprofile配下 `.codex/runtime/agent_event_end.md` をUTF-8、LF、末尾改行あり、自然文3行で上書きする。

- 1行目は作業要約。
- 2行目は主対応。
- 3行目は確認結果と完了または未完了状態。
- 見出し、箇条書き、file名、path、command名を書かない。

通知fileの書込みに失敗した場合は親directory作成後に一度だけ再試行し、変更結果と分けて報告する。

## 禁止

- 利用者がcommitまたはpushの省略を求めた状態でfileを変更しない。読み取り結果か変更案だけを返す。
- `main` 以外で通常開発を続行しない。
- `git add -A` をpath指定なしで実行しない。
- `git reset`、`git restore`、`git clean`、`git rebase`、force push、履歴書換えを行わない。
- 同じrepositoryの未push作業を複数端末で並行しない。
