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
This section provides examples of simple `fio` commands to get started with benchmarking different storage systems.

### HDD Benchmarking
HDDs are optimized for sequential workloads. Use the following commands to test sequential read and write performance:

**Sequential Write Test:**
```bash
fio --name=seq_write --rw=write --bs=1M --size=1G --numjobs=1 --iodepth=1 --filename=/path/to/testfile
```

**Sequential Read Test:**
```bash
fio --name=seq_read --rw=read --bs=1M --size=1G --numjobs=1 --iodepth=1 --filename=/path/to/testfile
```

### SSD Benchmarking
SSDs excel at random I/O and parallelism. Use these commands to test random read and write performance:

**Random Write Test:**
```bash
fio --name=rand_write --rw=randwrite --bs=4k --size=1G --numjobs=4 --iodepth=32 --filename=/path/to/testfile
```

**Random Read Test:**
```bash
fio --name=rand_read --rw=randread --bs=4k --size=1G --numjobs=4 --iodepth=32 --filename=/path/to/testfile
```

### Remote Server Benchmarking (NAS/SFTP)
For remote storage, you can mount the filesystem locally or use `fio`'s network capabilities.

**Testing NAS (NFS/SMB):**
1. Mount the NAS share:
   ```bash
   sudo mount -t nfs <nas_ip>:/share /mnt/nas
   ```
2. Run `fio` on the mounted directory:
   ```bash
   fio --name=nas_test --rw=write --bs=1M --size=1G --numjobs=1 --iodepth=1 --filename=/mnt/nas/testfile
   ```

**Testing SFTP:**
1. Mount the SFTP server using `sshfs`:
   ```bash
   sshfs user@remote:/path/to/dir /mnt/sftp
   ```
2. Run `fio` on the mounted directory:
   ```bash
   fio --name=sftp_test --rw=randread --bs=4k --size=1G --numjobs=4 --iodepth=32 --filename=/mnt/sftp/testfile
   ```

These examples provide a starting point for benchmarking HDDs, SSDs, and remote servers. Adjust the parameters as needed to suit your specific use case.

## Advanced Configuration
This section covers advanced configurations in `fio` to simulate complex workloads and fine-tune performance tests.

### Multi-Job Workloads
Simulate multiple concurrent workloads to test how the storage system handles parallelism.

**Example:**
```bash
fio --name=multi_job --rw=randrw --rwmixread=70 --bs=4k --size=1G --numjobs=8 --iodepth=16 --filename=/path/to/testfile
```
- `--numjobs=8`: Runs 8 parallel jobs.
- `--rwmixread=70`: 70% reads and 30% writes.
- Useful for testing SSDs or high-performance storage systems.

### I/O Engines
Choose different I/O engines to simulate various access methods.

**Common I/O Engines:**
- `sync`: Synchronous I/O.
- `libaio`: Linux asynchronous I/O (default for most advanced tests).
- `mmap`: Memory-mapped I/O.
- `posixaio`: POSIX asynchronous I/O.
- `net`: Network-based I/O.

**Example:**
```bash
fio --name=io_engine_test --rw=randread --bs=4k --size=1G --iodepth=32 --ioengine=libaio --filename=/path/to/testfile
```

### Mixed Workloads
Simulate real-world scenarios with a mix of reads and writes.

**Example:**
```bash
fio --name=mixed_workload --rw=randrw --rwmixread=60 --bs=8k --size=2G --numjobs=4 --iodepth=16 --filename=/path/to/testfile
```
- `--rw=randrw`: Random read/write workload.
- `--rwmixread=60`: 60% reads and 40% writes.

### Variable Block Sizes
Test performance across a range of block sizes to understand how the storage system handles different workloads.

**Example:**
```bash
fio --name=variable_bs --rw=randread --bsrange=4k-128k --size=1G --iodepth=16 --filename=/path/to/testfile
```
- `--bsrange=4k-128k`: Randomly selects block sizes between 4KB and 128KB.

### Disk Spanning
Test multiple disks or partitions simultaneously.

**Example:**
```bash
fio --name=disk_span --rw=randwrite --bs=4k --size=1G --iodepth=16 --filename=/dev/sda:/dev/sdb:/dev/sdc
```
- `--filename=/dev/sda:/dev/sdb:/dev/sdc`: Tests multiple devices.

### Steady-State Testing
Simulate long-running workloads to analyze steady-state performance.

**Example:**
```bash
fio --name=steady_state --rw=randwrite --bs=4k --size=10G --runtime=3600 --time_based --filename=/path/to/testfile
```
- `--runtime=3600`: Runs the test for 1 hour.
- `--time_based`: Ensures the test runs for the specified duration.

These configurations allow you to tailor `fio` tests to specific scenarios, making it a powerful tool for analyzing storage performance.

## Interpreting Results
`fio` produces detailed output that includes key metrics to evaluate storage performance. Here's how to interpret the results:

- **Bandwidth (BW)**: The amount of data read or written per second, typically measured in MB/s or GB/s. This is crucial for understanding throughput.
- **IOPS (Input/Output Operations Per Second)**: The number of I/O operations completed per second. Higher IOPS indicate better performance for random workloads.
- **Latency**: The time taken for each I/O operation, reported as average, minimum, maximum, and percentiles. Low latency is critical for responsiveness.
- **Percentiles**: Percentile metrics (e.g., 99th percentile) show the worst-case latency for a given percentage of operations, useful for identifying outliers.

### Example Output:
```plaintext
READ: bw=150MiB/s (157MB/s), IOPS=38400, runt= 10000msec
   clat (nsec): min=2000, max=50000, avg=12000, stdev=3000
   lat (nsec): min=2500, max=52000, avg=12500, stdev=3200
```
- **bw**: Bandwidth.
- **IOPS**: Input/Output operations per second.
- **clat**: Completion latency.
- **lat**: Total latency (includes submission and completion).

Use these metrics to compare performance across different configurations or storage systems.

## Troubleshooting
- **Permission Denied**:
  - Ensure you have write permissions for the test file or directory.
  - Use `sudo` if testing raw devices (e.g., `/dev/sda`).

- **Insufficient Disk Space**:
  - Reduce the `--size` parameter to fit within available space.
  - Clean up old test files before running new benchmarks.

- **High Variability in Results**:
  - Ensure the system is idle during testing to avoid interference from other processes.
  - Use `--direct=1` to bypass the OS cache for consistent results.

- **fio Command Not Found**:
  - Verify `fio` is installed using `fio --version`.
  - Install it via your package manager (e.g., `sudo apt install fio` on Ubuntu).

- **Errors with Remote Testing**:
  - Check network connectivity and ensure the remote server is accessible.
  - For `sshfs`, verify the mount point and credentials.

## References
Here are some useful links and resources for further learning about `fio`:

- [fio GitHub Repository](https://github.com/axboe/fio): The official source code and documentation for `fio`.
- [fio Manual Page](https://fio.readthedocs.io/): Comprehensive documentation on `fio`'s options and usage.
- [fio Examples](https://github.com/axboe/fio/tree/master/examples): A collection of example job files and configurations.
- [fio Benchmarks](https://www.phoronix.com/scan.php?page=search&q=fio): Benchmark results and comparisons using `fio`.
