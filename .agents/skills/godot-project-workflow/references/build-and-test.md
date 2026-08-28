# Build and Test

変更影響に合う最小確認を選ぶ。commandはrepositoryの既存scriptや文書があればそれを優先する。

## Godot command

- まずprojectが定義するcommand、tool、CI設定を確認する。
- 定義がなければPATH上の `godot` を使用する。Windowsでconsole出力が必要で `godot_console` が用意されている場合は使用してよい。
- 毎回の再帰探索、候補path総当たり、machine固有pathのcommitを行わない。
- commandが見つからない場合は推測で別binaryを実行せず、未確認として報告する。

## Version

- `project.godot` のfeature宣言、既存CI、project文書、`godot --version` から対象versionを確定する。
- projectのmajor/minorと異なるeditorで自動importやscene保存を行わない。
- unrelated taskでengine versionやproject formatを更新しない。

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
- GUI操作を自動化する必要はない。CLI、log、test、timeout付き起動で確認できないvisual事項は未確認として利用者へ渡す。

## 判定

- commandのexit codeとerror outputを確認する。
- parseまたはimport成功だけでruntime behavior、visual、inputを確認済みとしない。
- 失敗時は最初の主要原因を切り分け、対象箇所だけを修正して同じ確認を再実行する。
- 同じ原因で修正loopが続く場合は権限や範囲を広げず停止して報告する。
