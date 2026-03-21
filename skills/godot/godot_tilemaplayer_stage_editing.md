# Godot TileMapLayer Stage Editing

## 目的
- Godot エディタで直接編集できる静的地形構成を固定する

## 対象
- 床、壁、天井などの静的グリッド地形
- TileSet と TileMapLayer を用いるステージ編集

## 規則
- 静的地形は `TileSet + TileMapLayer` で管理する
- 新規実装では `TileMap` ではなく `TileMapLayer` を使用する
- 床、壁、天井、固定地形の正本は scene 上の `TileMapLayer` とする
- `TileSet` に衝突形状を持たせる
- エディタで編集した地形がそのまま実行時地形になる構成を優先する
- 32x32 などのグリッド地形は個別 node 大量配置より `TileMapLayer` を優先する
- 敵、プレイヤー開始位置、移動足場、発射装置、動的ギミックは通常 node または個別 scene で配置する
- JSON は地形正本に使わず、必要時のみ敵設定、ステージ設定、補助 parameter に限定して使う

## 禁止
- 固定数の床 node を並べて座標だけ差し替える方式
- エディタ表示地形と実行時地形の正本分離
- 静的地形を大量の `StaticBody2D` 個別 node で管理する方式
- 地形編集時に JSON と scene の両方を手修正する構成
- 地形編集目的で `TileMapLayer` を避けて個別 node 配置へ戻すこと

## 推奨構成
- `Stage/GroundLayer` : `TileMapLayer`
- `Stage/DecorationLayer` : `TileMapLayer`
- `Stage/HazardLayer` : `TileMapLayer`
- `Stage/PlayerSpawn` : `Marker2D` または `Node2D`
- `Stage/EnemySpawn*` : `Marker2D` または `Node2D`
- 動的オブジェクトは通常 node

## 適用条件
- Godot エディタで地形を直接配置、編集、保存したい場合の第一候補は `TileSet + TileMapLayer` とする

## 移行規則
- 既存方式が固定 node + JSON 再配置で、利用者がエディタ編集を要求した場合は以下を優先する
1. 固定床 node 群を廃止する
2. `TileMapLayer` を追加する
3. `TileSet` に床衝突を持たせる
4. 床再配置処理を削除する
5. 床データの正本を `TileMapLayer` に移す

## 補足
- 動的オブジェクトを `TileMapLayer` に押し込めない
- 無関係なリファクタリングをしない