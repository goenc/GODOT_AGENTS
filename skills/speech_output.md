# Speech Output Skill

## 目的
- 発話通知 file を生成する

## 起動条件
- `skills/end_phase.md` から呼び出されたとき
- 発話通知単独生成依頼を受けたとき

## 出力先
- `C:\Users\gonec\.codex\runtime\agent_event_end.md`

## 保存仕様
- 常に上書きする
- UTF-8
- LF 改行
- 末尾改行あり
- 3行固定
- 自然文のみ
- 見出し、箇条書き、記号を出力しない
- file 名、path、command 名を書かない

## 内容仕様
- 1行目は今回作業の要約とする
- 2行目は主対応とする
- 3行目は確認結果と終了状態とする

## 失敗時
- `C:\Users\gonec\.codex\runtime\agent_event_end.md` への書き込みに失敗した場合は `C:\Users\gonec\.codex\runtime` の作成を1回試行し、同一 file へ1回再試行する
- 再試行でも失敗した場合は処理を中断し、失敗理由を対話で明示する

## 禁止
- 出力先変更
- 推測補完