**Note: This module is provided purely as an example and not as an official Codecov Enterprise deployment strategy. If you want to use this configuration to test Codecov Enterprise on your own internally maintained cluster, that is fine. Codecov, however, _will not support_ its use in production environments, nor will we provide support for this module's installation or continued use in any context.**

# Codecov terraform module for kubernetes

This module provides an example of how to set up Codecov Enterprise in a 
kubernetes cluster.

It is *highly* recommended to deploy Codecov Enterprise into a managed k8s cluster on one of the major cloud providers: AWS, GCP, or Azure. Those terraform configurations are supported and provided elsewhere in this repository. Codecov will fully support these configurations if problems arise as a result of their use.

## Prerequisites

- A working [kubernetes cluster](https://kubernetes.io/docs/home/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) configured
  to access your cluster.

## Required services

- A postgresql v10 server configured to allow connections from your k8s cluster. 
- A redis server configured to allow connections from your k8s cluster.
- An NFS server configured to allow connections from your k8s cluster.  This
  will be used to store coverage reports.

## Module Parameters

| name | description | default |
| --- | --- | --- |
| `config_context` | kubectl config context used to connect to your cluster | required |
| `web_replicas` | number of web pod replicas to run | 2 |
| `worker_replicas` | number of worker pod replicas to run | 2 |
| `minio_replicas` | number of minio pod replicas to run | 4 |
| `ingress_host` | hostname used to connect to codecov | required |
| `traefik_replicas` | number of traefik ingress controller pod replicas to run | 2 |
| `enable_https` | whether to enable https ingress.  requires tls key and cert | false |
| `tls_key` | path to TLS private key in PEM format | |
| `tls_cert` | path to TLS certificate in PEM format | |
| `codecov_yml` | path to your enterprise [codecov.yml](https://docs.codecov.io/docs/configuration) | required |
| `nfs_pv_host` | NFS host for minio persistent volume | required |
| `nfs_pv_path` | NFS export path for minio persistent volume | required |
| `nfs_pv_size` | Size of NFS persistent volume claim | required |
| `minio_access_key` | minio access key.  Can be used to access minio from outside the cluster using common s3 tools | required |
| `minio_secret_key`| minio secret key.  Can be used to access minio from outside the cluster using common s3 tools | required |

## Setup

1. Install the module in your terraform project.
1. Configure the module
    ```
    # example module definition
    module "codecov-enterprise" {
      source = "path/to/terraform-k8s-codecov"
      config_context = "kubernetes-admin@kubernetes"
      web_replicas = "2"
      worker_replicas = "2"
      minio_replicas = "4"
      codecov_yml = "path/to/codecov.yml"
      nfs_pv_host = "nfs.server.hostname"
      nfs_pv_path = "/exports/minio"
      nfs_pv_size = "10Gi"
      minio_access_key = "access_key"
      minio_secret_key = "super_secret_key"
      ingress_host = "codecov.yourdomain.com"
      enable_https = "1"
      tls_key = "path/to/key.pem"
      tls_cert = "path/to/cert.pem"
    }
    ```
1. Run `terraform init` to import the module and the required terraform
   providers.
1. Carry on with normal [terraform usage](https://learn.hashicorp.com/terraform/getting-started/build.html) (`terraform plan`, `terraform apply`)
