# End Phase Git Push Skill

## 目的
- 現在 branch を origin へ push する

## 実行規則
- 利用者が明示的に「プッシュしない」と指示した場合は実行しない
- 作業対象 project ルートで実行する
- 現在 branch は `git branch --show-current` で取得する
- branch 名が空の場合は中断し、標準エラー出力を対話で明示する
- upstream 確認は `git rev-parse --abbrev-ref --symbolic-full-name "@{u}"` で行う
- upstream ありの場合は `git push` を実行する
- upstream なしの場合は `git push -u origin <current-branch>` を実行する

## 失敗時
- push 失敗時は中断し、標準エラー出力を対話で明示する