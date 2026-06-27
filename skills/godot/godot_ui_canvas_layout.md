# Godot UI Canvas Layout Rules

## 目的
- UI、HUD、menu、画面構成で、Container や clip による欠け、移動しづらさ、後調整不能を防ぐ
- Godot Editor の Inspector で位置と大きさを調整できる構成を維持する

## 対象
- `CanvasLayer`
- `Control`
- `Panel`
- `TextureRect`
- `Label`
- `Button`
- HUD、menu、dialog、overlay、title、result、pause 画面
- 画面全体の構成、解像度変更、viewport、camera、safe area

## 基本方針
- UI の正本は scene と Inspector 設定とする
- 画面を箱に閉じ込めて作らない
- Full screen の UI root は自由配置の `Control` を基本とする
- HUD や overlay は `CanvasLayer` 配下に置いてよい
- World object は `Node2D`、UI は `CanvasLayer` または `Control` に分ける
- 後から位置調整する要素は、個別 node として直接選択できる構成を優先する

## 禁止
- 画面全体を `Container` 系 node の自動 layout に入れて構成しない
- 画面全体を `PanelContainer`、`MarginContainer`、`ScrollContainer`、`AspectRatioContainer`、`SubViewportContainer` に入れて構成しない
- UI グループ用途だけで `HBoxContainer`、`VBoxContainer`、`GridContainer` を使わない
- 表示欠けの原因になる `clip_contents = true` を既定で使わない
- `SubViewport`、`ViewportTexture`、`CanvasItem.clip_children` を通常 UI の囲い込み目的で使わない
- script から `position`、`size`、`anchor_*`、`offset_*` を固定代入して layout を支配しない
- Camera2D や viewport 設定で UI の表示位置を補正しない

## 例外
- list、log、inventory、settings menu など、自動 layout 自体が目的の場合のみ Container 系 node を使ってよい
- scroll 表示が目的の場合のみ `ScrollContainer` を使ってよい
- 固定比率の preview、render target、mini map など、viewport 表示自体が目的の場合のみ `SubViewportContainer` を使ってよい
- 例外使用時は、使用理由と欠け対策を短く記録する

## 推奨構成
- `Main` : game world root
- `World` : `Node2D`
- `Camera2D` : world camera
- `UI` : `CanvasLayer`
- `UI/Root` : `Control`
- `UI/Root/HUD` : `Control`
- `UI/Root/Menu` : `Control`
- `UI/Root/DialogLayer` : `Control`

## Anchor と Size
- full screen root の `Control` は viewport 全体に合わせる
- 個別 UI 部品は Inspector で調整しやすい anchor と offset を使う
- 自由配置 UI の anchor は必要がなければ `Top Left` を基本とする
- 画面端に固定する HUD は対応する corner または edge anchor を使う
- サイズが変わる text は container で押し込まず、十分な余白と最小 size を scene 側に持たせる

## 確認
- UI、CanvasLayer、Control、viewport、camera、window に関わる変更では非 headless 表示確認を行う
- 画面欠け、重なり、クリック不能、focus 不能、意図しない scroll がないことを確認する
- 最低限、既定 window size で確認する
- 解像度や stretch 設定を変更した場合は、小さめの window と想定最大 window の両方を確認する
- 自動確認ができない場合は、未確認の表示条件を明示する
