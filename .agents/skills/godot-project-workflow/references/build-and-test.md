# Build and Test

変更影響に合う最小確認を選ぶ。commandはrepositoryの既存scriptや文書があればそれを優先する。

## Godot command

- まずprojectが定義するcommand、tool、CI設定を確認する。
- 定義がなければPATH上の `godot` を確認する。見つからなければMCPの接続情報や対象Editor processから実行binaryを特定する。Windowsでconsole出力が必要で `godot_console` が用意されている場合は使用してよい。
- 毎回の再帰探索、候補path総当たり、machine固有pathのcommitを行わない。
- commandが見つからない場合は推測で別binaryを実行せず、未確認として報告する。

## Version

- `project.godot` のfeature宣言、既存CI、project文書、`godot --version` から対象versionを確定する。
- projectのmajor/minorと異なるeditorで自動importやscene保存を行わない。
- Godot C#では対応する.NET版Editorとproject指定の.NET SDKを使い、C# buildも確認する。`dotnet run` をGodotの実行確認の代用にしない。
- unrelated taskでengine versionやproject formatを更新しない。

## import・build cache

- 同じprojectと適合するengineで生成した既存import cacheを再利用する。Godot 4では `.godot/imported/` が対象。別majorのcache配置や互換性は推測しない。
- source asset・import設定・engine条件が変わった場合は必要な再importを行い、現在のresourceが読めることを確認する。cacheの存在だけで検証成功と扱わない。
- 通常作業で `.godot` を削除したり全assetを強制再importしたりしない。破損や不整合の証拠がある場合だけ原因に関係する対象へ復旧を限定する。
- asset横の `.import` 設定や、対象versionで管理される `.uid` はcache削除の対象と混同しない。既存の追跡方針とresource参照を維持する。`.godot` の生成cacheを新たにcommitしない。
- Godot C#では既存NuGet cacheと増分buildを使い、source・設定・依存変更に必要なbuildとtestを実行する。無条件のclean、依存再取得、旧binaryだけでの確認を行わない。
- 成功済み確認は同じ入力条件なら再利用する。追加変更や新しい失敗があれば影響する確認だけ再実行する。

## 確認候補

resource importが必要な場合:

```text
godot --headless --path . --import
```

単独GDScriptのparser確認が有効な場合:

```text
godot --headless --path . --script res://PATH/TO/SCRIPT.gd --check-only
```

- repository固有test runnerがある場合は、変更に関係する最小suiteを実行する。
- exportはtaskがbuild、配布物、export設定を対象にする場合だけ行う。既存presetとexport templateを使用する。
- UI、入力、window、描画、camera、animation、game feelは、headless確認に加えてboundedな非headless smoke testを行う。
- MCPで可能な画面取得、入力、scene実行を優先して対象の表示・操作を確認する。必要なら利用可能なGUI操作手段で補う。確認できないvisual事項だけを理由付きで未確認とする。

## 判定

- commandのexit codeとerror outputを確認する。
- parseまたはimport成功だけでruntime behavior、visual、inputを確認済みとしない。
- 失敗時は最初の主要原因を切り分け、対象箇所だけを修正して同じ確認を再実行する。
- 同じ原因で修正が続く場合は新しい証拠や別の確認方法へ切り替える。解消に利用者判断や範囲外の変更が必要なら該当確認だけ保留し、独立した作業を続ける。
