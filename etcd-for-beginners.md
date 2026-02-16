# ETCD for Beginners

> This document is designed for CKA revision and interview preparation.

ETCD is a distributed, reliable key-value store used by Kubernetes to store cluster state.  
Understanding ETCD is critical for the CKA exam and real-world troubleshooting.

---

# 1. What is ETCD?

ETCD is:

- A distributed key-value store
- Strongly consistent
- Leader-based (Raft consensus)
- Fault tolerant
- Used by Kubernetes control plane

In Kubernetes, ETCD stores:

- Pods
- Deployments
- Services
- ConfigMaps
- Secrets
- Nodes
- RBAC objects
- Cluster configuration

If ETCD is lost → Kubernetes cluster state is lost.

---

# 2. What Is a Key-Value Store?

Unlike relational databases (tables with rows and columns), a key-value store stores data as:

```
key → value
```

In Kubernetes, keys follow this structure:

```
/registry/<resource-type>/<namespace>/<resource-name>
```

Example:

```
/registry/configmaps/default/app-config
```

---

# 3. Real Kubernetes Example (Instead of Generic Data)

Create a ConfigMap:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: default
data:
  DATABASE_HOST: mysql-service
  DATABASE_PORT: "3306"
  ENVIRONMENT: production
```

Apply:

```bash
kubectl apply -f configmap.yaml
```

Internally:

1. kubectl → kube-apiserver
2. API server validates
3. Object converted to JSON
4. Stored in ETCD

Stored as:

```
/registry/configmaps/default/app-config
```

Value (simplified):

```json
{
  "metadata": {
    "name": "app-config",
    "namespace": "default"
  },
  "data": {
    "DATABASE_HOST": "mysql-service",
    "DATABASE_PORT": "3306",
    "ENVIRONMENT": "production"
  }
}
```

This is real-world ETCD data.

---

# 4. How ETCD Works – Raft Consensus

ETCD uses the Raft consensus algorithm.

Characteristics:

- One Leader
- Multiple Followers
- Writes go to Leader
- Majority (quorum) required to commit

---

## Raft Consensus Diagram


::contentReference[oaicite:0]{index=0}


---

## Raft Write Flow

1. Client sends write request to Leader.
2. Leader appends entry to its log.
3. Leader replicates to Followers.
4. Followers acknowledge.
5. Majority confirms.
6. Entry committed.
7. Client receives success response.

---

## Why Odd Number of Nodes?

| Nodes | Majority | Tolerates Failures |
|-------|----------|-------------------|
| 1     | 1        | 0 |
| 2     | 2        | 0 |
| 3     | 2        | 1 |
| 5     | 3        | 2 |

Production recommendation:
- Minimum: 3 nodes
- Large clusters: 5 nodes

---

# 5. Installing ETCD (Standalone Example)

Download:

```bash
curl -L https://github.com/etcd-io/etcd/releases/download/v3.3.11/etcd-v3.3.11-linux-amd64.tar.gz -o etcd.tar.gz
```

Extract and start:

```bash
tar -xvf etcd.tar.gz
./etcd
```

Default client port:

```
2379
```

Default data directory:

```
/var/lib/etcd
```

---

# 6. etcdctl – Command Line Tool

Used to interact with ETCD.

---

## Check Version

```bash
./etcdctl --version
```

Output may show:

```
API version: 2
```

---

# 7. API v2 vs API v3

Modern Kubernetes uses API v3.

Enable API v3:

```bash
export ETCDCTL_API=3
```

---

## Command Differences

| API v2 | API v3 |
|--------|--------|
| set | put |
| get | get |
| ls | get --prefix |
| cluster-health | endpoint health |

---

# 8. Basic API v3 Commands

Set key:

```bash
ETCDCTL_API=3 etcdctl put key1 value1
```

Get key:

```bash
ETCDCTL_API=3 etcdctl get key1
```

List all keys:

```bash
ETCDCTL_API=3 etcdctl get / --prefix --keys-only
```

---

# 9. Accessing ETCD in Kubernetes (TLS Secured)

In kubeadm clusters:

```bash
ETCDCTL_API=3 etcdctl \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
get /registry/configmaps/default/app-config
```

Important for CKA exam.

---

# 10. Backing Up ETCD (Very Important for CKA)

Backup:

```bash
ETCDCTL_API=3 etcdctl snapshot save snapshot.db
```

Restore:

```bash
ETCDCTL_API=3 etcdctl snapshot restore snapshot.db
```

If no backup → cluster must be rebuilt.

---

# 11. Failure Scenarios (Interview Focus)

## If ETCD loses quorum:

- Cluster becomes read-only
- No new deployments
- No updates possible

## If ETCD disk is full:

- Writes fail
- API server errors

## If ETCD is slow:

- API server slow
- Entire cluster performance degrades

---

# 12. Important Interview Questions

### What is ETCD?
Distributed key-value store used by Kubernetes control plane.

---

### Does kubelet talk to ETCD?
No. Only kube-apiserver communicates with ETCD.

---

### What is quorum?
Majority agreement required for writes.

---

### Where is a Pod stored?
```
/registry/pods/<namespace>/<pod-name>
```

---

### How many ETCD nodes should you have?
Odd number:
- 3 (recommended)
- 5 (large clusters)

---

### What happens if ETCD is deleted?
Cluster state lost.
Running containers may continue temporarily.

---

# Final Revision Summary

- ETCD stores entire cluster state.
- Uses Raft consensus.
- Requires majority quorum.
- Only API server communicates with ETCD.
- API v3 is standard.
- Backup is mandatory in production.
