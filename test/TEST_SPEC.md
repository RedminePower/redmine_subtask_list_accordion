# Subtask List Accordion テスト仕様書

## 概要

Subtask List Accordion プラグインのテスト仕様。チケットのサブタスク一覧にアコーディオン機能を追加する。

## 環境パラメータ

パスから自動判定:
- `redmine_5.1.11` → コンテナ名: `redmine_5.1.11`, ポート: `3051`
- `redmine_6.1.1` → コンテナ名: `redmine_6.1.1`, ポート: `3061`

## 機能の内部実装

| 項目 | 値 |
|------|-----|
| プラグインID | `:redmine_subtask_list_accordion` |
| フッククラス | `RedmineSubtaskListAccordion::Hooks::SubtaskListAccordionHook` |
| パッチ | `IssuesHelperPatch`, `UserPreferencePatch` |
| 設定 | `enable_server_scripting_mode`, `expand_all`, `collapsed_tracker_ids` |
| View パーシャル | `issues/_subtask_list_accordion_partial.html.erb` |
| コンテキストメニュー | `context_menus/_accordion_menu.html.erb` |
| 個人設定パーシャル | `my/_subtask_list_accordion_preferences.erb` |

---

## 1. Runner テスト

### [1-1] プラグイン登録確認

**確認方法:**
```ruby
plugin = Redmine::Plugin.find(:redmine_subtask_list_accordion)
puts plugin.name
```

**期待結果:** `Redmine Subtask List Accordion plugin` が出力される

### [1-2] フッククラス定義確認

**確認方法:**
```ruby
puts defined?(RedmineSubtaskListAccordion::Hooks::SubtaskListAccordionHook)
```

**期待結果:** `constant` が出力される

### [1-3] IssuesHelper パッチ適用確認

**確認方法:**
```ruby
# パッチは IssuesController に helper として追加されている
puts IssuesController._helpers.included_modules.include?(RedmineSubtaskListAccordion::Patches::IssuesHelperPatch)
```

**期待結果:** `true` が出力される

### [1-4] UserPreference パッチ適用確認

**確認方法:**
```ruby
puts UserPreference.included_modules.include?(RedmineSubtaskListAccordion::Patches::UserPreferencePatch)
```

**期待結果:** `true` が出力される

### [1-5] フック登録確認（チケット画面）

**確認方法:**
```ruby
hooks = Redmine::Hook.hook_listeners(:view_issues_show_description_bottom)
puts hooks.any? { |h| h.is_a?(RedmineSubtaskListAccordion::Hooks::SubtaskListAccordionHook) }
```

**期待結果:** `true` が出力される

### [1-6] フック登録確認（個人設定）

**確認方法:**
```ruby
hooks = Redmine::Hook.hook_listeners(:view_my_account_preferences)
puts hooks.any? { |h| h.is_a?(RedmineSubtaskListAccordion::Hooks::SubtaskListAccordionHook) }
```

**期待結果:** `true` が出力される

### [1-7] View ファイル存在確認

**確認方法:**
```ruby
files = [
  'app/views/issues/_subtask_list_accordion_partial.html.erb',
  'app/views/context_menus/_accordion_menu.html.erb',
  'app/views/my/_subtask_list_accordion_preferences.erb',
  'app/views/settings/_subtask_list_accordion_settings.html.erb'
]
files.each do |f|
  path = Rails.root.join('plugins', 'redmine_subtask_list_accordion', f)
  puts "#{f}: #{File.exist?(path)}"
end
```

**期待結果:** すべて `true`

### [1-8] JavaScript ファイル存在確認

**確認方法:**
```ruby
files = [
  'assets/javascripts/subtask_list_accordion.js',
  'assets/javascripts/subtask_list_accordion_client.js'
]
files.each do |f|
  path = Rails.root.join('plugins', 'redmine_subtask_list_accordion', f)
  puts "#{f}: #{File.exist?(path)}"
end
```

**期待結果:** すべて `true`

### [1-9] プラグイン設定のデフォルト値確認

**確認方法:**
```ruby
plugin = Redmine::Plugin.find(:redmine_subtask_list_accordion)
defaults = plugin.settings[:default]
puts "enable_server_scripting_mode: #{defaults['enable_server_scripting_mode']}"
puts "expand_all: #{defaults['expand_all']}"
puts "collapsed_tracker_ids: #{defaults['collapsed_tracker_ids'].inspect}"
```

**期待結果:**
- `enable_server_scripting_mode: true`
- `expand_all: false`
- `collapsed_tracker_ids: []`

---

## 2. HTTP テスト

### [2-1] チケット画面にアコーディオン用クラスが出力される

**前提条件:**
- 子チケットを持つ親チケットが存在すること

