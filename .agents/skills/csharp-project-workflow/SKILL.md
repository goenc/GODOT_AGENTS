---
name: csharp-project-workflow
description: C#および.NETプロジェクトの構成特定、最小実装、ビルド、テスト、UI、設定、リソース、機密情報を既存方式に合わせて扱う。C#、WPF、Windows Forms、MAUI、ASP.NET Core、Unityのコードやプロジェクトを調査・変更するときに使用する。非C#リポジトリや一般的なGit操作だけには使用しない。
---

# C# Project Workflow

既存構成を守り、必要な参照資料だけを読み込んで C# 変更を実施する。

## 必須の組合せ

- ファイル変更を伴う場合は `repository-change-workflow` も使用する。
- 読み取り専用の調査では、必要な C# 参照資料だけを使用する。

## 参照資料の選択

- 対象 solution、project、framework の特定: `references/project-structure.md`
- `.cs`、テスト、責務分割、ファイル配置の変更: `references/code-and-layout.md`
- build、test、run、XAML compile の確認: `references/build-and-test.md`
- WPF、Windows Forms、MAUI、ASP.NET Core UI、Unity asset の変更: `references/ui.md`
- project file、設定、resource、secret の変更: `references/configuration-and-secrets.md`
- 外部仕様、SDK、NuGet、framework の調査が必要な場合: `references/research.md`

発動条件に一致する参照資料を全文読む。一致しない資料は読まない。

## 実行原則

1. 対象プロジェクトと変更方式を特定する。
2. 対象ファイル、直接依存、関連テストだけを先に確認する。
3. 既存規約に沿う最小変更を行う。
4. 変更影響に合う最小の検証を行う。
5. ファイル変更がある場合は `repository-change-workflow` に従い、コミット、push、リモート照合まで完了する。
