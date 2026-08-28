# TileMapLayer Stage Editing

## 適用

Godot 4系で、床、壁、天井、固定hazardなど静的grid地形をEditorから配置、編集、保存するtaskに適用する。

## 規則

- 新規の静的grid地形は `TileSet` と `TileMapLayer` を第一候補にする。
- 地形とcollisionの正本はscene上の `TileMapLayer` と `TileSet` に置く。
- decoration、ground、hazardは、編集上の利点がある場合にlayerを分ける。
- player start、enemy spawn、moving platform、launcher、動的gimmickは通常nodeまたは個別sceneで扱う。
- JSONやCSVを地形の二重正本にしない。外部dataが必要なら、役割をstage parameter等へ限定する。

## 既存project

- 現在taskが地形編集方式の変更を要求しない限り、既存方式を全面移行しない。
- 固定node群からTileMapLayerへ移行する場合は、scene上の見た目、collision、spawn、参照先を順に移し、旧runtime再配置処理を残して二重管理にしない。
- 動的objectをTileMapLayerへ押し込めない。

## 確認

- import後にTileSet参照、cell source、terrain、collision layerが解決することを確認する。
- 地形変更では対象sceneを実行し、見た目とcollisionの両方を確認する。
