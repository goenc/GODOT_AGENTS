# Godot 4 Physics Signal Rules

## 対象
- Godot 4 系 2D 物理 signal 実装

## 目的
- `Can't change this state while flushing queries`
- `Function blocked during in/out signal`
- 上記エラーの防止

## 禁止
- 物理 signal 内で物理監視、衝突、処理有効化、削除に関わる即時状態変更を行わない
- `monitoring = true/false`
- `monitorable = true/false`
- `CollisionShape2D.disabled = true/false`
- `queue_free()`
- `set_process(true/false)`
- `set_physics_process(true/false)`

## 規則
- 物理 signal 内の状態変更は deferred 系を使用する
- `monitoring` は `set_deferred("monitoring", false)` を使用する
- `monitorable` は `set_deferred("monitorable", false)` を使用する
- `CollisionShape2D.disabled` は専用関数へ分離し `call_deferred()` で呼ぶ
- `queue_free()` は `call_deferred("queue_free")` を使用する
- `set_process` `set_physics_process` は専用関数へ分離し `call_deferred()` で呼ぶ
- `body_entered` `area_entered` `body_exited` `area_exited` では直接状態変更せず、窓口関数のみ呼ぶ

## 例
```gdscript
func deactivate() -> void:
	visible = false
	set_deferred("monitoring", false)
	set_deferred("monitorable", false)
	call_deferred("_disable_collision")
	call_deferred("_stop_processing")
	call_deferred("queue_free")

func _disable_collision() -> void:
	if has_node("CollisionShape2D"):
		$CollisionShape2D.disabled = true

func _stop_processing() -> void:
	set_process(false)
	set_physics_process(false)

func _on_body_entered(body: Node) -> void:
	deactivate()