
Datasets
========

### Create a Dataset
```
# zfs create [poolName]/[datasetName]

# zfs list
NAME           USED  AVAIL  REFER  MOUNTPOINT
zfstest        118K  23.4G  25.5K  /zfstest
```

You can create a "descendent" dataset/filesystem:
```
# zfs create [poolName]/[datasetName]/[descendent]

# zfs create zfstest/king
# zfs list
NAME           USED  AVAIL  REFER  MOUNTPOINT
zfstest        118K  23.4G  25.5K  /zfstest
zfstest/king  24.5K  23.4G  24.5K  /zfstest/king
```

### List Datasets and Pools
```
# zpool list  
NAME                    SIZE    USED   AVAIL    CAP  HEALTH     ALTROOT
zfstest                23.8G     91K   23.8G     0%  ONLINE     -

# zfs list
NAME      USED  AVAIL  REFER  MOUNTPOINT
zfstest    88K  23.4G  24.5K  /zfstest
```

### Delete Dataset
```
# zfs destroy [pool name]/[dataset name]
```

> __Note:__ A dataset cannot be destroyed if snapshots or clones of the dataset exist.

### Set Dataset Record Size
```
# zfs set recordsize=[size] pool/dataset/name
```

### Get Dataset Record Size
```
# zfs get recordsize pool/dataset/name
```

### Getting filesystem options
```
# zfs get all zfstest/queen
NAME           PROPERTY       VALUE                  SOURCE
zfstest/queen  type           filesystem             -
zfstest/queen  creation       Wed Apr 23  6:05 2008  -
zfstest/queen  used           24.5K                  -
zfstest/queen  available      5.00G                  -
zfstest/queen  referenced     24.5K                  -
zfstest/queen  compressratio  1.00x                  -
zfstest/queen  mounted        yes                    -
zfstest/queen  quota          5G                     local
zfstest/queen  reservation    5G                     local
zfstest/queen  recordsize     128K                   default
zfstest/queen  mountpoint     /zfstest/queen         default
zfstest/queen  sharenfs       off                    default
zfstest/queen  checksum       on                     default
zfstest/queen  compression    on                     local
zfstest/queen  atime          on                     default
zfstest/queen  devices        on                     default
zfstest/queen  exec           on                     default
zfstest/queen  setuid         on                     default
zfstest/queen  readonly       off                    default
zfstest/queen  zoned          off                    default
zfstest/queen  snapdir        hidden                 default
zfstest/queen  aclmode        groupmask              default
zfstest/queen  aclinherit     secure                 default
zfstest/queen  canmount       on                     default
zfstest/queen  shareiscsi     off                    default
zfstest/queen  xattr          on                     default
```
