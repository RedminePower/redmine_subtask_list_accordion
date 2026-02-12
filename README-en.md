# Redmine Subtask List Accordion

A plugin that adds accordion functionality to the subtask list on issues.

## Overview

Redmine's subtask list becomes difficult to navigate when the hierarchy is deep.
This plugin converts the subtask list into an accordion format with collapse/expand functionality,
allowing you to display only the parts you need even with complex issue structures.

## Features

### Accordion Display

- Collapse/expand each level of the subtask list
- Adds "Expand All" and "Collapse All" links at the top of the subtask list
- Context menu options: "Expand this tree", "Collapse this tree", "Expand next level all"

### Plugin Settings

| Setting | Description |
|---------|-------------|
| Server processing mode | Faster but may conflict with other plugins (default: enabled) |
| Expand all by default | Expand all subtasks on initial display |
| Collapsed trackers | Specified trackers are collapsed by default (only when "Expand all" is enabled) |

### User Settings

- "Maximum number of subtasks for default tree expansion" can be configured in personal settings

## Supported Versions

- Redmine 5.x (tested on 5.1.11)
- Redmine 6.x (tested on 6.1.1)

## Installation

The Redmine installation path varies depending on your environment.
The instructions below use `/var/lib/redmine`.
Please adjust according to your environment.

| Environment | Redmine Path |
|-------------|--------------|
| apt (Debian/Ubuntu) | `/var/lib/redmine` |
| Docker (official image) | `/usr/src/redmine` |
| Bitnami | `/opt/bitnami/redmine` |

Run the following commands and restart Redmine.

```
$ cd /var/lib/redmine/plugins
$ git clone https://github.com/RedminePower/redmine_subtask_list_accordion.git
$ bundle exec rake redmine:plugins:migrate RAILS_ENV=production
```

## Uninstallation

Run the following commands and restart Redmine.

```
$ cd /var/lib/redmine
$ bundle exec rake redmine:plugins:migrate NAME=redmine_subtask_list_accordion VERSION=0 RAILS_ENV=production
$ rm -rf plugins/redmine_subtask_list_accordion
```
