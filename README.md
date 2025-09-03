# Configure and Install RKE2 + Rancher

Ansible playbook to configure RKE2 Cluster with Rancher installed using custom CA root.

## Prerequisites
1. 3 or more hosts / vm's - EL's distros (Oracle Linux, SUSE Linux, Red Hat, Alma Linux, Rocky Linux)
1. Public ssh keys configured in hosts
1. Internet Connection
1. Custom CA root configured
1. Custom tls-certificate to be used in https Rancher's URL

## Playbook - Inventory

Configure `./inventory/hosts.yml` with hosts information:

* IP
* Hostname

## Playbook - RKE2

This playbook is divided in 4 roles:

* Init
* Principal
* Masters
* Agents

### Init

This role will 

### Principal

This role will 

### Masters

This role will 

### Agents

This role will 

### Starting Up

```
ansible-playbook -i inventory/hosts.yml playbook/init-cluster.yml
```

## Playbook - RANCHER

After generating necessary files in certs/tls-rancher folder:

* ca-additional.pem
    * This certificate represents additional trusted CA's | [docs](https://ranchermanager.docs.rancher.com/getting-started/installation-and-upgrade/installation-references/helm-chart-options#additional-trusted-cas)

* internal-ca.pem
    * This certificate is necessary when using a a private CA, Rancher requires a copy of the private CA's root certificate or certificate chain, which the Rancher Agent uses to validate the connection to the server | [docs](https://ranchermanager.docs.rancher.com/getting-started/installation-and-upgrade/resources/add-tls-secrets)


* rancher.local.crt / rancher.local.key
    * TlS crt and key that will be used in https 

```
ansible-playbook -i inventory/hosts.yml playbook/install-rancher.yml
```
