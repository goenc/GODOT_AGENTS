# Code Scenes and Resources

## Sceneとresource

- game objectは、既存projectが採用する再利用可能なscene単位を維持する。
- visual、collision、animation、editor調整値は、既存scene、resource、Inspector設定を正本にする。
- dynamic objectは、既存の `PackedScene` をinstantiateする方式を優先する。
- 既存sceneで表現できるobjectを、raw `Node2D.new()`、`Sprite2D.new()`、`CollisionShape2D.new()` の組合せへ理由なく置き換えない。
- main sceneは、現在taskがmain scene自体を対象にする場合だけ変更する。

## Script

- scriptは状態遷移、入力、計算、scene間連携へ寄せ、Inspector調整可能な値を固定値で支配しない。
- 調整値は既存方式に合わせて `@export`、custom `Resource`、定数、設定fileを選ぶ。新しい設定systemを作らない。
- node参照は既存方式を維持し、深いhard-coded NodePath、`/root/...`、通常runtimeでの `find_child()` の追加を避ける。
- scene間連携は、既存の公開method、signal、groupを優先する。
- time依存処理では必要に応じて `delta` を使う。physics movementは `_physics_process()`、非physics更新は用途に合うcallbackへ置く。

## Inputとglobal state

- player inputは既存のInput Map actionを使い、key code直書きを追加しない。
- Autoloadはproject全体で常時必要なserviceに限定する。scene内責務や便利目的のmanagerを安易にglobal化しない。
- Autoloadからscene内部nodeへの長期参照を増やさない。

## 変更保護

- node名、NodePath、scene inheritance、parent-child構造、external resource UIDを必要なく変更しない。
- scene全体を書き直さず、対象nodeまたはpropertyだけを変更する。
- projectの既存formatとGodotが保存する順序を尊重し、無関係な再serializeを避ける。
