# GlusterFS
GlusterFS, or Gluster File System, is an open-source distributed file system that provides scalable and highly available network-attached storage (NAS). It is designed to run on commodity hardware and is particularly well-suited for use in cloud computing and containerized environments. GlusterFS is part of the Red Hat family of open-source projects and is sometimes referred to as Red Hat Gluster Storage.

Key features and concepts of GlusterFS include:

1. **Distributed File System**: GlusterFS is distributed, meaning it can aggregate storage resources from multiple servers (often referred to as "bricks") and present them as a single, unified namespace. This distributed architecture allows for horizontal scaling and the ability to handle large amounts of data.

2. **Scalability**: You can easily scale a GlusterFS cluster by adding more storage nodes or bricks. The system will automatically rebalance data across the newly added storage resources, ensuring even distribution.

3. **High Availability**: GlusterFS provides data redundancy and high availability by replicating data across multiple bricks. If one brick or server fails, data can still be accessed from the replica copies, ensuring data integrity and minimal downtime.

4. **Data Striping**: GlusterFS supports data striping, which divides data into smaller chunks and distributes them across multiple bricks. This can improve both read and write performance.

5. **Data Replication**: You can configure GlusterFS to replicate data across multiple bricks for redundancy and fault tolerance. Data replication is often used to ensure data availability even if some bricks fail.

6. **POSIX-Compliant**: GlusterFS aims to be POSIX-compliant, which means it supports standard file system operations and can be used as a drop-in replacement for other file systems in many cases.

7. **Integration with Kubernetes**: GlusterFS can be integrated with Kubernetes to provide persistent storage for containers. This allows stateful applications to store and retrieve data across container restarts and rescheduling.

8. **Snapshots and SnapMirror**: GlusterFS supports snapshots, allowing you to create point-in-time copies of the file system. SnapMirror provides the ability to replicate snapshots to remote locations for disaster recovery.

9. **Management Tools**: GlusterFS provides management tools and a web-based GUI (Gluster Management Console or GMC) for easier cluster configuration, monitoring, and maintenance.

GlusterFS is often used in scenarios where scalable and highly available storage is required, such as big data analytics, media streaming, content delivery, and virtualization environments. It's important to plan your GlusterFS deployment carefully, considering factors like data distribution, replication, and failure recovery to meet your specific storage needs.

## SPEED
The performance of any storage solution, including GlusterFS, can vary based on several factors, including configuration, workload, and hardware. Here's a general comparison of GlusterFS with some other storage options:

1. **Local Disk Storage**:
   - **Read Speed**: Local disk storage typically offers high read speeds because data is stored directly on the local hard drive.
   - **Write Speed**: Write speeds can also be fast, but they depend on the performance of the local disk. SSDs generally provide faster write speeds than traditional HDDs.
   - **Redundancy**: Local storage lacks redundancy, so data is at risk if the disk fails.

2. **NFS (Network File System)**:
   - **Read Speed**: NFS can provide good read performance, especially when the NFS server is well-tuned and has low-latency access to storage.
   - **Write Speed**: NFS write performance can be good but may not be as fast as local storage due to network overhead.
   - **Redundancy**: NFS can be configured with redundancy and failover, depending on the NFS server setup.

3. **Ceph**:
   - **Read Speed**: Ceph, a distributed storage system, can offer high read speeds, especially when the data is distributed across multiple nodes.
   - **Write Speed**: Write performance in Ceph can be good, but it depends on the underlying hardware and configuration.
   - **Redundancy**: Ceph provides data redundancy through replication and data striping, enhancing fault tolerance.

4. **Amazon S3** (Object Storage):
   - **Read Speed**: S3 offers good read performance, particularly for large objects, thanks to its scalable architecture.
   - **Write Speed**: Write performance in S3 is generally acceptable but may not match the speed of local storage.
   - **Redundancy**: S3 offers high data durability and redundancy through replication across multiple data centers.

5. **GlusterFS**:
   - **Read Speed**: GlusterFS read performance can be competitive, especially when data is distributed and well-cached on clients.
   - **Write Speed**: GlusterFS write performance can vary depending on configuration, replication settings, and network latency.
   - **Redundancy**: GlusterFS provides redundancy options through data replication, improving data availability.

The choice between these storage solutions depends on your specific use case and requirements. For example:

- If you need high availability and data redundancy, distributed file systems like GlusterFS or Ceph might be a good choice.
- If you're working with cloud-based applications, object storage like Amazon S3 could be a suitable option.
- If you need local, high-speed storage for a single server, local disks or NFS might be sufficient.

When evaluating storage options, it's essential to consider factors such as data durability, scalability, ease of management, and cost, in addition to raw read and write performance. Conducting performance testing in your specific environment can help you make an informed decision based on your application's needs.

