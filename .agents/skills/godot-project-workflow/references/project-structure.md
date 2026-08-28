# Project Structure

新規fileや分割は、固定templateではなく対象projectの既存構成に合わせる。

## 配置

- 最初に `rg --files`、`project.godot`、近接する同種fileから既存の役割別folderと命名規則を確認する。
- 新規scene、script、resource、asset、tool、testは、同じ役割の既存fileと同じ領域へ置く。
- 既存の配置規則がない新規projectだけ、`scenes/`、`scripts/`、`assets/`、`tests/`、`tools/` の最小構成を候補にする。
- root直下へ `.gd` や `.tscn` を無秩序に追加しない。
- 利用者の明示がない限り、既存fileの大規模移動、rename、folder再編を行わない。

## 分割

- fileまたは関数の大きさだけで分割しない。責務混在、修正漏れ、衝突、test困難が現在taskの具体的な問題になっている場合だけ分割する。
- 分割ではbehaviorを変えず、移動と参照修正を先に完了して確認する。
- 分割に便乗した命名変更、format変更、最適化、dependency追加を行わない。
- 公開interfaceと依存方向を増やさない。表示、入力、状態、外部副作用の分離は、実際に保守性を改善する範囲に限定する。

## Asset

- third-party assetを追加する場合はsource、version、license、attribution条件を確認する。
- assetを無断でdownloadしない。生成物や一時fileをsource assetと混同しない。

## 確認

- 追加・移動した全pathと参照元を確認する。
- `.tscn`、`.tres`、`project.godot` の外部参照とresource pathが切れていないことを確認する。
