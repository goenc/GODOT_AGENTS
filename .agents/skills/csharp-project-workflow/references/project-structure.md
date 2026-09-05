# プロジェクト構成

## 対象特定

- リポジトリルートを基準に、指定された `*.sln`、`*.slnx`、`*.csproj` を優先確認する。
- 指定が不足または不正な場合だけ、ルートから段階的に探索する。
- multi-project solution では、application、library、test の役割を区別して対象を絞る。
- target framework、nullable、implicit usings、reference、package 管理方式は変更に関係する項目だけ確認する。

## 種別判定

- WPF: `.xaml`、`App.xaml`、`UseWPF`
- Windows Forms: `.Designer.cs`、`ApplicationConfiguration.Initialize`、`UseWindowsForms`
- MAUI: `UseMaui`、`Platforms/`、`MauiProgram.cs`、`AppShell.xaml`
- ASP.NET Core: `Program.cs`、`appsettings.json`、middleware、Controllers、Pages、Razor
- Unity: `Assets/`、`ProjectSettings/`、`.unity`、`.prefab`、`.asmdef`
- Godot C#: `project.godot`、`Godot.NET.Sdk` を使う `.csproj`、`.tscn`。Godot workflowと併用する。
- その他: Console、Class Library、Worker Service、test project を project file と entry point で判定する。

## 制約

- 構成把握前に新規 project、folder、package、namespace 移動を決め打ちしない。
- 既存構成に収まる変更を優先する。
- solution と project file を無関係に再整形しない。
