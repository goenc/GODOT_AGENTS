# Godot Editor MCP

Godot project本体の調査・実装・Editor操作・実行確認では `godot_editor` を必ず使用する。文書だけの変更、Gitだけの操作、Godot projectがない規則repositoryの作業には適用しない。

## 接続と起動

1. 対象 `project.godot` の絶対pathとengine versionを特定する。公開されているMCP toolを検索し、実際のschemaに従って読み取り専用toolを呼び出す。tool名や引数を推測で作らない。
2. 応答から対象projectを照合する。別projectへ接続していたら変更を送らず、既存の対象選択機能で正しいprojectを選ぶ。選択機能がなければMCP依存操作を保留し、独立した作業を続ける。
3. 接続できない場合は、Codex側のtool公開状態、`godot_editor` の登録設定、MCPサーバーprocess、対象Editorとプラグインの接続状態を分けて確認する。tool未公開をサーバー未起動と決めつけない。設定のsecret値を表示しない。
4. サーバー `godot-mcp` が未起動なら、導入済みコマンド `godot-editor-mcp` を既存登録の引数・transportで起動する。stdio方式はMCP clientが起動・管理する接続手順を使い、単独background processにして接続済みと扱わない。独立常駐方式は既存の起動手順に従い、Windowsのbackground helperは非表示で起動する。processやportを確認し、重複起動しない。
5. 対象Editorが必要なら、既存Editorと `addons/godot_mcp` の `Godot MCP` を確認する。未起動Editorを対象projectと適合するengineで起動する。プラグイン有効化で永続設定が変わる場合は、実装依頼の範囲内で行う。Godot C#は対応する.NET版を使う。別projectを閉じたり、未保存sceneを上書きしたりしない。
6. 準備後にMCPを再接続し、読み取り専用toolで正常応答と対象projectを再確認する。processが存在するだけでは接続成功としない。

操作権限は現在の依頼、会話中の既存許可、有効なAGENTSから判断し、skill自体を新しい許可の根拠にしない。対象taskに必要な導入済みサーバー・Editorの起動、再接続、既存設定に基づくローカル接続は通常の作業準備として扱い、許可済みなら再確認しない。新規導入、更新、再インストール、接続設定の書換え、別projectの停止へ許可を拡張しない。

## 復旧できない場合

- 起動やtool応答には既存設定のtimeoutを使い、未設定なら1回60秒を目安に待つ。正常な起動進捗があればprocessを追加起動せず待機を続ける。
- 同じ失敗に対する再試行は、原因に対応する修正後に最大2回。条件を変えず同じ操作を繰り返さない。新しい証拠が得られた場合は別の復旧手段を試す。
- toolがsessionへ公開されない場合は、利用可能なclientの再読込・再接続機能を使う。利用者によるsession再開が必要なら、その事実と必要操作を記録する。認証や新規導入が必要な場合も同様。
- MCP依存のEditor操作・実測だけ保留する。local sourceの調査、許可済みのfile編集、CLI検証など独立した安全な作業は続ける。MCPの使用義務を黙って省略せず、未接続理由、試した復旧、代替確認、残る未確認を報告する。

## 作業と確認

- 対象taskに役立つscene・node・Inspector・log・実行確認のMCP機能を使う。必要な機能が公開されていなければ、file編集やCLIで補い、MCPで実施した範囲を区別する。
- 説明・調査だけの依頼ではsceneや永続設定を変更しない。接続にプラグイン設定変更が必要ならMCP依存部分だけ保留し、local調査を続ける。起動・importが予期せず追跡fileを変えた場合は差分を報告し、調査依頼だけを根拠にcommit・pushしない。
- Editorでの変更後は、対象だけを保存してdisk上のscene・resource・scriptとGit差分を確認する。toolの成功応答だけで永続化・動作確認済みとしない。既存の未保存編集が混在する場合は一括保存しない。
- 実際に呼び出したtool、対象project、正常応答、関連する検証結果を簡潔に記録する。

## Godot MCPの必須使用と自動起動

導入済みMCPの識別情報は以下とする。バージョンは利用者申告の導入時情報であり、作業時は実環境も確認する。

- Codex側の登録名: `godot_editor`
- MCPサーバー名: `godot-mcp`
- 実行コマンド: `godot-editor-mcp`
- Godotプラグイン名: `Godot MCP`
- プラグインフォルダ: `addons/godot_mcp`
- 導入時バージョン: `2026.09.02`

- Godot project本体の作業では `godot_editor` を必ず使用し、読み取り専用toolの正常応答と対象projectの一致を確認する。tool一覧の存在だけで使用済みとしない。文書だけの変更やGitだけの操作ではEditor起動を要求しない。
- 未起動なら既存設定に従いサーバーを起動する。必要な対象Editor起動、再接続、ローカル接続は許可済みとし、再確認せず実施する。導入済みプラグインの有効化で永続設定が変わる場合は、実装依頼の範囲内で行う。
- 接続確認、起動、復旧、保存確認の詳細は、本書の接続と起動・復旧・作業と確認の節に従う。復旧失敗時はMCP依存操作だけ保留し、安全な独立作業を続ける。未接続を成功と報告しない。

### 共有サーバーを使う端末

- 最初にその端末の `godot_editor` 登録がcommand方式かHTTP URL方式かを確認する。別端末にも同じ常駐設定があるとは推測しない。
- HTTP URL登録の端末では全taskが1つのローカル共有サーバーへ接続する。taskごとにstdioサーバーを追加起動したり、空きportへ自動変更したりしない。停止時は導入済みの二重起動防止付きlauncherで復旧する。
- 共有構成の標準はCodex接続先 `http://127.0.0.1:9090/mcp`、Editor bridge `ws://127.0.0.1:9080`。実際の登録を優先し、両portを同じサーバーprocessが所有することを確認する。外部公開せずloopbackに限定する。
- `godot_get_server_info`、`godot_list_toolsets`、`godot_inspection_get_project_info`で接続とproject絶対pathを確認する。必要なtoolsetは実在する `godot_enable_toolset` で有効にし、tool一覧を再取得する。Codexの許可tool一覧からこの切替toolや必要なruntime/input toolを除外しない。
- 複数taskの接続は共有できるが、Editorの変更・実行・入力操作は同時に行わない。別projectのEditorや他taskのplay sessionを無断で閉じたり操作したりしない。projectが一致しない場合はMCP変更操作を保留する。
- 操作確認はMCP経由の起動、入力の押下・解除、runtimeの座標変化と入力受信、テストで起動したplay sessionの停止で判定する。既存play sessionと、利用者が起動継続を指定したgameは停止しない。
- Codexの接続設定を変更した場合、既存taskのtoolが旧接続を保持することがある。共有HTTPへの直接確認と、このtaskに公開されたtoolからの確認を区別し、後者に再接続・Codex再起動が必要なら明示する。ユーザー作業中のCodexを自動終了しない。
