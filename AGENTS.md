# Agent Definition

## Core Principles
- 目的は要求を満たす最小変更の実現とする
- 開発運用は人間がエディタで管理するオブジェクト単位を前提とする
- AI は script だけでなく scene と resource の設定変更も担当してよい
- 既存オブジェクトがある場合は再生成や置換より既存オブジェクトへの設定変更を優先する
- node 名、NodePath、親子構造、外部 resource 参照は、利用者の明示がない限り維持する
- 無関係なリファクタリングと最適化を禁止する
- 現在状態の真実源は source code、scene、resource、設定 file とする
- 動作確認は対象差分に応じた最小確認とする
- project 本体を変更した場合の完了条件は、対象差分に応じた最小確認の成功とする
- `AGENTS.md` と `skills/` 配下 skill のみを変更した場合の完了条件は、文面整合と参照整合の確認とする

## Skill Loading
- skill 配置先は `skills/` 配下とする
- skill 一覧と path 解決の正本は `skills/skill_index.md` とする
- skill 読み込みは `skills/*.md` の浅い列挙を正本にしない
- `skills/skill_index.md` に記載された skill path を相対 path として順に解決する
- `skills/godot/...` を含む subdirectory 配下 skill を参照対象に含める
- path 解決基準は共通 `AGENTS.md` が存在する directory 基準とする
- 必須 skill は `skills/start_phase.md` `skills/implementation_phase.md` `skills/end_phase.md` `skills/end_phase_commit.md` `skills/end_phase_git_commit.md` `skills/end_phase_git_push.md` `skills/speech_output.md` とする
- 任意 skill は `skills/file_splitting.md` `skills/initial_structure_compliance.md` `skills/implementation_research_policy.md` `skills/godot/godot_tilemaplayer_stage_editing.md` `skills/godot/physics_signal_rules.md` `skills/godot/godot_executable_path.md` `skills/skill_index.md` とし、存在時のみ参照する
- 不存在の任意 skill を必須扱いしない
- 必須 skill は存在しない場合は失敗扱いとする
- `skill_index.md` に記載がある skill は、その記載 path を優先して読み込む
- `skills/*.md` の旧直下限定挙動は補助扱いとし、正本にしない
- `skills/file_splitting.md` は file 分割規則として参照する
- `skills/initial_structure_compliance.md` は初期構成配置規則として参照する
- `skills/implementation_research_policy.md` は実装時の調査切替と外部情報採用規則として参照する
- `skills/godot/godot_tilemaplayer_stage_editing.md` は Godot 4.6 の地形編集方針として参照する
- `skills/godot/physics_signal_rules.md` は Godot 4 の physics signal 制約として参照する
- `skills/godot/godot_executable_path.md` は Godot 実行 file の PATH 解決規則として参照する
- `AGENTS.md` と `skills/` 配下 skill が矛盾する場合は、対象 task に対してより具体的な規則を優先する

## Phase Routing
- 実行順は `Start -> Implementation -> End` とする
- 起動条件は実装依頼または `AGENTS.md` / `skills/` 配下 skill の変更依頼とする
- 実装依頼の完了範囲は End Phase までとする
- `AGENTS.md` / `skills/` 配下 skill 変更依頼の完了範囲も End Phase までとする
- End Phase の実行順序は `skills/end_phase.md` に従う
- 利用者が明示的に「コミットしない」と指示した場合のみ git commit を省略する
- 利用者が明示的に「プッシュしない」と指示した場合のみ git push を省略する

## Start Phase
- `skills/start_phase.md` を正とする

## Implementation Phase
- `skills/implementation_phase.md` を正とする

## End Phase
- `skills/end_phase.md` を正とする
- コミット関連は `skills/end_phase_commit.md` を参照する
- 発話関連は `skills/speech_output.md` を参照する