[ref](https://www.digitalocean.com/community/tutorials/how-to-create-a-redundant-storage-pool-using-glusterfs-on-ubuntu-20-04)
# installation



```
sudo apt update
sudo apt install glusterfs-server
sudo systemctl start glusterd.service
sudo systemctl enable glusterd.service
sudo systemctl status glusterd.service
```

* gluster1  ---> server
* gluster2  ---> server
* gluster3  ---> client



add to `/etc/hosts`

```
192.168.23.23 gluster1.example.com gluster1
192.168.23.24 gluster2.example.com gluster2
192.168.23.25 gluster3.example.com gluster3


```

enable firewall on port `24007`

```
# run on gluster1

sudo ufw allow from 192.168.23.24  to any port 24007
sudo ufw allow from 192.168.23.25  to any port 24007
sudo ufw deny 24007



# run on gluster2
sudo ufw allow from 192.168.23.23  to any port 24007
sudo ufw allow from 192.168.23.25  to any port 24007
sudo ufw deny 24007

```
by the way, I don't enable firewall 
```
# run on all gluster server 
ufw disable



gluster peer probe gluster2

gluster peer status

```






GlusterFS supports several types of volumes, each designed to serve specific use cases and requirements. The primary GlusterFS volume types include:

1. **Distributed Volume (Distribute):**
   - **Description:** A distributed volume distributes data across multiple bricks (storage servers) without any redundancy. Each file is stored entirely on one brick. This type is ideal for maximizing storage capacity and distributing data across servers for load balancing.
   - **Use Case:** When you need to aggregate storage across multiple servers without duplicating data for redundancy, often used for large-scale data storage.

2. **Replicated Volume (Replicate):**
   - **Description:** A replicated volume replicates data across multiple bricks to provide redundancy and fault tolerance. Data is stored on multiple bricks to ensure high availability, but it consumes more storage space compared to distributed volumes.
   - **Use Case:** When data redundancy and fault tolerance are critical, such as for ensuring data durability and high availability.

3. **Striped Volume (Stripe):**
   - **Description:** A striped volume breaks files into smaller segments and distributes those segments across multiple bricks. This enhances performance by utilizing multiple disks simultaneously for read/write operations, but it does not provide data redundancy.
   - **Use Case:** When you need to improve I/O performance by distributing data across multiple storage devices.

4. **Distributed-Replicated Volume (Distribute-Replicate):**
   - **Description:** A combination of distributed and replicated volumes. Data is first distributed across servers and then replicated within each server to provide both scalability and redundancy.
   - **Use Case:** When you want to combine the benefits of distribution for capacity scaling with replication for data protection.

5. **Distributed-Striped Volume (Distribute-Stripe):**
   - **Description:** A combination of distributed and striped volumes. Data is distributed across multiple servers and striped across storage devices within each server. This offers both capacity scaling and improved performance.
   - **Use Case:** When you need to scale both capacity and performance by combining distribution and striping.

6. **Distributed-Replicated-Striped Volume (Distribute-Replicate-Stripe):**
   - **Description:** This is a combination of all three volume types: distributed, replicated, and striped. It distributes data across servers, replicates it within servers, and stripes it across storage devices within each server. This provides high availability, scalability, and performance.
   - **Use Case:** In complex scenarios where you require a balance of high availability, scalability, and performance.

7. **Arbiter Volume (Arbiter):**
   - **Description:** An arbiter volume is used in conjunction with replicated volumes to reduce storage overhead. Instead of replicating data to a full replica node, it replicates only metadata and stores a smaller arbiter brick to decide which replica node contains the most up-to-date data.
   - **Use Case:** When you want to minimize storage overhead in replicated volumes while maintaining data integrity.

8. **Thinly Provisioned Volume (Thin):**
   - **Description:** Thin provisioning allows you to allocate storage space dynamically as needed, rather than pre-allocating all storage upfront. It helps save space and allows for more flexible storage allocation.
   - **Use Case:** When you want to efficiently use available storage resources and allocate space on-demand.

Each of these GlusterFS volume types has its own strengths and trade-offs, so choosing the appropriate volume type depends on your specific use case, performance requirements, fault tolerance needs, and available storage resources.



# replicated
```



gluster volume list

gluster volume create volume1 replica 2 gluster1.example.com:/mnt/sdb1 gluster2.example.com:/mnt/sdb1 force  # we use option `force` because we have even number of storage server(two gluster server), 

gluster volume list
gluster volume info
gluster volume start volume1

netstat -ntlu
gluster volume status




# remove volume

gluster volume stop volume1
gluster volume delete volume1



# run on gluster clinet

sudo apt install glusterfs-client
sudo mkdir /mnt/gluster-db
sudo mount -t glusterfs gluster1:volume1 /mnt/gluster-db/

```



# Distributed
```

# run on gluster server

gluster volume create volume2 gluster0.example.com:/mnt/sdb1 gluster1.example.com:/mnt/sdb1 gluster2.example.com:/mnt/sdb1 force

gluster volume info
gluster volume list
gluster volume status
gluster volume start volume2




# Run on gluster nodes
mount -t glusterfs gluster0:volume2 /mnt/gluster-db/

# now run the `df -Th` to see the storage size 
df -TH
df -Ph .



# delete the volume
gluster volume stop volume2
gluster volume delete volume2
```








# Additional command 

```
gluster pool list


```


## detach a node 
```
gluster peer list
gluster peer detach gluster2

```


# Enable quota

```

gluster volume quota <volume-name> list
gluster volume quota <volume-name> enable
gluster volume quota <volume-name> list


gluster volume quota <volume-name> limit-usage / 10MB
gluster volume quota <volume-name> list


# on client run 
df -TH



dd if=/dev/urandom of=test-file bs=6MB count=1
dd if=/dev/urandom of=test-file bs=6MB count=4


gluster volume quota <volume-name> disable

```



