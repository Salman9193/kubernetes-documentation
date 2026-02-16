# ETCD Complete CKA Guide

## What is ETCD
ETCD is a distributed key-value store that provides a reliable way to store data across a cluster of machines. It is often used to hold the configuration data for distributed systems and is a core component of Kubernetes.

## Key-Value Store Explained
A key-value store is a simple database that uses an associative array as its data model, where every key is unique and maps to a specific value. It offers quick lookups and is essential for performance-critical applications.

## Real Example ConfigMap
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  database-url: "http://127.0.0.1:5432"
```

## How ETCD Works with Raft Consensus
ETCD utilizes the Raft consensus algorithm to manage a replicated log. It ensures that all changes are consistent across nodes, even in the event of network partitions.

## Installing ETCD
```bash
# Install etcd on Ubuntu
echo "deb http://ubuntu/etc/repo/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/etcd.list
sudo apt update
sudo apt install etcd
```

## etcdctl Commands
Commands to interact with etcd:
```bash
# Set a key-value pair
etcdctl put mykey "Hello World"

# Get a value
etcdctl get mykey
```

## API v2 vs v3 Differences
- **API v2** uses simple key-value pairs.
- **API v3** supports features like leases, watch, and transactions, making it more powerful and flexible.

## Accessing ETCD in Kubernetes
ETCD can be accessed within Kubernetes clusters via the `kubectl` command or by directly interacting with the etcd API.

## Backing up and Restoring ETCD
To back up etcd, you can use the following command:
```bash
ETCDCTL_API=3 etcdctl snapshot save backup.db
```
To restore:
```bash
ETCDCTL_API=3 etcdctl snapshot restore backup.db
```

## Failure Scenarios
Some common failure scenarios include node failure, network partition, and data corruption. It is crucial to have a backup and recovery plan to mitigate these issues.

## Interview Questions with Key Takeaways
1. **What is ETCD?**  
   - A distributed key-value store used for configuration management.
2. **How does Raft Consensus work?**  
   - Raft provides a method for managing a replicated log.

Key takeaway: Understanding ETCD is essential for managing cluster state and configurations effectively in Kubernetes.