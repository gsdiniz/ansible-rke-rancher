# Rancher Downstream Policies

Place downstream policy manifests here when they should be applied after a
cluster is registered, for example:

- network policies
- Pod Security Admission labels
- monitoring/logging configuration
- storage classes
- ingress controller configuration

Prefer applying workload-facing resources through Fleet or Argo CD after the
cluster is visible to Rancher.

