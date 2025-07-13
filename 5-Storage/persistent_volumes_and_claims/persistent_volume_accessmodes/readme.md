## Kubernetes Persistent Volume Access Modes 

| Access Mode                 | Description                                                                              |
| --------------------------- | ---------------------------------------------------------------------------------------- |
| **ReadWriteOnce (RWO)**     | Mounted as read-write by a single node                                                   |
| **ReadOnlyMany (ROX)**      | Mounted as read-only by many nodes                                                       |
| **ReadWriteMany (RWX)**     | Mounted as read-write by many nodes                                                      |
| **ReadWriteOncePod (RWOP)** | Mounted as read-write by a single Pod (newer mode from Kubernetes 1.22+, mainly for CSI) |


## Note: Not all volumes support all access modes.