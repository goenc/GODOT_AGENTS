# Godot Executable Path Rule

## 目的
- Godot 実行 file 探索を廃止し、PATH 解決を前提に実行する

## 規則
- Godot 実行 file は PATH 解決された `godot` または `godot_console` を使用する
- 毎回の自動探索を行わない
- `where godot` `where godot_console`、再帰探索、候補総当たりを既定動作にしない
- 用途ごとに固定コマンドを使用する

## 既定コマンド
- GUI 起動は `godot`
- コンソール起動は `godot_console`
- テスト、起動確認、非 headless 確認で GUI 起動が必要な場合は `godot` を使用する
- テスト、起動確認、headless またはコンソール確認が必要な場合は `godot_console` を使用する

## 失敗時
- `godot` または `godot_console` が実行できない場合のみ中断する
- 失敗理由を対話で明示する
- 代替 path を推測しない