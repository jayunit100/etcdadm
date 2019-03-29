etcdadm
=======

etcdadm is a command-line tool for operating an etcd cluster. It makes it easy to create a new cluster, add a member to, or remove a member from an existing cluster. Its user experience is inspired by [kubeadm](https://kubernetes.io/docs/reference/setup-tools/kubeadm/).

<p align="center">
    <img src="https://cdn.rawgit.com/platform9/etcdadm/master/demo.svg">
</p>

## Table of Contents

  - [Getting Started](#getting-started)
    - [Installing](#installing)
    - [Creating a new cluster](#creating-a-new-cluster)
    - [Adding a member](#adding-a-member)
    - [Removing a member](#removing-a-member)
  - [Advanced Usage](#advanced-usage)
    - [Creating a new cluster from a snapshot](#creating-a-new-cluster-from-a-snapshot)
  - [Caveats & Limitations](#caveats--limitations)
  - [Design](#design)
  - [Questions](#questions)

## Getting Started

### Quick start: How to quickly create a production etcd cluster using etcdadm

- Build a release of etcdadm.  Assuming your nodes are linux, run `GOOS=linux make container-build`.
- copy the created `etcdadm` file it to every node on your cluster.
- Create a new cluster on your first node.
- On successive nodes, just run "Adding a member" instructions below.

### Building, Contributing

#### As a developer
- Setup Golang: The fastest way to do this is using https://github.com/travis-ci/gimme
- Then run `go get -u sigs.k8s.io/etcdadm`

#### As a user
- Build in docker, it's easy ! Just run `make container-build`.
- The outputted binary will be natively runnable on your mac or linux machine.

### Contributing
- Follow the above directions, 
- cd $GOPATH/github.com/sigs.k8s.io/etcdadm
- Submit a PR !

## Creating a new cluster

### Step 1: Bootstrapping your first member

1. Copy `etcdadm` to each machine that will become a member.
2. Choose one machine and run

```
etcdadm init
```

You now have an etcd 'cluster' which is usable, of size 1.

### Step 2: Adding a member

Although not necessary, it's obviously a good idea to have more then 1 node in your cluster.

1. Copy the CA certificate and key from any machine in the cluster to the machine being added.
2. Choose a cluster endpoint (i.e. client URL of some member) and run

```
etcdadm join <endpoint>
```

### Removing a member

From time to time, you need to remove etcd nodes.  On the machine being removed, run

```
etcdadm reset
```

## Advanced Usage

### Creating a new cluster from a snapshot

If you have an existing etcd snapshot, you can use it to create a new cluster:

```
etcdadm init --snapshot /path/to/etcd.snapshot
```

## Caveats and Limitations

1. Must run as root. (This is because etcdadm creates a systemd service)
2. Does not support etcd v2.
3. Currently tested on Container Linux, with plans for other platforms.

## Design

The goal of etcdadm is to make it easy to operate an etcd cluster. It downloads a specific etcd release, installs the binary, configures a systemd service, generates certificates, calls the etcd API to add (or remove) a member, and verifies that the new member is healthy.

Etcdadm must be run on the machine that is being added or removed. As a consequence, if a member permanently fails, and the operator cannot invoke `etcdadm reset` on that machine, the operator must use the etcd API to delete the failed member from the list of members.

On its own, etcdadm does not automate cluster operation, but a cluster orchestrator can delegate all the above tasks to etcdadm.

<a name="questions"></a>
## Questions?

For more information reach out to [etcdadm slack channel](https://kubernetes.slack.com/messages/CEM0AT9GW)
