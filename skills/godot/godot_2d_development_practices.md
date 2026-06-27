# Godot 2D Development Practices

## 目的
- Godot 2D game 開発で壊れやすい設計、重くなりやすい実装、後調整不能な構成を避ける
- scene、script、resource、signal、input、physics の責務を分け、Editor で調整できる状態を保つ

## 根拠
- Godot official docs: Best practices https://docs.godotengine.org/en/stable/tutorials/best_practices/index.html
- Godot official docs: Scene organization https://docs.godotengine.org/en/stable/tutorials/best_practices/scene_organization.html
- Godot official docs: Nodes and Scenes https://docs.godotengine.org/en/stable/getting_started/step_by_step/nodes_and_scenes.html
- Godot official docs: Nodes and scene instances https://docs.godotengine.org/en/stable/tutorials/scripting/nodes_and_scene_instances.html
- Godot official docs: Scene Unique Nodes https://docs.godotengine.org/en/stable/tutorials/scripting/scene_unique_nodes.html
- Godot official docs: Input examples https://docs.godotengine.org/en/stable/tutorials/inputs/input_examples.html
- Godot official docs: Singletons Autoload https://docs.godotengine.org/en/stable/tutorials/scripting/singletons_autoload.html
- Godot official docs: Node processing https://docs.godotengine.org/en/stable/classes/class_node.html

## 基本方針
- game object は再利用可能な scene 単位で作る
- script だけで見た目、当たり判定、UI、地形を組み立てない
- scene の node 構成、Inspector 設定、resource を正本にする
- script は状態遷移、入力処理、計算、scene 間連携に寄せる
- 直接参照、global state、巨大 manager に寄せすぎない

## 使った方がいいもの
- player、enemy、bullet、item、effect、UI panel は個別 scene として作る
- 動的に出す object は事前作成 scene を `PackedScene` として instantiate する
- 調整値は `@export`、custom `Resource`、設定 file に寄せる
- node 参照は `_ready()` 以降に `@onready` で取得する
- 同一 scene 内の重要 node 参照には scene unique node を使ってよい
- scene をまたぐ通知には signal を使う
- 多数 object への一括通知や検索には group を使ってよい
- 入力は Input Map の action を使う
- 物理移動、衝突、CharacterBody2D の movement は `_physics_process()` に置く
- visual effect、animation 補助、非物理 UI 更新は `_process()` に置く
- frame rate 依存を避けるため、時間変化には `delta` を使う
- 削除は原則 `queue_free()` を使う

## 使わない方がいいもの
- 画面や object を script の `new()` と `add_child()` だけで大量生成しない
- 動的 object に raw `Sprite2D.new()`、`CollisionShape2D.new()`、`Node2D.new()` を使って見た目や当たり判定を組まない
- scene 外の深い node を hard-coded NodePath で直接参照しない
- `/root/...` や `..` を多用して親や他 scene の内部構造へ依存しない
- `find_child()` を通常の runtime 参照取得に使わない
- すべてを Autoload singleton に集めない
- すべてを node として表現しない
- key code 直書きで player input を処理しない
- physics object を `_process()` で動かさない
- physics signal 中に collision、monitoring、queue_free を即時変更しない
- `free()` は明確な理由がない限り使わない
- magic number を script 内に散らさない

## Scene 分割
- 1 scene は 1 つの概念に寄せる
- 再利用する object は早めに scene 化する
- scene 間の内部 node へ直接依存しない
- 親 scene は子 scene の公開 API、signal、export property を使う
- 子 scene は親 scene の具体構造を知らないようにする
- rename で壊れる NodePath は局所化する

## Autoload
- Autoload は global state ではなく global service に限定する
- 設定、save data、scene transition、audio bus 管理など、常時必要なものだけに使う
- enemy manager、UI manager、game manager を安易に Autoload 化しない
- scene 内で完結する責務は通常 node に置く
- Autoload へ scene node 参照を長期間保持しない

## Input
- action 名は意味で命名する
- `move_left`、`move_right`、`attack`、`dash` のように game 操作として定義する
- keyboard、gamepad、mouse の差は Input Map 側へ寄せる
- UI 用 action と gameplay 用 action を混ぜない
- input 処理で直接 scene tree を大きく変更しない

## Physics
- collision layer と mask は project 内で意味を固定する
- layer 番号の意味をコメントまたは設定 file に記録する
- Area2D は検知、PhysicsBody2D は物理挙動に使い分ける
- 物理 signal 内の状態変更は `skills/godot/physics_signal_rules.md` に従う
- 高頻度 spawn object は必要に応じて pool を検討する

## Resource と Data
- 速度、HP、damage、cooldown、spawn 設定は script 内定数だけに閉じ込めない
- 調整対象は `@export` または custom `Resource` にする
- scene 固有の見た目や collision は scene/resource 側へ置く
- JSON や CSV は大量設定や外部編集が必要な場合だけ使う
- data と表示 node を相互依存させない

## Performance
- 毎 frame の `get_node()`、`find_child()`、全 node 走査を避ける
- 大量 bullet、particle、damage number は scene 化と pool を検討する
- 常時不要な `_process()`、`_physics_process()` は有効化しない
- node 数が過剰になる表現は TileMapLayer、MultiMesh、resource data、描画専用手段を検討する
- 最適化は測定後に行う

## 確認
- source、scene、resource を変更したら、対象差分に応じた最小確認を行う
- physics、input、UI、camera、spawn、scene transition は実行確認を優先する
- 表示や操作に関わる変更は非 headless 確認を行う
- 自動確認できない場合は、未確認の条件を明示する
