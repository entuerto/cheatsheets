
ZFS Record Size
=================

Using the appropriate record size.

Creating a dataset is as easy as
```
# zfs create poolName/datasetName
```

Use the following command to set the appropriate record size for the different data types.
```
# zfs set recordsize=[size] /archive/media
```

For things like movies and TV series datasets, set a size of 1 megabyte.
```
# zfs set recordsize=1M /archive/media/videos
```

For databases, set it to just 16K.
```
# zfs set recordsize=16K /archive/db
```

## Some Facts About Record Size
- The default record size for ZFS is 128K.
- Changing the record size will only have an effect on new files. It will not have any effect on existing files.
- All files are stored either as a single block of varying sizes (up to the __recordsize__) or using multiple __recordsize__ blocks.
    * This suggests to me that using a block size of 1M will not cause your 1024 text files to eat up 1 Megabyte of disk space.
    * Your file's block size will never be greater than the record size, but can be smaller.
- The blocksize is the size that ZFS validates through checksums.

### Performance Impacts
Tuning the record size should not have much effect on burst workloads of 5 seconds or less, or those workloads that are sequential in nature.

Early storage bandwidth saturation can occur when using overly large record sizes. If an application such as an Innodb database, requests 16K of data, then fetching a 128K block/record doesn't change the latency that the application sees but will waste bandwidth on the disk's channel. A 100 MB channel could handle just under 800 requests at 128k, or it could handle a staggering 6,250 random 16k requests. This is an important factor as you probably don't want to gimp your MySQL database to under 800 iops.

Another factor, is that a partial write to an uncached block will require the system to go fetch the rest of the block's data from disk before the block can be updated. Otherwise how is ZFS going to know what the new checksum should be? However, if your blocks are smaller, then updates are likely to be at least an entire block or more in size. These "full" blocks can be written without needing to fetch data first.

Before you go ahead and reduce your block size to something tiny, like 4K, there are good reasons for having larger block sizes. Having larger blocks means there are less blocks that need to be written in order to write a large piece of data, such as a movie. Less blocks means less metadata to write/manage. Many of the aspects of the ZFS filesystem, such as caching, compression, checksums, and de-duplication work on a block level, so having a larger block size are likely to reduce their overheads.

## References
- [Oracle - Tuning ZFS recordsize](https://blogs.oracle.com/roch/entry/tuning_zfs_recordsize)
- [OpenZFS - Performance tuning](http://open-zfs.org/wiki/Performance_tuning#Dataset_recordsize)
- [Joyent - Bruning Questions: ZFS Record Size](https://www.joyent.com/blog/bruning-questions-zfs-record-size)