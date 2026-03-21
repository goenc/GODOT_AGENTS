# Start Phase Skill

## 目的
- 受信要求の理解と次行動を読み上げ専用 file に上書き出力する

## 起動条件
- 実装依頼を受けたとき
- `AGENTS.md` または `skills/*.md` の変更依頼を受けたとき

## 許可処理
- 共通 `AGENTS.md` の読み込み
- 必須 skill の読み込み
- 作業対象 project の特定
- project 直下 `AGENTS.md` の存在確認
- project 直下 `AGENTS.md` の妥当性確認
- `agent_event_start.md` の上書き出力

## 禁止処理
- 作業対象 project 配下 `scenes/` `scripts/` `data/` `tools/` の内容調査
- `tools/run.ps1` の読み込み
- `rg` `git` `Get-ChildItem` による対象 source 探索
- プロセス停止
- 実行確認

## AGENTS 確認
- project 直下 `AGENTS.md` を参照する場合は先頭見出しが `# Agent Definition` であることを確認する
- 先頭見出しが一致しない場合は AGENTS 本体として採用しない
- 不正な `AGENTS.md` は誤配置として扱う
- 誤配置時は共通 `AGENTS.md` を優先する
- 誤配置を対話で明示する

## 出力先
- `C:\Users\gonec\.codex\runtime\agent_event_start.md`

## 出力仕様
- 上書き保存
- UTF-8
- LF 改行
- 2行固定
- 自然文のみ
- 記号、見出し、箇条書き禁止

## 出力内容
- 1行目は要求理解を書く
- 2行目は次行動を書く

## 失敗時
- `C:\Users\gonec\.codex\runtime\agent_event_start.md` への書き込みに失敗した場合は `C:\Users\gonec\.codex\runtime` の作成を1回試行し再書き込みする
- 再書き込みでも失敗した場合は処理を中断し失敗理由を対話で明示する