**確認方法:**
```powershell
$response = Invoke-WebRequest -Uri "http://localhost:3051/issues/{親チケットID}" -UseBasicParsing
$response.Content -match "subtask_list_accordion"
```

**期待結果:** `True`

### [2-2] サーバーモード時に JavaScript が読み込まれる

**確認方法:**
```powershell
$response = Invoke-WebRequest -Uri "http://localhost:3051/issues/{親チケットID}" -UseBasicParsing
$response.Content -match "subtask_list_accordion\.js"
```

**期待結果:** `True`（サーバーモード有効時）

### [2-3] コンテキストメニューにアコーディオン項目が含まれる

**確認方法:**
```powershell
$session = # ログインセッション取得
# Note: back_url パラメータで /issues/{ID} パターンを渡す必要がある（チケット画面判定に使用）
$response = Invoke-WebRequest -Uri "http://localhost:3051/issues/context_menu?ids[]={ID}&back_url=/issues/{ID}" -WebSession $session -UseBasicParsing
$response.Content -match "selectedTreeOpen"
```

**期待結果:** `True`

### [2-4] 管理画面でプラグイン設定が表示される

**確認方法:**
```powershell
$session = # admin でログインセッション取得
$response = Invoke-WebRequest -Uri "http://localhost:3051/settings/plugin/redmine_subtask_list_accordion" -WebSession $session -UseBasicParsing
$response.StatusCode
```

**期待結果:** `200`

### [2-5] プラグイン設定画面のフォーム要素確認

**確認方法:**
```powershell
$session = # admin でログインセッション取得
$response = Invoke-WebRequest -Uri "http://localhost:3051/settings/plugin/redmine_subtask_list_accordion" -WebSession $session -UseBasicParsing
$html = $response.Content
# サーバースクリプトモード設定
$html -match "enable_server_scripting_mode"
# デフォルト展開設定
$html -match "expand_all"
# 収縮させるトラッカー設定（複数選択リスト）
$html -match "collapsed_tracker_ids"
```

**期待結果:** すべて `True`

### [2-6] 個人設定画面のフォーム要素確認

**確認方法:**
```powershell
$session = # admin でログインセッション取得
$response = Invoke-WebRequest -Uri "http://localhost:3051/my/account" -WebSession $session -UseBasicParsing
$response.Content -match "subtasks_default_expand_limit_upper"
```

**期待結果:** `True`

### [2-7] 収縮させるトラッカー設定の動作確認

**前提条件:**
- `expand_all` 設定が `true` であること
- `collapsed_tracker_ids` に特定のトラッカーIDが設定されていること
- そのトラッカーの子チケットを持つ親チケットが存在すること

**確認方法:**
```ruby
# 設定を変更
settings = Setting.plugin_redmine_subtask_list_accordion
settings['expand_all'] = true
settings['collapsed_tracker_ids'] = [Tracker.first.id.to_s]
Setting.plugin_redmine_subtask_list_accordion = settings

# 対象トラッカーのチケットID
target_tracker_id = Tracker.first.id
```

```powershell
# 親チケットを表示し、対象トラッカーの子チケットに collapse クラスが付与されていることを確認
$response = Invoke-WebRequest -Uri "http://localhost:3051/issues/{親チケットID}" -UseBasicParsing
# 対象トラッカーの子チケット行に "collapse" クラスがあることを確認
$response.Content -match "issue-{子チケットID}.*collapse"
```

**期待結果:**
- 対象トラッカーの子チケットは `collapse` クラスを持つ
- 対象外のトラッカーの子チケットは `expand` クラスを持つ

**後処理:**
```ruby
# 設定を元に戻す
settings = Setting.plugin_redmine_subtask_list_accordion
settings['expand_all'] = false
settings['collapsed_tracker_ids'] = []
Setting.plugin_redmine_subtask_list_accordion = settings
```

### [2-8] デフォルト展開設定の動作確認

**テストケース A: expand_all=true の場合**

**前提条件:**
- `expand_all` 設定が `true` であること
- `collapsed_tracker_ids` が空であること

**確認方法:**
```ruby
# 設定を変更
settings = Setting.plugin_redmine_subtask_list_accordion
settings['expand_all'] = true
settings['collapsed_tracker_ids'] = []
Setting.plugin_redmine_subtask_list_accordion = settings
```

```powershell
$response = Invoke-WebRequest -Uri "http://localhost:3051/issues/{親チケットID}" -UseBasicParsing
# すべての子チケットが expand クラスを持つことを確認
$response.Content -match "issue-{子チケットID}.*expand"
```

**期待結果:** すべての子チケットが `expand` クラスを持つ

---

