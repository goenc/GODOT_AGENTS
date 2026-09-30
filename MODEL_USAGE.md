# Godot開発のモデル・使用量・キャッシュ運用

2026-09-30確認。C#・Android版と同じ運用方針をGodotへ適用する。本書は運用調整時だけ読み、通常のproject作業はAGENTSと必要なskill referenceを使う。

## モデルの選択

| 作業 | 第一候補 | 判断 |
| --- | --- | --- |
| 明確な局所修正、既存scene・script方式に沿う実装、文書更新 | GPT-6 Luna / max | 対象を絞り、必要な構文・実行確認で精度を確保 |
| scene責務、resource構成、複数system間の設計判断 | GPT-6.1 Sol / medium | 設計上の判断をまとめる |
| 原因不明のruntime障害、複雑な状態遷移・並行処理、データ保全 | GPT-6.1 Sol / high | 判断の難しさと失敗の影響を優先 |

これは本repositoryの運用推奨。Luna maxでSolと同じ精度になるとは保証しない。Solのmax・xhighは、実測で効果がある難題に限定する。利用者の指定を優先し、モデル選択・推論設定はCodexのUIまたは設定で変更する。AGENTSだけでは実設定を切り替えられない。

同じ課題を両モデルに毎回解かせない。切替時は目的、対象project・engine version、commit、再現条件、実施済み確認、残件を短く引き継ぐ。通常は親agentだけで進め、独立作業の利益が追加使用量を上回る場合に限りsubagentを1体まで使う。

## Plusの5時間枠

- Standard速度を優先する。Fastは短時間で終わっても利用枠の消費が増える。[Codexの料金・使用枠](https://learn.chatgpt.com/docs/pricing)
- 依頼は観測できる一つの成果へ区切り、関連source・scene・logだけを渡す。同じ指示や過去会話を毎回全文添付しない。
- 長時間作業の開始時や警告時に公開された使用量を確認する。毎回の監視や全log走査は行わない。残り20%は任意作業を抑える目安で、公式の安全残量ではない。
- 必要な実装、検証、commit・pushを完了する。使用量を節約するために非headless確認や必要なtestを省略しない。
- 作業の目的が変わり長い文脈が不要になった場合だけ、新しいチャットへ短い要約で引き継ぐ。新規チャット・モデル切替で利用枠がリセットされるとは扱わない。

文書の短縮率は利用枠の削減率ではない。API単価やメッセージ数の目安から、このアカウントの残り作業数を計算しない。実作業数件でモデル・推論、開始前後の使用量、修正往復、検証結果を比較する。同時利用があれば表示差分を当該作業だけの消費とみなさない。

## プロンプトキャッシュ

同じ入力の先頭部分を再利用できるよう、固定指示・共通資料・tool定義の内容と順序を安定させ、今回固有の情報を後ろへ追加する。実際の入力構成とcache処理はCodex実行基盤が管理する。ヒットをAGENTSから強制できるとは扱わない。[OpenAIのキャッシュ仕様](https://developers.openai.com/api/docs/guides/prompt-caching)、[Codex CLIの構成例](https://developers.openai.com/cookbook/examples/prompt_caching_201)

- 同じ開発作業は既存チャットで続け、追加指示を後続メッセージで渡す。cache目的だけの指示全文再貼付を避ける。
- 現在時刻、使用量、進捗、今回のcommit等を固定指示へ埋め込まない。AGENTS・skill・tool設定を理由なく変更・並べ替えしない。
- モデル切替や文脈圧縮で同じcacheを再利用できるとは仮定しない。精度確保や文脈整理が必要な場合はそちらを優先する。
- 取得済みsource、公式資料、検証結果は変更と鮮度を確認して再利用する。古い結果を現在の証拠として使わない。
- cache維持のための定期request、空の会話、不要な資料読込、長文追加を行わない。公開されたヒット情報がある場合だけ実測評価する。

APIのcache key・保持時間等はAPI実装向けの設定。未確認のCodex設定へ転用しない。ヒット率とPlus利用枠への実際の削減効果は未計測。

## Godotのimport・buildキャッシュ

Godot 4の生成import cacheは `.godot/imported/` にあり、asset横の `.import` はimport設定。通常の開発では既存cacheを使い、source assetやimport設定の変更に必要な再importを行う。生成cacheを消すと再importが必要になるため、証拠なしに全削除しない。[Godot公式のimport手順](https://docs.godotengine.org/en/stable/tutorials/assets_pipeline/import_process.html)

- 対象projectと適合するengineを使う。別majorのcache配置や互換性は推測しない。
- `.import` や対象versionで管理する `.uid`、resource参照を生成cacheと混同しない。既存の追跡方針を維持する。
- C#では既存NuGet cacheと増分buildを使う。変更に必要なbuild・testを行い、古いbinaryだけで確認済みと扱わない。
- Parser・import成功だけでgameplay・visual・inputの成功と判定しない。MCPで対象projectを照合し、変更に応じて安全な非headless確認を行う。

起動・復旧・共有サーバーの詳細はGodot skillの [editor-mcp.md](.agents/skills/godot-project-workflow/references/editor-mcp.md)、検証とcacheの詳細は [build-and-test.md](.agents/skills/godot-project-workflow/references/build-and-test.md) を必要時だけ読む。

## 適用と公式の根拠

共通AGENTSは各projectで有効な配置に置くか必要時に参照させる。このrepositoryへの保存だけでは別projectへ自動適用されない。登録済みskillの同期とは別の仕組み。

- [AGENTS.mdの探索・優先順](https://learn.chatgpt.com/docs/agent-configuration/agents-md): 共通規則とproject別規則の適用範囲。
- [スキル・指示文の見直し](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra): 短い説明、必要時の参照読込、過剰な手順の削減。構成原則を参考にし、Godotの検証要件は維持。
- [GPT-6.1 Sol](https://developers.openai.com/api/docs/models/gpt-6.1-sol)・[GPT-6 Luna](https://developers.openai.com/api/docs/models/gpt-6-luna): 特性と推論設定。API仕様とCodex利用枠は区別。
- [サブエージェント](https://learn.chatgpt.com/docs/agent-configuration/subagents): 独立作業の分担と追加使用量。
