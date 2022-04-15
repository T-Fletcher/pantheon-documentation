---
title: Migrate a Site Created With the Pantheon Dashboard to Drupal 9
subtitle: Troubleshooting
description: 
categories: [develop]
tags: [code, launch, migrate, site, updates]
contributors: [wordsmither]
reviewed: "2021-03-31"
layout: guide
showtoc: true
permalink: docs/guides/drupal-9-hosted-btworkflow/troubleshooting
anchorid: troubleshooting
editpath: drupal-9/drupal-9-hosted-btworkflow/17-troubleshooting.md
---

### Provided Host Name Not Valid

Update your `settings.php` file with a trusted host setting, if you receive the following error message:

```none
 The provided host name is not valid for this server
 ```

Refer to the [Trusted Host Setting](/settings-php#trusted-host-setting) documentation for more information.

### Working With Dependency Versions

<Partial file="composer-updating.md" />