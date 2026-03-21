# End Phase Skill

## 目的
- End Phase の実行順序を固定する

## 実行順序
1. `skills/end_phase_commit.md`
2. `skills/end_phase_git_commit.md`
3. `skills/end_phase_git_push.md`
4. `skills/speech_output.md`

## 実行規則
- 下位 skill は上記順序で実行する
- 利用者が明示的に「プッシュしない」と指示した場合は 3 を省略する
- 発話単独生成時のみ `skills/speech_output.md` を単独実行してよい

## 失敗時
- 下位 skill 失敗時は中断し、失敗理由を対話で明示する

## 禁止
- 下位 skill 規則を上書きしない