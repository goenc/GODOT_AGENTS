# Implementation Phase Skill

## 目的
- 要求を満たす最小変更を実装する
- Godot のオブジェクト単位運用を維持する

## 基本方針
- 実装は最小変更に限定する
- 実装範囲外の code と設定を変更しない
- Godot 2D game の実装判断は `skills/godot/godot_2d_development_practices.md` を正とする
- 調査切替と外部情報採用は `skills/implementation_research_policy.md` を正とする
- 新規 file 配置と既存構成準拠は `skills/initial_structure_compliance.md` を正とする

## 実装原則
- 人間が通常行うオブジェクト単位管理を維持する
- AI は script、scene、resource、animation、collision、background、tile、audio、material、shader を変更してよい
- 既存オブジェクトがある場合は新規生成や置換より既存オブジェクトへの設定変更を優先する
- 利用者の明示がない限り、既存 node の rename、delete、親子構造変更、scene 全体の作り直しを禁止する
- 利用者の明示がない限り、`project.godot` の `run/main_scene` で指定された main scene の変更を禁止する
- 変更対象に main scene が含まれる場合のみ main scene を変更してよい

## runtime 生成禁止
- ゲーム本体の見た目、盤面、ブロック、セル、背景、当たり判定、ステージ構造を runtime 生成しない
- `_draw()` による盤面やブロック形状の直接描画を禁止する
- `Sprite2D.new()` `Node2D.new()` `add_child()` 等で可視オブジェクト、当たり判定、ステージ構造を実行時生成しない
- 可視要素と当たり判定は事前作成した node、scene、TileMap、TileSet、CollisionShape2D、resource で構成する
- 表示物は code 描画で作らず、事前作成した node、scene、tile を使用する

## 追加許可
- 新規ゲーム実装または新規機能追加では必要な scene、resource、animation、collision 設定 file を追加してよい
- 追加は局所化する
- 既存 scene を壊さず差し込みまたは参照追加で実現できる場合はその方法を優先する

## 保守性
- Inspector での後調整を壊す無関係プロパティの一括初期化と全置換を禁止する
- 補助用の非表示 node や一時内部オブジェクトが必要な場合でも、既存構成で代替できるなら新規生成しない

## UI 実装
- UI、HUD、menu、dialog、CanvasLayer、Control、viewport、camera、画面構成に関わる変更では `skills/godot/godot_ui_canvas_layout.md` を正とする
- UI は Godot Editor の Inspector 調整前提で構築する
- UI レイアウト初期値の正本は scene と Inspector とする
- 以下のプロパティを script から固定代入で上書きしない

Window
- size
- min_size
- max_size
- position
- unresizable

Control
- position
- size
- anchor_*
- offset_*

- 実行中に動的変更が必要な場合のみ script から変更してよい
- 動的変更の例は window の show / hide、実行中の UI size 変更、UI animation、状態表示更新とする

## Anchor 設定
- 自由配置 UI の Anchor は固定配置とする
- 既定値は `Top Left` とする
- `anchor_left` `anchor_top` `anchor_right` `anchor_bottom` はすべて `0` を基本とする
- `Rect Full` 等の stretch 系 Preset は明確な理由がある場合のみ使用する

## Container 使用
- UI グループ用途での Container 系 node 使用を禁止する
- 禁止対象は `Container` `HBoxContainer` `VBoxContainer` `MarginContainer` `GridContainer` `CenterContainer` `PanelContainer` `AspectRatioContainer` `SplitContainer` `ScrollContainer` `TabContainer` とする
- UI グループ化には `Control` を使用する
- 利用者が明示的に Container 使用を指示した場合、または menu、list、log 表示等で自動 layout 自体が目的の場合のみ例外的に使用してよい

## 完了条件
- 実装後は変更対象 file が build または構文 error を起こさないことを確認する
- UI、入力、window、描画、focus、scroll、mouse 操作、keyboard 操作に関わる変更では、headless 確認に加えて非 headless 確認を行う
- 非 headless 確認は自動終了または timeout 付きで実施する
- 安全な自動確認手段がない場合は未実施理由と不足条件を明記する