**テストケース B: expand_all=false の場合（子チケット数が上限以下）**

**前提条件:**
- `expand_all` 設定が `false` であること
- ユーザーの `subtasks_default_expand_limit_upper` が子チケット数以上であること

**確認方法:**
```ruby
# 設定を変更
settings = Setting.plugin_redmine_subtask_list_accordion
settings['expand_all'] = false
Setting.plugin_redmine_subtask_list_accordion = settings

# ユーザー設定を変更（上限を大きく設定）
User.current.pref.subtasks_default_expand_limit_upper = 100
User.current.pref.save
```

```powershell
$response = Invoke-WebRequest -Uri "http://localhost:3051/issues/{親チケットID}" -WebSession $session -UseBasicParsing
# 子チケットが expand クラスを持つことを確認
$response.Content -match "issue-{子チケットID}.*expand"
```

**期待結果:** 子チケットが `expand` クラスを持つ

---

**テストケース C: expand_all=false の場合（子チケット数が上限超過）**

**前提条件:**
- `expand_all` 設定が `false` であること
- ユーザーの `subtasks_default_expand_limit_upper` が子チケット数未満であること

**確認方法:**
```ruby
# ユーザー設定を変更（上限を小さく設定）
User.current.pref.subtasks_default_expand_limit_upper = 1
User.current.pref.save
```

```powershell
$response = Invoke-WebRequest -Uri "http://localhost:3051/issues/{親チケットID}" -WebSession $session -UseBasicParsing
# 子チケットが collapse クラスを持つことを確認
$response.Content -match "issue-{子チケットID}.*collapse"
```

**期待結果:** 子チケットが `collapse` クラスを持つ

**後処理:**
```ruby
# 設定を元に戻す
settings = Setting.plugin_redmine_subtask_list_accordion
settings['expand_all'] = false
Setting.plugin_redmine_subtask_list_accordion = settings

User.current.pref.subtasks_default_expand_limit_upper = 10  # デフォルト値
User.current.pref.save
```

### [2-9] サーバーモード無効時の動作確認

**前提条件:**
- `enable_server_scripting_mode` 設定が `false` であること

**確認方法:**
```ruby
# 設定を変更
settings = Setting.plugin_redmine_subtask_list_accordion
settings['enable_server_scripting_mode'] = false
Setting.plugin_redmine_subtask_list_accordion = settings
```

```powershell
$response = Invoke-WebRequest -Uri "http://localhost:3051/issues/{親チケットID}" -UseBasicParsing
# クライアントモードの JS が読み込まれることを確認
$response.Content -match "subtask_list_accordion_client\.js"
```

**期待結果:**
- `subtask_list_accordion_client.js` が読み込まれる
- `subtask_list_accordion.js`（サーバーモード用）は読み込まれない

**後処理:**
```ruby
# 設定を元に戻す
settings = Setting.plugin_redmine_subtask_list_accordion
settings['enable_server_scripting_mode'] = true
Setting.plugin_redmine_subtask_list_accordion = settings
```

---

## 3. ブラウザテスト

### セットアップデータ

**チケット構造:**

| チケット | 親チケット | subject |
|---------|-----------|---------|
| 親1 | - | 親チケット1 |
| 子1-1 | 親1 | 子チケット1-1 |
| 子1-2 | 親1 | 子チケット1-2 |
| 孫1-1-1 | 子1-1 | 孫チケット1-1-1 |
| 親2 | - | 親チケット2 |
| 子2-1 | 親2 | 子チケット2-1 |

### [3-1] アコーディオン展開/折りたたみ操作

**手順:**
1. admin でログイン
2. 親チケット1を開く
3. 子チケット1-1 の左にある矢印アイコンをクリック

**確認1:** 孫チケット1-1-1 が非表示になる

4. 再度矢印アイコンをクリック

**確認2:** 孫チケット1-1-1 が表示される

5. 「すべて展開」リンクをクリック

**確認3:** すべてのサブタスク（子1-1, 子1-2, 孫1-1-1）が表示される

6. 「すべて折りたたむ」リンクをクリック

**確認4:** 子チケット以下がすべて非表示になる

### [3-2] コンテキストメニューからの展開/折りたたみ

**手順:**
1. admin でログイン
2. 親チケット1を開く
3. サブタスク一覧で子チケット1-1 のチェックボックスにチェックを入れる
4. 子チケット1-1 を右クリック
5. 「この階層をすべて展開」をクリック

**確認1:** 子チケット1-1 配下のすべてのチケット（孫1-1-1）が展開される

6. 再度子チケット1-1 を右クリック
7. 「折りたたむ」をクリック

**確認2:** 子チケット1-1 配下のチケット（孫1-1-1）が非表示になる
