# 設定、リソース、機密情報

## 設定

- `appsettings*.json`、`launchSettings.json`、`.config`、`.runsettings`、`.editorconfig`、`Directory.Build.*`、`Directory.Packages.props`、`NuGet.config` は既存の読込・管理方式に合わせる。
- local-only 設定を repository に固定しない。
- target framework、nullable、implicit usings、LangVersion、OutputType、UseWPF、UseWindowsForms、UseMaui は必要性が明確な場合だけ変更する。
- package version の管理方式を混在させない。中央管理時は `.csproj` に version を直書きしない。
- `global.json` は SDK 固定が必要な場合だけ変更する。

## 機密情報

- secret、token、password、connection string、certificate、private key、personal access token を生成、上書き、表示、複製、コミットしない。
- sample 値は明確な placeholder だけを使う。
- 実値らしき情報を検出した場合は内容を出力せず、対象ファイルと必要な対応だけを報告する。
- 削除や rotation が必要でも、依頼範囲と権限がなければ実行しない。

## リソース

- `.resx`、`Resources/`、`Assets/`、`wwwroot/`、ResourceDictionary は既存配置と build action に合わせる。
- rename より追加・差替えを優先し、参照切れを防ぐ。
- 未使用 resource は依頼なしに削除しない。
- 追加後は compile と resource resolve を確認する。
