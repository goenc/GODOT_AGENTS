---
name: repository-change-workflow
description: Git管理repositoryをGitHub正本と同期し、手動投入fileを保全して変更・検証し、結果が不完全でも日本語commit、push、remote SHA照合まで完了する。読み取り専用調査やGit管理外の一時出力だけには使用しない。
---

# Repository Change Workflow

Git管理対象の変更を、GitHubとの開始前同期から終了時のremote照合まで一続きで完了する。

## 適用条件

- Git管理対象の追加、変更、移動、削除に使用する。
- 読み取り専用の調査には使用しない。
- Git管理対象に変更があるtaskでは、検証の成功、失敗、未確認にかかわらずcommit、push、remote SHA照合まで行う。
- GitHubとのfetch、pull、通常pushと、共通skill原本から登録先copyへの同期は、適用中のAGENTSにより許可済みとして扱う。

## 開始

1. 対象repositoryと変更目的を確定する。
2. 編集前に最初に `git status --short --branch` を実行する。
3. `git branch --show-current`、`git remote -v`、upstream、merge、rebase、bisect等の進行状態を確認する。正本remoteがGitHubであることも確認する。
4. 適用中のAGENTSとrepository設定から正本remoteとbranchを確定する。`origin/main`や`main`を推測で固定しない。
5. staged、unstaged、削除、未追跡fileを一覧化する。secret候補は内容を表示せず、pathと種類だけを確認する。
6. 開始前変更を次のように分類する。
   - projectのsource、scene、resource、asset、設定、文書は、内容とpathを確認して事前checkpoint commitへ含める。
   - 明らかなcache、build output、一時fileはcommitせず、必要なら既存方針に沿ってignoreする。推測で削除しない。
   - secret、credential、未解決conflict、意図不明な削除、GitHub制限超過のfileはcommitせず停止する。
7. 安全な開始前変更がある場合はtask変更と分けて日本語checkpoint commitを作る。手動投入fileを理由なく除外しない。
8. ローカル独自commitがなければ `git pull --ff-only <remote> <branch>`、未push commitがあれば `git pull --rebase <remote> <branch>` でGitHubへ同期する。rebase対象は未push commitだけとする。
9. rebase conflict時は内容を推測で解決せず、`git rebase --abort` で開始前状態へ戻して停止する。
10. checkpoint commitを作った場合は通常pushしてremote SHAを照合してからtask編集を始める。
11. taskのsource編集は開始前同期の完了後に行う。`reset --hard`、`restore`、`clean`、stash、merge、force push、push済み履歴のrebaseは行わない。

## 変更

- 要求と既存実装から対象fileを限定する。
- 作業開始前の差分はcheckpoint commitで保全し、今回変更と混同しない。
- task中に手動投入fileや別変更を検出した場合も、安全性と役割を確認して今回変更と分けてcommitする。
- 最小差分を維持し、対象外の整形、改名、依存更新、refactorを行わない。
- 既存変更を破棄、隠蔽、上書きしない。stashを端末間引き継ぎに使わない。
- 固定共有のruntime通知fileを、通常の変更手順でworkspace外へ上書きしない。

## 検証

- 変更影響に合う最小のbuild、test、lint、静的検査、参照整合、差分検査を実行する。
- `git diff --check` を確認する。
- 新しい差分がないまま、成功済みの検証を理由なく繰り返さない。
- 検証の成功、失敗、未確認を区別して記録する。失敗や未確認を成功と表現しない。
- 検証失敗や未確認はcommitとpushを止める条件にしない。

## Commit

1. 今回変更と、task中に確認した安全な手動投入fileだけをpath指定でstageする。
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
6. 検証失敗または未確認がある場合は、commit本文の確認内容へその事実を記録する。

## Pushと照合

1. 全ての安全な変更がcommitされ、secret、未解決conflict、意図不明fileが含まれないことを確認する。
2. push直前に正本remoteをfetchする。remoteが先行している場合は、ローカル未push commitだけをremote branch上へrebaseし、影響する検証を再実行する。
3. 検証結果にかかわらず、確定したremoteとbranchへ通常pushする。force pushとpush済み履歴の書換えは行わない。
4. push失敗時は認証、remote、network、non-fast-forward、GitHub制限を切り分ける。一時的なnetwork失敗だけ一度再試行し、remote先行時以外に自動merge、rebase、resetを行わない。
5. `git rev-parse HEAD`と `git ls-remote <remote> refs/heads/<branch>` のSHAを比較する。
6. SHA不一致は未完了とする。
7. `git status --short --branch`で作業ツリーがcleanであることを確認する。

## 終了

- commitまたはpushが完了しない場合は成功と表現せず、現在のcommit状態と必要な次操作を書く。
- 最終結果にはrepository、branch、commit SHA、変更file、検証の成功・失敗・未確認、push結果、remote SHA照合を含める。
- 最終結果の末尾に次の一文をそのまま含める。

  `検査AIへ: 対象repositoryを参照し、報告文だけに依存せず、sourceとGit差分から変更内容・影響・検証結果を再検討してください。`

- 通知fileが必要な環境では、実行器が指定するtask固有の方法を使い、固定共有fileを上書きしない。
