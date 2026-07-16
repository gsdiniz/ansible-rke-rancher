# GitOps for Rancher Downstream Clusters

This directory contains declarative examples for Rancher downstream clusters of
type custom/imported.

## Imported clusters

The expected flow for a cluster that already has Kubernetes is:

1. Apply `gitops/rancher/clusters/<env>` to the Rancher management cluster.
2. Wait for Rancher to create the `ClusterRegistrationToken` status.
3. Apply the generated registration manifest to the downstream cluster.
4. Let Rancher/Fleet/Argo CD manage post-registration resources.

For example:

```bash
kubectl --kubeconfig rancher.kubeconfig apply -k gitops/rancher/clusters/dev
kubectl --kubeconfig rancher.kubeconfig \
  -n downstream-dev \
  get clusterregistrationtoken.management.cattle.io default-token \
  -o jsonpath='{.status.manifestUrl}'
```

Apply the returned manifest URL to the downstream cluster:

```bash
curl -sfL "$MANIFEST_URL" | kubectl --kubeconfig downstream.kubeconfig apply -f -
```

## Custom clusters on empty VMs

The expected flow for empty VMs is:

1. Choose the CNI overlay before the cluster is created:
   - `gitops/rancher/custom-clusters/dev` creates a custom RKE2 cluster with Calico.
   - `gitops/rancher/custom-clusters/dev-cilium` creates a custom RKE2 cluster with Cilium, kube-proxy replacement, Hubble Relay, and Hubble UI.
2. Apply `gitops/rancher/custom-clusters/<env>` to the Rancher management
   cluster.
3. Wait for Rancher to publish the node registration command in the
   `ClusterRegistrationToken` status.
4. Copy `inventory/custom-downstream.example.yml` to an ignored inventory and
   set the VM addresses and desired node roles.
5. Run:

```bash
ansible-playbook -i inventory/custom-downstream.local.yml playbook/register-custom-downstream.yml
```

The playbook reads the registration command from Rancher and executes it on the
VMs with `--etcd`, `--controlplane`, and/or `--worker` according to inventory
host variables. The command contains credentials and is hidden with `no_log`.

Do not change the CNI overlay after the custom cluster is created. Create a new
cluster instead, then migrate workloads.

For the Cilium overlay, external HTTP/HTTPS traffic is expected to enter through
the packaged `rke2-ingress-nginx` controller. Keep application Services as
`ClusterIP`, create Kubernetes `Ingress` resources for routing, and let Cilium
LB IPAM allocate the `LoadBalancer` VIP only to the ingress controller Service
selected by the `lb-pool: bgp` label.
The Cilium overlay runs ingress-nginx as a `Deployment` with
`externalTrafficPolicy: Local` and selects worker nodes labeled
`ingress-ready=true`.

Do not commit Rancher API keys, kubeconfigs, registration commands, generated
tokens, or downstream cluster credentials.
