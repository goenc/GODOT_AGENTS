# コードと配置

## C# コード

- 既存の code style、namespace 形式、nullable、implicit usings、型表記、async 設計を維持する。
- 変更前に対象 class、method、責務、呼出し元を確認する。
- signature、interface、base class の変更前に全呼出し元への影響を確認する。
- DTO、model、entity の property 変更では serialization、binding、database、API への影響を確認する。
- exception、logging、CancellationToken、IDisposable、購読解除は既存方式を維持する。
- public API の破壊的変更、大規模抽象化、DI 導入、layer 再編を依頼なしに行わない。
- 無関係な rename、format、warning 一括解消、動作変更を伴う最適化を行わない。

## テスト

- 既存 test framework、命名、assertion style を維持する。
- bug fix では変更対象に近い最小の回帰テストを追加する。
- テスト追加が本体変更より大きくなる場合は、理由を示して作業範囲を分ける。

## 新規ファイル

- 既存の役割別 folder と命名を優先する。既存構成にない `src/` や `tests/` を機械的に新設しない。
- UI は既存の `Views/`、`ViewModels/`、`Pages/`、`Components/`、`Forms/` に合わせる。
- domain と logic は既存の `Models/`、`Services/`、`Domain/`、`Core/` に合わせる。
- configuration、resource、tool、runtime は既存配置に合わせる。
- root 直下へ `.cs`、`.xaml`、`.resx` を無秩序に追加しない。
- 追加前にファイル名、保存先、既存追記ではなく新設する理由を確定する。

## 分割

- 見落とし、競合、複数責務、重複、分岐漏れの危険が具体化した場合だけ分割する。
- 行数は目安とし、file 400～800 行、method 80～150 行、主要分岐 5～10 件超で分割を検討する。
- 一つの file を一つの概念へ寄せ、表示、入力、状態更新、外部副作用を分離する。
- 既存動作を変えずに移動し、参照修正と検証を先に完了する。整形や追加最適化は同時に行わない。
- 分割に便乗した設計変更、命名一括変更、依存追加を行わない。
