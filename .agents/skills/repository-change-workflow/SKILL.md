---
name: repository-change-workflow
description: Git管理リポジトリのファイル追加・変更・移動・削除を、安全確認、最小検証、日本語commit、明示許可時のpush、remote SHA照合まで完了する。読み取り専用調査やGit管理外の一時出力だけには使用しない。
---

# Repository Change Workflow

Git管理対象の変更を、安全確認から必要なremote反映まで一続きで完了する。

## 適用条件

- Git管理対象の追加、変更、移動、削除に使用する。
- 読み取り専用の調査には使用しない。
- commit、push、network、workspace外writeは、現在taskで必要かつ利用者が明示的に許可した場合だけ行う。

## 開始

1. 対象repositoryと変更目的を確定する。
2. 編集前に最初に `git status --short --branch` を実行する。
3. `git branch --show-current`、`git remote -v`、upstream、merge/rebase/bisect等の進行状態を確認する。
4. 適用中のAGENTS、repository設定、利用者の依頼から正本remoteとbranchを確定する。`origin/main`や`main`を推測で固定しない。
5. 未commit変更、未追跡file、競合、進行中operationがある場合は、利用者の作業を保持したまま原則停止する。利用者が既存差分を今回taskの入力として明示し、安全にpathを分離できる場合だけ限定して続行する。
6. remote操作が必要でcleanな場合だけ、確定したremoteとbranchをfetchする。behindならfast-forwardだけで更新し、分岐時は停止する。
7. pull、reset、restore、clean、stash、merge、rebase、force pushを自動実行しない。

## 変更

- 要求と既存実装から対象fileを限定する。
- 作業開始前の差分を今回変更と混同しない。
- 同一fileに無関係な既存hunkがある場合は、今回hunkだけを編集してstageする。安全に分離できなければ停止する。
- 最小差分を維持し、対象外の整形、改名、依存更新、refactorを行わない。
- 既存変更を破棄、隠蔽、上書きしない。stashを端末間引き継ぎに使わない。
- 固定共有のruntime通知fileを、通常の変更手順でworkspace外へ上書きしない。

## 検証

- 変更影響に合う最小のbuild、test、lint、静的検査、参照整合、差分検査を実行する。
- `git diff --check` を確認する。
- 新しい差分がないまま、成功済みの検証を理由なく繰り返さない。
- 自動確認できない内容は未確認として残す。

## Commit

1. 今回変更したpathだけを `git add -A -- <path...>` でstageする。
2. `git diff --staged --name-status`、`git diff --staged --check`、`git diff --staged`を確認する。
3. stage対象がなければ空commitを作らない。
4. commit messageは次の日本語形式とする。

```text
変更内容が分かる短いタイトル

理由:
変更理由

変更点:
主な変更内容

確認内容:
実行した確認と結果
```

5. task固有の安全なmessage fileまたは `git commit -m` を使用してcommitする。共有profile配下の固定message fileを上書きしない。

## Pushと照合

1. pushは利用者の明示許可または対象repositoryの明示規則がある場合だけ行う。
2. 確定したremoteとbranchへpushする。通常開発のbranchを推測しない。
3. dirty状態のpush、force push、rebase、履歴書換えを行わない。
4. push失敗時は認証、remote、network、non-fast-forwardを切り分ける。remote先行や分岐の場合に自動pull、merge、rebase、resetを行わない。
5. `git rev-parse HEAD`とremote tracking refまたは`git ls-remote`のSHAを比較する。
6. SHA不一致は未完了とする。
7. `git status --short --branch`で今回の差分が残っていないことを確認する。既存差分はそのまま残す。

## 終了

- commitまたはpushが完了しない場合は成功と表現せず、現在のcommit状態と必要な次操作を書く。
- 通知fileが必要な環境では、実行器が指定するtask固有の方法を使い、固定共有fileを上書きしない。
