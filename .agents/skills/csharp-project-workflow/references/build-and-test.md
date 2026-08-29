# ビルドとテスト

## コマンド選択

- 既存の `build.ps1`、`test.ps1`、`run.ps1`、Makefile があり、対象作業に適合する場合はそれを優先する。
- 既存 script がない場合は `dotnet build`、`dotnet test`、`dotnet run` を使用する。
- solution があっても、影響が一 project に限定されるなら対象 project を指定する。
- restore、build、test を理由なく分割・重複せず、依存復元を含む最小コマンドから始める。

## 検証範囲

- source、XAML、project、共有設定の変更は、compile と参照解決を確認する。
- logic または test の変更は、関連 test project を実行する。
- 全 solution 確認は、共有設定、複数 project 影響、局所確認失敗の場合に限定する。
- UI application は build 成功を基本確認とし、GUI 操作が必要な事項は未確認とする。
- console、service は副作用がなく必要な場合だけ短時間実行する。

## 失敗分類

- restore 失敗: NuGet source、network、package version、認証を分ける。
- compile 失敗: C# source と生成 source を分ける。
- XAML 失敗: XAML、code-behind、binding、resource 参照を分ける。
- test 失敗: 今回差分による回帰と既存失敗を分ける。
- runtime 失敗: 外部 service、database、device、OS 権限を分ける。

## 画面確認

- Visual Studio を含む Windows GUI のクリック、入力、ドラッグ、メニュー操作、画面遷移を行わない。
- command、URI、route、test entry point から対象画面を直接呼び出せる場合だけ、操作せずに読み取りまたはスクリーンショットを取得する。
- 直接呼び出せない場合は、必要な画面と状態を明示して利用者へ確認を依頼する。
- 画面取得失敗を反復せず、ログ、ソース、テストによる代替確認を一度行う。

## 記録

- 実行コマンド、成功・失敗、主要原因、未確認事項を結果へ記録する。
- 新しい差分がない限り、成功済みコマンドを再実行しない。
