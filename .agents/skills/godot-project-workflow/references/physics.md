# Physics

## 基本

- physics movement、collision応答、`CharacterBody2D` の移動は `_physics_process()` と既存physics設計に合わせる。
- `Area2D` は検知、`PhysicsBody2D` は物理挙動という既存の役割を維持する。
- collision layerとmaskのproject内意味を確認し、番号を推測で変更しない。

## Physics signal

- `body_entered`、`area_entered`、`body_exited`、`area_exited` などphysics query中のsignalでは、engineがquery flush中の変更を拒否するpropertyを即時変更しない。
- `monitoring`、`monitorable`、`CollisionShape2D.disabled` などは、必要に応じて `set_deferred()` または限定したdeferred methodで変更する。
- node削除やprocess切替をdeferredにする必要がある場合は、対象操作だけを小さいmethodへ分離する。
- game state更新まで無条件にdeferred化しない。実際にphysics server制約を受ける操作へ限定する。

## Performance

- 毎physics frameの全node走査、`find_child()`、不要なallocationを追加しない。
- poolingは測定または実際のspawn負荷がある場合だけ検討する。

## 確認

- collision、signal、movement変更では対象sceneを実行し、enter、exit、削除、再spawn等の関連経路を確認する。
- headlessで再現できない入力やgame feelは未確認条件を明示する。
