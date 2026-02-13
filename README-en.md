# redmine_subtask_list_accordion

> [!Tip]
> With [redmine_studio_plugin](https://github.com/RedminePower/redmine_studio_plugin), you can manage this feature along with other useful features in one place.
>
> Also, combined with the [Redmine Studio](https://www.redmine-power.com/) app, you can enjoy an even better Redmine experience.

## Overview

A plugin that adds accordion functionality to the subtask list on issues.
It converts deep subtask hierarchies into a collapsible format, allowing you to display only the parts you need even with complex issue structures.

<img src="images/subtask_list_accordion_01.png" width="600">

For details, see [here](https://github.com/RedminePower/redmine_studio_plugin/blob/master/docs/subtask_list_accordion-en.md).

## Supported Versions

- Redmine 5.x (tested with 5.1.11)
- Redmine 6.x (tested with 6.1.1)

## Installation

The Redmine installation path varies depending on your environment.
The following instructions use `/var/lib/redmine`.
Please adjust according to your environment.

| Environment | Redmine Path |
|-------------|--------------|
| apt (Debian/Ubuntu) | `/var/lib/redmine` |
| Docker (Official Image) | `/usr/src/redmine` |
| Bitnami | `/opt/bitnami/redmine` |

Run the following commands and restart Redmine.

```bash
cd /var/lib/redmine/plugins
git clone https://github.com/RedminePower/redmine_subtask_list_accordion.git
cd /var/lib/redmine
bundle exec rake redmine:plugins:migrate RAILS_ENV=production
```

## Uninstallation

Run the following commands and restart Redmine.

```bash
cd /var/lib/redmine
bundle exec rake redmine:plugins:migrate NAME=redmine_subtask_list_accordion VERSION=0 RAILS_ENV=production
rm -rf plugins/redmine_subtask_list_accordion
```
