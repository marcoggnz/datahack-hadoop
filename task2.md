# Task 2: Sizing the Hadoop Cluster

<strong>The CEO is very pleased with the work done and wants to invest even more in Big Data technologies. He is considering setting up another Hadoop infrastructure to process movie events coming from various sources (cinemas, streaming platforms, etc.) and needs an estimate of the platform size considering the following:

| Source    | Daily Events    | Size per Event    |
|-----------|-----------------|-------------------|
| Source 1  | 10,000          | 15 KB             |
| Source 2  | 120,000         | 300 Bytes         |
| Source 3  | 150,000         | 100 KB            |
| Source 4  | 170,000         | 800 KB            |
| Source 5  | 2,000           | 1500 KB           |

The machine specifications are capable of having up to 22 disks of 2 TB for storage each. Indicate the number of machines required to store the total volume of data for the next year, as well as the justification for why such capacity is needed for a Hadoop cluster.</strong>

##

There are several methods for calculating the number of machines needed to store data in a Hadoop cluster. The two main methods are as follows:

## Calculation Using a Formula Considering Compression and Reserved Space

This method uses the formula <code>H = c * r * S / (1 - i)</code>, where the variables have the following meanings:

| Variable  | Meaning                                                                                 |
|-----------|-----------------------------------------------------------------------------------------|
| H         | Total Hadoop cluster capacity required (in TB, for example)                             |
| c         | Data compression (value between 0 and 1, where 0.5 means the data size is halved)       |
| r         | Hadoop replication factor (default is 3)                                                |
| S         | Original data volume (without replication or compression)                               |
| i         | Percentage of space reserved in HDFS for maintenance and operation (usually 0.2 or 20%) |

Characteristics of This Method:
* Considers compression
* Considers space reserved in HDFS
* Less conservative
* Estimated lower cost
* Recommended for detailed technical design

## Direct Calculation Without Compression

This method is based on the raw data volume with replication. Its characteristics are:
* Does not consider compression
* Does not consider space reserved in HDFS
* More conservative
* Estimated higher cost
* Recommended for early phases or when there is uncertainty

Since this is an initial approximation for the design of a Hadoop cluster and data regarding compression or reserved HDFS space are unknown, this second method was used to calculate the necessary machines.

By multiplying the number of daily events and the size per event, we obtain the daily data volume:

| Source    | Daily Data Volume                                |
|-----------|--------------------------------------------------|
| Source 1  | 10,000 * 15 KB = 150,000 KB = 146.48 MB          |
| Source 2  | 120,000 * 300 Bytes = 36,000,000 B = 34.33 MB    |
| Source 3  | 150,000 * 100 KB = 15,000,000 KB = 14,648.44 MB  |
| Source 4  | 170,000 * 800 KB = 136,000,000 KB = 132,812.5 MB |
| Source 5  | 2,000 * 1500 KB = 3,000,000 KB = 2,929.69 MB     |
> **Note:** The data size conversion uses the binary system (where 1 MB equals 1,024 KB), which is common in technical environments and particularly in Hadoop.

Total daily = 146.48 MB + 34.33 MB + 14,648.44 MB + 132,812.5 MB + 2,929.69 MB = 150,571.44 MB = 147.7 GB/day

Since the approximate daily consumption is 147.7 GB, the annual consumption would be:
147.7 GB/day * 365 days = 53,900.5 GB = 53.9 TB/year

As Hadoop HDFS has a default replication factor of 3, meaning each data block is stored three times for fault tolerance, the total volume with replication will be three times the original. The actual volume with replication is 161.7 TB.

Each machine has 22 disks of 2 TB, so there is a total of 44 TB available per machine. However, not all the capacity can be allocated to data storage. Typically, part of the disk is reserved for the operating system, logs, and buffers. Assuming that 90% of the capacity is allocated for storage, the usable capacity per machine is 39.8 TB.

By dividing the total volume by the usable capacity per machine, we obtain the theoretical number of required machines: 161.7 TB / 39.6 TB ≈ 4.08

Since we must be conservative and round up to ensure the needs are met, the number of required machines is 5. These machines will be used for data storage, so they will be DataNodes. However, an additional machine is necessary as a NameNode, so the total number of machines required is 6 to ensure high availability.

## Conclusion
Hadoop not only stores the data, but it also processes it in a distributed and parallel manner. The replication factor ensures fault tolerance, but on the other hand, it triples the required storage. Additionally, extra space is needed for temporary files, intermediate results, logs, and other MapReduce processes. Having multiple machines allows scaling the processing in parallel, which is key for efficient analytics.

If we had used the first method to calculate, considering a compression factor of 0.5 (data is reduced by half), a replication factor of 3, and a reserved HDFS space of 0.2, only 3 machines would have been required. However, we need to be conservative in this initial design and opt to ensure the architecture. After the first year, this approach can be revisited and the cluster size re-evaluated.
