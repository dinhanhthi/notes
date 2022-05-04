---
layout: post
title: "Google Cloud CLI"
tags: [API & Services]
toc: true
notfull: true
icon: google.svg
keywords: "cli command line interface gcloud google cloud cli apis terminal"
---

{% assign img-url = '/img/post/api' %}

:point_right: [Official doc to install and setting up](https://cloud.google.com/sdk/gcloud).

This note contains only the quick shortcodes.

Using another google account ([official doc](https://cloud.google.com/sdk/docs/initializing)),

```bash
gcloud auth login
```

Or using a service account,

```bash
gcloud auth activate-service-account
```

Set another project,

```bash
gcloud config set project <project-id>
```

