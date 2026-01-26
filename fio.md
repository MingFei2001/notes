# Fio - Flexible I/O Tester
Fio is a versatile storage benchmarking tool for measuring latency and throughput across common workloads — SSDs/HDDs, file backups/copies, media editing, small VMs, and NAS/file-server scenarios.

## Installation
Install `fio` from your distribution's package repository. E.g., On Ubuntu:

```bash
# Update the repo and install fio
sudo apt update && sudo apt install fio

# Verify the installation by running a version check
fio --version
```

## Understanding the workflow

~~~{.mermaid format=svg}
graph LR;
    A[Define Benchmarking Objective] --> B[Understand Storage System]
    B --> C[Configure I/O Settings]
    C --> D[Configure Test Options]
    D --> E[Run Test]
    E --> F[Verify & Analyze Results]
~~~

### Define the Benchmarking Objective
The first step is to clearly define what you want to measure. This will determine the type of workload and options to use.  
- **Throughput**: Measure the maximum data transfer rate (e.g., MB/s).
- **IOPS**: Measure the number of I/O operations per second.
- **Latency**: Measure the responsiveness of the storage system.
- **Mixed Workloads**: Simulate real-world scenarios with a mix of reads and writes.

### Understand the Storage System
Different storage systems (HDD, SSD, NAS, etc.) have unique characteristics that influence the choice of options.  
- **HDDs**: Optimized for sequential workloads, low IOPS, high latency.
- **SSDs**: Optimized for random workloads, high IOPS, low latency.
- **Remote Storage (NAS/SFTP)**: Network latency and bandwidth are critical factors.

### Choose the I/O Pattern (`--rw`)
The I/O pattern determines how data is read or written.  
- **Sequential Workloads**: Use `--rw=read` or `--rw=write` for sequential reads/writes.
- **Random Workloads**: Use `--rw=randread` or `--rw=randwrite` for random reads/writes.
- **Mixed Workloads**: Use `--rw=randrw` for a mix of random reads and writes. Add `--rwmixread=<percentage>` to control the read/write ratio.

### Set the Block Size (`--bs`)
The block size determines the size of each I/O operation.  
- **Small Block Sizes (e.g., 4k)**: Simulates workloads like databases or small file operations. Common for SSDs and random I/O tests.
- **Large Block Sizes (e.g., 1M)**: Simulates workloads like backups or media streaming. Common for HDDs and sequential I/O tests.

### Configure Queue Depth (`--iodepth`)
Queue depth determines how many I/O operations are queued at once.  
- **Low Queue Depth (e.g., `--iodepth=1`)**: Simulates single-threaded workloads. Suitable for HDDs or low-latency scenarios.
- **High Queue Depth (e.g., `--iodepth=32`)**: Simulates multi-threaded workloads. Suitable for SSDs or high-performance systems.

### Set Parallelism (`--numjobs`)
The number of jobs determines how many threads or processes are running simultaneously.  
- **Single Job (e.g., `--numjobs=1`)**: Simulates a single application accessing the storage.
- **Multiple Jobs (e.g., `--numjobs=4`)**: Simulates multiple applications or threads accessing the storage.

### Enable Direct I/O (`--direct`)
Direct I/O bypasses the OS cache, providing a more realistic view of raw storage performance.  
- **Enable Direct I/O (`--direct=1`)**: Use for most benchmarks to avoid interference from the OS cache.
- **Disable Direct I/O (`--direct=0`)**: Use when you want to measure the combined performance of storage and caching.

### Set the Test Duration (`--runtime`)
The runtime determines how long the test will run.  
- **Short Tests (e.g., `--runtime=30`)**: Useful for quick benchmarks or initial testing.
- **Long Tests (e.g., `--runtime=300`)**: Useful for more accurate results, especially for SSDs with write caching.

### Add Verification (`--verify`)
Verification ensures data integrity during the test.  
- **Enable Verification (`--verify=md5`)**: Use when testing critical systems to ensure data is written and read correctly.
- **Disable Verification**: Skip for performance-only benchmarks.

### Interpret the Results
After running the test, analyze the output to understand the performance metrics:  
- **Bandwidth (BW)**: Indicates the data transfer rate.
- **IOPS**: Indicates the number of I/O operations per second.
- **Latency**: Indicates the responsiveness of the storage system.

This professional workflow ensures that you choose the right options for your specific use case and storage system.

## Basic Usage
Examples of simple fio commands to get started with benchmarking

- HDD
- SSD
- Remote server (NAS/SFTP)

## Advanced Configuration
Details on configuring fio for complex workloads, including multi-job setups, I/O engines, and runtime options.

## Interpreting Results
Guidelines for understanding fio's output, including human-readable and machine-parsable formats.

## Troubleshooting
Common issues and solutions when working with fio.

## References
Links to official documentation, man pages, and other resources for further learning.
or RTFM but gently
