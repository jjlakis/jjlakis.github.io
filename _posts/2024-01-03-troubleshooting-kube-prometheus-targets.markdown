---
layout: default
title: "Troubleshooting kube-prometheus targets"
date: 2024-01-03 13:31:03 +0200
categories: post
tags: kubernetes prometheus
---

To see scrape_configs that kube-prometheus translated from ServiceMonitors:

```
k -nmonitoring get secret prometheus-kube-prometheus-kube-prome-prometheus -oyaml | yq -r '.data."prometheus.yaml.gz"' | base64 -d | gunzip -c | yq '.scrape_configs.[].job_name'
```

To see rule and namespace selector:

```
k get prometheuses -A -oyaml  | grep -A 2 -B 2 -i Selector
```
