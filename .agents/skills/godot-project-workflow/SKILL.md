---
name: godot-project-workflow
description: Godot projectのGDScript、C#、scene、resource、UI、physics、TileMapLayer、project設定、import、test、buildを調査または変更する。GodotのC#または.NET taskではcsharp-project-workflowも併用する。Gitだけの操作やGodot以外のrepositoryには使用しない。
---

# Godot Project Workflow

Godot Editorで人間が調整できる構成を維持し、要求を満たす最小変更を実装して検証する。

## 開始

1. 対象repositoryとGodot project rootを特定する。
2. 最も近い `AGENTS.md`、`project.godot`、既存の実行・test手順を確認する。
3. projectが使用するGodot major/minor、主言語、main scene、既存folder構成を確定する。
4. 変更対象と直接依存だけを調べ、project全体を無目的に読み込まない。

Godot versionは既存projectの宣言と実測を優先する。別目的のtaskでmajor/minorを更新しない。新規projectでversion指定がない場合だけ、公式release情報を確認してstable版を提案する。

## Reference routing

現在taskに必要なものだけを読む。

- file追加、配置、分割、移動を伴う場合は [project-structure.md](references/project-structure.md)。
- GDScript、C#、scene、resource、signal、input、autoloadを扱う場合は [code-scenes-resources.md](references/code-scenes-resources.md)。
- `Control`、`CanvasLayer`、HUD、menu、window、viewport、camera、画面layoutを扱う場合は [ui.md](references/ui.md)。
- `PhysicsBody2D`、`Area2D`、collision、physics signalを扱う場合は [physics.md](references/physics.md)。
- 静的grid地形、`TileSet`、`TileMapLayer`を扱う場合は [tilemap.md](references/tilemap.md)。
- project本体を変更して検証commandを選ぶ場合は [build-and-test.md](references/build-and-test.md)。
- Godot仕様、外部dependency、addon、環境差、未確認APIの調査が必要な場合は [research.md](references/research.md)。
- GodotのC#、`.csproj`、.NET、NuGet、C# testを扱う場合は `csharp-project-workflow` とその必要なreferenceも併用する。

## 実装原則

- 既存scene、node、resource、script、test、Godot built-in APIを優先して再利用する。
- node名、NodePath、親子構造、外部resource参照、main scene、公開APIは、要求上必要でない限り維持する。
- sceneやInspectorが正本の調整値をscriptの固定代入で上書きしない。
- 新しいdependency、addon、autoload、singleton、base class、utility、framework、MCP、editor plugin、外部serviceは、現在の受入条件に必須の場合だけ提案し、追加前に利用者の許可を得る。
- runtime生成、file編集、Editor操作のどれを使うかは、既存projectの方式と要求から選ぶ。Editorで調整するvisualやcollisionを、理由なくraw node生成へ置き換えない。
- scene全体の再生成や無関係なformat変更を行わない。
- 受入条件と必要な確認を満たしたら停止する。

## 検証

- 変更影響に合う最小のimport、構文確認、test、build、bounded実行確認を行う。
- parseまたはimport成功だけでgameplayやvisualを確認済みと扱わない。
- UI、入力、window、描画、camera、animation、game feelは、可能なら安全な非headless確認も行う。
- 自動確認できない場合は、未確認条件と利用者側で必要な確認を明示する。
- `project.godot` がないagent規則repositoryではGodotを起動せず、skill形式とreference整合だけを確認する。
