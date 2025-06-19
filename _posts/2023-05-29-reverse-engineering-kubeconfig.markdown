---
layout: default
title: "Reverse-engineering Kubeconfig"
date: 2023-05-29 19:02:16 +0200
categories: post
tags: kubernetes kubeconfig
---

Imagine I've been working with Kubernetes for years, but kubeconfig was always somehow delivered to me and I didn't really look on it. Until now!

## Cluster CA

Kubernetes API Server provides a HTTP REST API with TLS enabled. There are [multiple authentication methods](https://kubernetes.io/docs/reference/access-authn-authz/authentication/), but from the client perspective, we are making regular HTTP call:

```
$ curl https://192.168.0.229:6443
[ fails with invalid certificate authority ]
```

CA for the cluster isn't installed on our system - it's not a well-known public CA (like Let's encrypt). Instead, every cluster has own CA certificate and deals with TLS termination. That's different from the "normal" scenario of exposing HTTPS servers - certificates are sorted out on ingress / reverse proxy level.

CA can be found in our Kubeconfig:

```
$ cat $KUBECONFIG | yq '.clusters[0].cluster.certificate-authority-data' | base64 -d > ca.crt
$ curl --cacert ca.crt https://192.168.0.229:6443
{
  "kind": "Status",
  "apiVersion": "v1",
  "metadata": {},
  "status": "Failure",
  "message": "Unauthorized",
  "reason": "Unauthorized",
  "code": 401
}
```

## User credential

User credentials can be provided to the HTTP request in two ways:

### Certificates

Client certificates are pretty common, Kubeconfig generated from my simple K3S installation contains client certificates.

```
$ cat $KUBECONFIG | yq '.users[0].user.client-certificate-data' | base64 -d  > client.crt
$ cat $KUBECONFIG | yq '.users[0].user.client-key-data' | base64 -d  > client.key
```

We can check certificate contents by:

```
$ openssl x509 -in client.crt -text -noout  | egrep 'Issuer:|Subject:'
        Issuer: CN = k3s-client-ca@1670179571
        Subject: O = system:masters, CN = system:admin
```

`O` and `CN` correspond to group and username for Kubernetes RBAC. We can now use client certificates to issue the request:

```
$ curl --cacert ca.crt --cert client.crt --key client.key https://192.168.0.229:6443/api/v1/namespaces
[ displays namespaces ]
```

### Token

Tokens are passed in HTTPS request's header. For example in GKE clusters, piece of Kubeconfig that represents token looks the following:

```
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args: null
      command: gke-gcloud-auth-plugin
      env: null
      installHint: Install gke-gcloud-auth-plugin for use with kubectl by following
        https://cloud.google.com/blog/products/containers-kubernetes/kubectl-auth-changes-in-gke
      interactiveMode: IfAvailable
      provideClusterInfo: true

```

If we just call `gke-gcloud-auth-plugin`, we are receiving JSON file with `status.token` entry:

```
$ export TOKEN=$(gke-gcloud-auth-plugin | yq .status.token)
```

We can use this token to call API directly:

```
$ curl --cacert ca.crt -H "Authorization: $TOKEN" https://gke-cluster-address/api/v1/namespaces
[ displays namespaces ]
```

## What to check next

- [jjlakis/reverse-engineering-kubeconfig](https://github.com/jjlakis/reverse-engineering-kubeconfig) - my script that reverse engineers Kubeconfig entries and writes summary
- [Kubeconfig API reference](https://kubernetes.io/docs/reference/config-api/kubeconfig.v1/)
