# Initial Structure Compliance

## 配置原則
- 新規 file は既存役割別 folder に配置する
- root 直下へ無秩序に追加しない

## 基本構成
- scene: `scenes/` `scenes/player/` `scenes/ui/`
- script: `scripts/game/` `scripts/player/` `scripts/ui/`
- asset: `assets/sprites/` `assets/sounds/` `assets/fonts/`
- config: `data/config/`
- tool: `tools/`
- runtime: `runtime/`

## 配置規則
- main scene は `scenes/`
- player 関連 scene は `scenes/player/`
- UI 関連 scene は `scenes/ui/`
- game 制御 script は `scripts/game/`
- player 関連 script は `scripts/player/`
- UI 関連 script は `scripts/ui/`
- image は `assets/sprites/`
- sound は `assets/sounds/`
- font は `assets/fonts/`
- config data は `data/config/`
- 開発補助は `tools/`
- 実行時出力は `runtime/`

## 禁止
- root 直下に新規 `.gd` `.tscn` を作成しない
- 配置先未確定のまま file を作成しない
- 役割混在配置をしない
- 明示指示なしに既存 file の大規模移動、大規模改名、初期構成 file 上書きをしない

## 作業前確認
- 追加 file 名
- 保存先
- 既存追記か新規作成か

## 結果報告
- 追加・変更 file は path 付きで列挙する