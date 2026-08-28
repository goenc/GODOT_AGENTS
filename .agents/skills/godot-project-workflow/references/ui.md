# UI and Canvas Layout

## 基本方針

- UIの初期layoutはsceneとInspector設定を正本にする。
- world objectは `Node2D`、screen UIは `CanvasLayer` または `Control` を基本に、既存project構成を維持する。
- 自由配置HUDやoverlayは、後から個別nodeを選択して調整できる構成を保つ。
- scriptから `position`、`size`、`anchor_*`、`offset_*` を固定代入しない。実行時に動的変更する要求がある場合だけ例外とする。

## Containerとviewport

- Containerを単なるgroup化目的で追加しない。
- list、log、inventory、settings menuなどauto layout自体が目的なら、適切なContainerを使用してよい。
- `ScrollContainer` はscrollが必要な場合、`SubViewportContainer` はrender targetやpreviewが必要な場合だけ使う。
- `clip_contents`、`clip_children`、SubViewport、Camera2Dを、通常UIの欠けや位置ずれを隠す目的で追加しない。

## Anchor

- full-screen rootはviewportに追従させる。
- 自由配置要素は既存方針を優先し、基準がなければ `Top Left` を候補にする。
- 画面端へ固定するHUDは対応するedgeまたはcorner anchorを使う。
- text可変長と最小sizeを考慮し、Containerを避けるためだけの固定sizeにしない。

## 確認

- 既定window sizeで欠け、重なり、click、focus、scrollを確認する。
- stretch、resolution、safe areaを変更した場合は、変更影響に合う複数sizeで確認する。
- visual確認は自動終了またはtimeout付きで行う。安全な自動確認手段がなければ未確認条件を報告する。
