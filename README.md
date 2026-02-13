# redmine_subtask_list_accordion

> [!Tip]
> [redmine_studio_plugin](https://github.com/RedminePower/redmine_studio_plugin) をご利用いただければ、この機能を含む複数の便利な機能をまとめて管理できます。
>
> また、[Redmine Studio](https://www.redmine-power.com/) アプリと組み合わせることで、より快適に Redmine をお使いいただけます。

## 概要

チケットの子チケット一覧にアコーディオン機能を追加するプラグインです。
階層が深い子チケット一覧を折りたたみ/展開できる形式に変換し、複雑なチケット構造でも必要な部分だけを表示して作業できます。

<img src="images/subtask_list_accordion_01.png" width="600">

詳細は [こちら](https://github.com/RedminePower/redmine_studio_plugin/blob/master/docs/subtask_list_accordion.md) をご覧ください。

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

```bash
cd /var/lib/redmine/plugins
git clone https://github.com/RedminePower/redmine_subtask_list_accordion.git
cd /var/lib/redmine
bundle exec rake redmine:plugins:migrate RAILS_ENV=production
```

## アンインストール

以下を実行し、Redmine を再起動してください。

```bash
cd /var/lib/redmine
bundle exec rake redmine:plugins:migrate NAME=redmine_subtask_list_accordion VERSION=0 RAILS_ENV=production
rm -rf plugins/redmine_subtask_list_accordion
```
