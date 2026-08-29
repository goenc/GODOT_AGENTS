# UI

## 共通

- 既存 UI framework、navigation、state 管理、binding、theme、resource 方針を維持する。
- 小さな変更のために画面全体を再構成しない。
- 表示文言、色、寸法、style、accessibility を既存方針に合わせる。
- UI 調整に便乗した命名変更やファイル移動を行わない。

## WPF

- XAML、code-behind、ViewModel の既存分担を維持する。
- binding path、DataContext、Command、INotifyPropertyChanged の整合を確認する。
- ResourceDictionary、Style、Template、Theme の既存構成を維持する。
- 既存方針に反して logic を code-behind へ移さない。

## Windows Forms

- `.Designer.cs` を手作業で大きく変更しない。
- Designer 管理領域と手書き code の境界を維持する。
- control 名、event handler、resource 参照を保つ。

## MAUI

- XAML、code-behind、ViewModel、Shell navigation の既存構成を維持する。
- `Platforms/` の変更では対象 platform を明示する。
- multi-target と resource pipeline を壊さない。

## ASP.NET Core、Blazor、Razor

- MVC、Razor Pages、Minimal API、Blazor の既存方式を切り替えない。
- routing、middleware、DI、authorization、model binding の影響を確認する。
- view と component の変更では model、handler、controller、service の参照を確認する。

## Unity

- scene、prefab、serialized field、asset 参照を壊さない。
- MonoBehaviour lifecycle と Inspector 前提の値を維持する。
- prefab と scene を依頼なしに大規模再生成しない。

## 確認

- UI 変更後は build、XAML compile、resource resolve、binding 参照を確認する。
- GUI 操作が必要な表示確認は未確認として明示する。
