# End Phase Commit Skill

## 目的
- End Phase のコミットメッセージ file を生成する

## 出力先
- `runtime/commit_message.md`

## 保存仕様
- 出力先は作業対象 project 配下 `runtime/` とする
- `runtime/` がなければ作成する
- `commit_message.md` は毎回上書きする
- UTF-8
- LF 改行
- 末尾改行あり
- 日本語のみ

## commit_message 仕様
- 1行目は今回作業要約とする
- 2行目は空行とする
- 3行目以降は全角 `・` の箇条書きとする
- 記載対象は今回の変更内容と確認内容のみとする
- 変更なし時は 1行目を `変更なし`、2行目以降を `なし` とする

## 実行順序
1. 変更有無を判定する
2. `commit_message.md` を上書き生成する

## 失敗時
- 書き込み失敗時は `runtime/` 作成を1回試行し、同一 file へ1回再試行する
- 再試行でも失敗した場合は中断し、失敗理由を対話で明示する

## 禁止
- `runtime/` 以外へ保存しない
- `commit_message.md` を追記しない
- 日本語以外で記述しない
- 推測補完しない