# IOStackBreakdown

Nanosecond-Resolution Latency Measurements Tool for the Linux Kernel I/O Stack. Based on eBPF and using bcc framework.

## Authors

* Zhe Tang (Email: tangzh6101@gmail.com)

## Prerequisites

- Python 3.7+

- BCC 0.10.0+

- Kernel Configuration ([Reference](https://github.com/iovisor/bcc/blob/master/INSTALL.md#kernel-configuration))：

    > In general, to use these features, a Linux kernel version 4.1 or newer is required. In addition, the kernel should have been compiled with the following flags set:
    >
    > ```
    > CONFIG_BPF=y
    > CONFIG_BPF_SYSCALL=y
    > # [optional, for tc filters]
    > CONFIG_NET_CLS_BPF=m
    > # [optional, for tc actions]
    > CONFIG_NET_ACT_BPF=m
    > CONFIG_BPF_JIT=y
    > # [for Linux kernel versions 4.1 through 4.6]
    > CONFIG_HAVE_BPF_JIT=y
    > # [for Linux kernel versions 4.7 and later]
    > CONFIG_HAVE_EBPF_JIT=y
    > # [optional, for kprobes]
    > CONFIG_BPF_EVENTS=y
    > # Need kernel headers through /sys/kernel/kheaders.tar.xz
    > CONFIG_IKHEADERS=y
    > ```
    >
    > There are a few optional kernel flags needed for running bcc networking examples on vanilla kernel:
    >
    > ```
    > CONFIG_NET_SCH_SFQ=m
    > CONFIG_NET_ACT_POLICE=m
    > CONFIG_NET_ACT_GACT=m
    > CONFIG_DUMMY=m
    > CONFIG_VXLAN=m
    > ```
    >
    > Kernel compile flags can usually be checked by looking at `/proc/config.gz` or `/boot/config-<kernel-version>`.

## Dependencies

- Install BCC following the instructions [here](https://github.com/iovisor/bcc/blob/master/INSTALL.md).
- If you need to rebuild Linux kernel, you can follow the instructions [here](https://phoenixnap.com/kb/build-linux-kernel).

## Usage

```
python IOStackBreakdown.py [-m] [-p PID] [-Q] [-T type] [-d disk] 
```

### Arguments

- `-m`: Output in milliseconds
- `-p PID`: Trace this PID only
- `-Q`: Include OS queued time in I/O time
- `-T type`: Trace syscall, vfs, ext4 or block layer
- `-d disk`: Trace this disk only

### Notice

- While gauging the latency of the Block Layer, it is highly recommended to employ the `-d` flag in order to obtain precise and exact outcomes.
- Regrettably, due to the constraints imposed by the implementation of the Linux kernel, `-p` can solely assure the latency to the syscall layer, vfs layer and ext4 layer for a particular process.

## Sample Output

```bash
#Syscall
sudo ./IOStackBreakdown -T syscall -p 6681
Breaking Down Linux IO Stack's operation latency... Hit Ctrl-C to end.
^C
02:15:40

operation = sysc_w
     nsecs               : count     distribution
         0 -> 1          : 0        |                                        |
         2 -> 3          : 0        |                                        |
         4 -> 7          : 0        |                                        |
         8 -> 15         : 0        |                                        |
        16 -> 31         : 0        |                                        |
        32 -> 63         : 0        |                                        |
        64 -> 127        : 0        |                                        |
       128 -> 255        : 0        |                                        |
       256 -> 511        : 0        |                                        |
       512 -> 1023       : 0        |                                        |
      1024 -> 2047       : 0        |                                        |
      2048 -> 4095       : 0        |                                        |
      4096 -> 8191       : 2        |                                        |
      8192 -> 16383      : 0        |                                        |
     16384 -> 32767      : 0        |                                        |
     32768 -> 65535      : 0        |                                        |
     65536 -> 131071     : 0        |                                        |
    131072 -> 262143     : 0        |                                        |
    262144 -> 524287     : 1000     |****************************************|
b'sysc_w', 329906470ns

#VFS
sudo ./IOStackBreakdown -T vfs -p 6688
Breaking Down Linux IO Stack's operation latency... Hit Ctrl-C to end.
^C
02:16:16

operation = vfs_w
     nsecs               : count     distribution
         0 -> 1          : 0        |                                        |
         2 -> 3          : 0        |                                        |
         4 -> 7          : 0        |                                        |
         8 -> 15         : 0        |                                        |
        16 -> 31         : 0        |                                        |
        32 -> 63         : 0        |                                        |
        64 -> 127        : 0        |                                        |
       128 -> 255        : 0        |                                        |
       256 -> 511        : 0        |                                        |
       512 -> 1023       : 0        |                                        |
      1024 -> 2047       : 0        |                                        |
      2048 -> 4095       : 0        |                                        |
      4096 -> 8191       : 1        |                                        |
      8192 -> 16383      : 1        |                                        |
     16384 -> 32767      : 0        |                                        |
     32768 -> 65535      : 0        |                                        |
     65536 -> 131071     : 0        |                                        |
    131072 -> 262143     : 0        |                                        |
    262144 -> 524287     : 983      |****************************************|
    524288 -> 1048575    : 17       |                                        |

operation = vfs_o
     nsecs               : count     distribution
         0 -> 1          : 0        |                                        |
         2 -> 3          : 0        |                                        |
         4 -> 7          : 0        |                                        |
         8 -> 15         : 0        |                                        |
        16 -> 31         : 0        |                                        |
        32 -> 63         : 0        |                                        |
        64 -> 127        : 0        |                                        |
       128 -> 255        : 0        |                                        |
       256 -> 511        : 0        |                                        |
       512 -> 1023       : 824      |****************************************|
      1024 -> 2047       : 167      |********                                |
      2048 -> 4095       : 9        |                                        |
b'vfs_o', 952072ns
b'vfs_w', 344076662ns

#EXT4
sudo ./IOStackBreakdown -T ext4 -p 6706
Breaking Down Linux IO Stack's operation latency... Hit Ctrl-C to end.
^C
02:17:32

operation = extfsync
     nsecs               : count     distribution
         0 -> 1          : 0        |                                        |
         2 -> 3          : 0        |                                        |
         4 -> 7          : 0        |                                        |
         8 -> 15         : 0        |                                        |
        16 -> 31         : 0        |                                        |
        32 -> 63         : 0        |                                        |
        64 -> 127        : 0        |                                        |
       128 -> 255        : 0        |                                        |
       256 -> 511        : 0        |                                        |
       512 -> 1023       : 0        |                                        |
      1024 -> 2047       : 0        |                                        |
      2048 -> 4095       : 0        |                                        |
      4096 -> 8191       : 0        |                                        |
      8192 -> 16383      : 0        |                                        |
     16384 -> 32767      : 0        |                                        |
     32768 -> 65535      : 0        |                                        |
     65536 -> 131071     : 0        |                                        |
    131072 -> 262143     : 0        |                                        |
    262144 -> 524287     : 999      |****************************************|
    524288 -> 1048575    : 1        |                                        |

operation = extopen
     nsecs               : count     distribution
         0 -> 1          : 0        |                                        |
         2 -> 3          : 0        |                                        |
         4 -> 7          : 0        |                                        |
         8 -> 15         : 0        |                                        |
        16 -> 31         : 0        |                                        |
        32 -> 63         : 0        |                                        |
        64 -> 127        : 0        |                                        |
       128 -> 255        : 0        |                                        |
       256 -> 511        : 939      |****************************************|
       512 -> 1023       : 60       |**                                      |
      1024 -> 2047       : 0        |                                        |
      2048 -> 4095       : 1        |                                        |

operation = extwrite
     nsecs               : count     distribution
         0 -> 1          : 0        |                                        |
         2 -> 3          : 0        |                                        |
         4 -> 7          : 0        |                                        |
         8 -> 15         : 0        |                                        |
        16 -> 31         : 0        |                                        |
        32 -> 63         : 0        |                                        |
        64 -> 127        : 0        |                                        |
       128 -> 255        : 0        |                                        |
       256 -> 511        : 0        |                                        |
       512 -> 1023       : 0        |                                        |
      1024 -> 2047       : 0        |                                        |
      2048 -> 4095       : 0        |                                        |
      4096 -> 8191       : 0        |                                        |
      8192 -> 16383      : 0        |                                        |
     16384 -> 32767      : 0        |                                        |
     32768 -> 65535      : 0        |                                        |
     65536 -> 131071     : 0        |                                        |
    131072 -> 262143     : 0        |                                        |
    262144 -> 524287     : 1000     |****************************************|
b'extopen', 443774ns
b'extwrite', 325436176ns
b'extfsync', 346928293ns

#Block I/O
sudo ./IOStackBreakdown -T blk -d nvme0n1 -Q
Breaking Down Linux IO Stack's operation latency... Hit Ctrl-C to end.
^C
02:19:11

operation = bio_op
     nsecs               : count     distribution
         0 -> 1          : 0        |                                        |
         2 -> 3          : 0        |                                        |
         4 -> 7          : 0        |                                        |
         8 -> 15         : 0        |                                        |
        16 -> 31         : 0        |                                        |
        32 -> 63         : 0        |                                        |
        64 -> 127        : 0        |                                        |
       128 -> 255        : 0        |                                        |
       256 -> 511        : 0        |                                        |
       512 -> 1023       : 0        |                                        |
      1024 -> 2047       : 0        |                                        |
      2048 -> 4095       : 0        |                                        |
      4096 -> 8191       : 1557     |***************                         |
      8192 -> 16383      : 443      |****                                    |
     16384 -> 32767      : 4        |                                        |
     32768 -> 65535      : 1001     |*********                               |
     65536 -> 131071     : 2803     |***************************             |
    131072 -> 262143     : 4111     |****************************************|
    262144 -> 524287     : 87       |                                        |
b'bio_op', 1139312161ns
```

## Future Work

- [ ] Fix the known bugs of measuring Direct I/O.
- [ ] Provide validation benchmark.
- [ ] Support more filesystems like XFS, Btrfs etc.
- [ ] Trace more I/O stacks like NVMe, SCSI etc.
- [ ] Provide latency breakdown at finer granularity.

## Reference

- [BCC Tools](https://github.com/iovisor/bcc/tree/master/tools)
- [BCC Reference Guide](https://github.com/iovisor/bcc/blob/master/docs/reference_guide.md)
- [BCC Tutorials](https://github.com/iovisor/bcc/blob/master/docs/tutorial.md)