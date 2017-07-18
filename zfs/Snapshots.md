
Snapshots
=========

One of the beauties of ZFS is its built in snapshots. This is handy for so many reasons: replication, frequent restore points , or for testing out changes. Creating a snapshot of a dataset is fairly straightforward – just indicate the dataset path followed by an @ and a snapshot name.

### Snapshot Datasets
```
# zfs snapshot [poolName]/[datasetName]@[snapshotName] 
``` 

### List Snapshots
```
# zfs list -t snapshot  
```

### Rename Snapshots
```
# zfs rename [poolName]/[datasetName]@[old name] [new name]  
```

### Restore Snapshot
```
# zfs rollback -r [poolName]/[datasetName]@[snapshotName]
```

> __Warning:__ This will delete all snapshots that were taken after [snapshot name] was taken!

> __Note:__ The file system that you want to roll back is unmounted and remounted, if it is currently mounted. If the file system cannot be unmounted, the rollback fails. The `-f` option forces the file system to be unmounted, if necessary.

### Delete a Snapshot
```
# zfs destroy archive/fotos@snap1
```

### Snapshot Replication
In order to replicate between two ZFS file systems, you’ll need some sort of connectivity. In this case, I’ll use ZFS send to indicate what dataset will be sent, pipe it through a SSH connection, and then indicate where to receive the data on the remote end.

```
# zfs send poolName/mountPoint@s1 | ssh 192.168.1.2 zfs receive Tank/mountPoint
```

In the example above, we are assuming that snapshot named s1 exists for poolName/mountPoint. From there, we are connecting to the remote system (192.168.1.2) and writing the snapshot to Tank/mountPoint (where Tank is the name of the zpool on the remote end).
