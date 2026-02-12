# Redmine Subtask List Accordion

チケットの子チケット一覧にアコーディオン機能を追加するプラグインです。

## 概要

Redmine の子チケット一覧は、階層が深くなると全体を把握しにくくなります。
このプラグインは、子チケット一覧を折りたたみ/展開できるアコーディオン形式に変換し、
複雑なチケット構造でも必要な部分だけを表示して作業できるようにします。

## 機能

### アコーディオン表示

- 子チケット一覧の各階層を折りたたみ/展開可能
- 子チケット一覧の上部に「すべて展開」「すべて収縮」リンクを追加
- 右クリックメニューから「このツリーを展開」「このツリーを収縮」「この階層をすべて展開」を選択可能

### プラグイン設定

| 設定項目 | 説明 |
|----------|------|
| サーバー処理モード | 高速だが他プラグインと競合の可能性あり（デフォルト：有効） |
| デフォルトですべて展開 | 初回表示時にすべての子チケットを展開 |
| 収縮させるトラッカー | 指定したトラッカーはデフォルトで折りたたむ（すべて展開が有効時のみ） |

### ユーザー設定

- 「デフォルトでツリーを展開する時の子チケット数上限」を個人設定で変更可能

## 対応バージョン

- Redmine 5.x（5.1.11 にて動作確認済み）
- Redmine 6.x（6.1.1 にて動作確認済み）

## インストール

Redmine のインストール先はお使いの環境によって異なります。
以下の説明では `/var/lib/redmine` を使用しています。
お使いの環境に合わせて変更してください。

| 環境 | Redmine パス |
|------|-------------|
| apt (Debian/Ubuntu) | `/var/lib/redmine` |
| Docker (公式イメージ) | `/usr/src/redmine` |
| Bitnami | `/opt/bitnami/redmine` |

以下を実行し、Redmine を再起動してください。

```
$ cd /var/lib/redmine/plugins
$ git clone https://github.com/RedminePower/redmine_subtask_list_accordion.git
$ bundle exec rake redmine:plugins:migrate RAILS_ENV=production
```

## アンインストール

以下を実行し、Redmine を再起動してください。

```
$ cd /var/lib/redmine
$ bundle exec rake redmine:plugins:migrate NAME=redmine_subtask_list_accordion VERSION=0 RAILS_ENV=production
$ rm -rf plugins/redmine_subtask_list_accordion
```
