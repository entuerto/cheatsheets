
Disk Pools
==========

Zpools are the underlying device layers for zfs filesystems. Mirrors, RAIDs and Concatenated Storage are defined here. 

For pooling devices, zpools can be:

- a mirror
- a RAIDz with single or double parity
- a concatenated/striped storage

## Single Disk (No RAID)

```
# zpool create -f [poolName] ada2
```

## Multiple Disk (RAID 0)
This will create a pool of storage where data is striped across all of the devices specified. Loss of any of the drives will result in losing all of your data.
```
# zpool create -f [poolName] ada2 ada3  
```

### Adding Drives
You can add drives to a pool to increase its capacity. Any new data will be dynamically striped across the pool, but existing data will not be moved in order to "balance" the pool.
```
# zpool add [existing poolName] ada4  
```

## Raid 1
To create a RAID1 pool (or mirror), simply add the command mirror when creating or adding drives. 

For example:
```
# zpool create  -f [poolName] mirror ada2 ada3
```
```
# zpool add [existing poolName] mirror ada4 ada5
```

## RAID 10
Creating a RAID1 pool of two drives, and then adding another pair of mirroring drives as shown above would actually create a RAID 10 pool whereby data is stripped over two mirrors. This results in better performance without sacrificing redundancy.

To create a RAID10 array in a single command, simply run:
```
zpool create [poolName] mirror disk1 disk2 mirror disk3 disk4  
```

The output of `zpool status` would show:
```
  pool: myPool
 state: ONLINE
  scan: none requested
config:

        NAME        STATE     READ WRITE CKSUM
        myPool      ONLINE       0     0     0
          mirror-0  ONLINE       0     0     0
            ada1    ONLINE       0     0     0
            ada2    ONLINE       0     0     0
          mirror-1  ONLINE       0     0     0
            ada3    ONLINE       0     0     0
            ada4    ONLINE       0     0     0
```

## RAID Z (RAID 5)
RAIDz is very similar to RAID 5 in which parity is generated and stored in order for the RAID array to be able to gracefully handle a drive failing. This means that only one drive's worth of capacity is "lost" in order to provide redundancy for the entire pool of drives. This means that you lose a lot less capacity than with using mirrors which result in you losing 50% of your storage capacity. However, parity cannot be used for scrubbing like RAID 10 can, and calculating parity for every tile transaction can slow down your IO.

To create a RAIDz pool simply run:
```
# zpool create -f [poolName] raidz ada1 ada2 ada3
```

__You can't add to a RAIDz pool.__ If you were to run the command below:
```
# zpool add -f [poolName] ada4
```

Then you would be creating a RAID 0 array over the raidz pool and the drive you just added. 
This is best shown by the ouput of `zpool status`:
```
  pool: poolz
 state: ONLINE
  scan: none requested
config:

        NAME        STATE     READ WRITE CKSUM
        poolz       ONLINE       0     0     0
          raidz1-0  ONLINE       0     0     0
            ada1    ONLINE       0     0     0
            ada2    ONLINE       0     0     0
            ada3    ONLINE       0     0     0
          sde       ONLINE       0     0     0
```

## RAIDz2
This is much like RAIDz except that two drives will store parity instead of just one. This means that your array can lose any two drives without loss of data. This means it is actually safer than RAID10 where you would lose all of your data if two drives in the same mirror died. Using RAIDz2 over RAIDz makes a lot of sense because the likelihood of losing a drive when rebuilding a degraded RAIDz array, is greatly increased. Thus losing a drive during the rebuild shouldn't be a problem.

To create a RAIDz2 pool simply run:
```
# zpool create -f [poolName] raidz2 ada1 ada2 ada3
```

> __Note:__ You need a minimum of 3 drives

## RAIDz3
Exactly the same as RAIDz3 except a third drive holds parity and the minimum number of drives is 4. Your array can lose 3 drives without loss of data.

To create a RAIDz3 pool simply run:
```
# zpool create -f [poolName] raidz3 ada1 ada2 ada3 ada4
```

## Destroying Pools

### Delete a Pool
```
# zpool destroy [poolName]
```

### Delete All Datasets In A Pool
```
# zfs destroy -r [poolName] 
```

## Viewing Pools

### List Pools
```
# zpool list

NAME      USED   AVAIL  REFER  MOUNTPOINT
poolz     86.7K  7.80G  24.4K  /poolz

```

### Check Disk Statuses
If you're running a redundant raid, you may want to check if any drives have failed once in a while. This is done by just checking the pools.
```
# zpool status

  pool: poolz
 state: ONLINE
  scan: none requested
config:

        NAME        STATE     READ WRITE CKSUM
        poolz       ONLINE       0     0     0
          raidz1-0  ONLINE       0     0     0
            ada1    ONLINE       0     0     0
            ada2    ONLINE       0     0     0
            ada3    ONLINE       0     0     0
          sde       ONLINE       0     0     0
```