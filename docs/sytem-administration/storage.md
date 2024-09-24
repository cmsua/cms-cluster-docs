# Rook Debugging

## Access Ceph Toolbox

```bash
kubectl -n rook-ceph-cluster exec -it $(kubectl -n rook-ceph-cluster get pod -l "app=rook-ceph-tools" -o jsonpath='{.items[0].metadata.name}') -- bash
```

# Remove / Move OSD

```bash
kubectl -n rook-ceph scale deployment rook-ceph-operator --replicas=0

kubectl -n rook-ceph-cluster scale deployment rook-ceph-osd-<ID> --replicas=0
kubectl -n rook-ceph-cluster exec -it $(kubectl -n rook-ceph-cluster get pod -l "app=rook-ceph-tools" -o jsonpath='{.items[0].metadata.name}') -- ceph osd down osd.<ID>

kubectl -n rook-ceph scale deployment rook-ceph-operator --replicas=1

```