---
layout: post
title:  "Latency Test For AWS Block Devices"
date:   2019-06-02 12:00:00 +0400
categories: AWS
---

AWS documentation explains a lot about throughput and IO performance for every block device but it does not give much information on latency. In the documentation you will read words like low latency and very low latency but what does that mean. I recently encountered a situation were I wanted know the exact numbers. I ran some tests below are the results.  

Region: Ireland  
Instance: m5d.large  
OS: Amazon Linux release 2 (Karoo)

I used IOping to measure the latency, first EFS with General Purpose performance mode. 
```
# ioping -c 100 /mnt/efs/
99 requests completed in 279.2 ms, 396 KiB read, 354 iops, 1.38 MiB/s
generated 100 requests in 1.65 min, 400 KiB, 1 iops, 4.04 KiB/s
min/avg/max/mdev = 2.38 ms / 2.82 ms / 4.50 ms / 410.1 us
```

EFS with Max/IO 
```
99 requests completed in 360.9 ms, 396 KiB read, 274 iops, 1.07 MiB/s
generated 100 requests in 1.65 min, 400 KiB, 1 iops, 4.04 KiB/s
min/avg/max/mdev = 2.88 ms / 3.65 ms / 17.3 ms / 1.58 ms
```

Now EBS gp2 with xfs as the filesystem  
Read Latency  
```
99 requests completed in 31.1 ms, 396 KiB read, 3.19 k iops, 12.4 MiB/s
generated 100 requests in 1.65 min, 400 KiB, 1 iops, 4.04 KiB/s
min/avg/max/mdev = 258.2 us / 313.9 us / 821.3 us / 75.4 us
```
Write Latecny  
```
99 requests completed in 76.9 ms, 396 KiB written, 1.29 k iops, 5.03 MiB/s
generated 100 requests in 1.65 min, 400 KiB, 1 iops, 4.04 KiB/s
min/avg/max/mdev = 621.2 us / 776.4 us / 1.33 ms / 110.0 us
```

Generally all EBS volume types have similar latency and vary a little bit from one test to the other. Here is the test with st1 hdd storage type
```
99 requests completed in 77.4 ms, 396 KiB read, 1.28 k iops, 5.00 MiB/s
generated 100 requests in 1.65 min, 400 KiB, 1 iops, 4.04 KiB/s
min/avg/max/mdev = 189.9 us / 782.0 us / 13.9 ms / 2.45 ms
```

Now local NVMe that is available with m5d family instances. I have ran multiple test on the local NVMe and each time they differ a little bit, the average latency changes if the EC2 is running on a different host.   
```
99 requests completed in 8.18 ms, 396 KiB read, 12.1 k iops, 47.3 MiB/s
generated 100 requests in 1.65 min, 400 KiB, 1 iops, 4.04 KiB/s
min/avg/max/mdev = 56.7 us / 82.6 us / 133.5 us / 21.1 us
```
