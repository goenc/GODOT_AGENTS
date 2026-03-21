# End Phase Git Commit Skill

## 目的
- `work` ブランチで `runtime/commit_message.md` を用いて git commit を実行する

## 入力
- `runtime/commit_message.md`

## 実行規則
- 利用者が明示的に「コミットしない」と指示した場合は実行しない
- 作業対象 project ルートで実行する
- `main` では commit しない
- `work` が存在すれば切り替える
- `work` が存在しなければ現在 HEAD から作成して切り替える
- `runtime/commit_message.md` が存在しない場合は中断する
- `git status --porcelain` が空の場合は `変更なし` として終了する
- 既存変更があっても停止しない
- `git add -A` を実行する
- `git commit -F runtime/commit_message.md` を実行する

## 失敗時
- `git switch` `git add` `git commit` 失敗時は中断し、標準エラー出力を対話で明示する

## 禁止
- `main` で commit しない
- `git reset` を実行しない
- `git revert` を実行しない
- `git rebase` を実行しない
- 履歴を書き換えない