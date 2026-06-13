# Sysctl Kernel Parameter Optimization

## Level 1 Tuning

Level 1 `sysctl` tuning means deliberate, targeted adjustments to kernel runtime parameters to improve system performance.

Kernel defaults are deliberately conservative and generic — chosen to work reasonably across a wide range of hardware and workloads, not to be optimal for any specific one. Tuning these values to better fit your actual workload yields real improvements. For example, raising TCP socket buffer ceilings directly reduces web page response time by allowing the TCP stack to hold more data in-flight per connection.

The right choices depend on the purpose of the machine. A desktop optimizes for the experience of one interactive user. A server optimizes for throughput and correctness across many concurrent connections. The same parameter can warrant opposite values in each context.

Rules for this level:

- Change a small number of parameters with clear, well-understood scope.
- Prefer reversible changes that can be tested before making persistent.
- Verify the effect is real before keeping the change.

Level 1 tuning is not limited to memory and networking. Depending on the system and workload, it can reasonably include:

- virtual memory reclaim and swap policy
- filesystem cache retention
- socket buffer sizing
- TCP connection lifecycle behavior
- filesystem limits and file handle counts
- scheduler hints
- security-adjacent defaults such as core dump behavior or ptrace scope

## How sysctl Works

`sysctl` exposes runtime kernel parameters through a virtual filesystem mounted at `/proc/sys/`. Parameters are organized into subsystem namespaces:

| Namespace | Subsystem |
|---|---|
| `vm.*` | Virtual memory management, swap, reclaim, OOM policy |
| `net.core.*` | Core network stack, socket buffers, packet queuing |
| `net.ipv4.*` | IPv4 stack, TCP and UDP behavior, routing |
| `net.ipv6.*` | IPv6 stack configuration |
| `net.netfilter.*` | Connection tracking, netfilter state |
| `fs.*` | Filesystem limits, inotify, file handles, pipe sizes, cache |
| `kernel.*` | Core kernel behavior, scheduler, IPC, modules, security |
| `dev.*` | Device-specific parameters |
| `user.*` | Per-user namespace limits |
| `debug.*` | Kernel debug and tracing controls |
| `abi.*` | Binary compatibility settings |

### The /proc/sys/ Interface

Everything in Linux is a file. Kernel parameters are no different.

`/proc/sys/` is a virtual filesystem the kernel maintains in memory. Each parameter is exposed as a readable and writable file. The dot-separated `sysctl` key maps directly to a path:

```
vm.swappiness               → /proc/sys/vm/swappiness
net.core.rmem_max           → /proc/sys/net/core/rmem_max
net.ipv4.tcp_fin_timeout    → /proc/sys/net/ipv4/tcp_fin_timeout
```

Reading or writing a parameter is reading or writing that file:

```bash
cat /proc/sys/vm/swappiness
echo 10 > /proc/sys/vm/swappiness
```

The `sysctl` command is a convenience wrapper over these same file operations. When you run `sysctl -w vm.swappiness=10`, the kernel receives the new value from the file write and applies it immediately.

### Runtime Changes

`sysctl -w` writes a value to the corresponding `/proc/sys/` file. The kernel applies it immediately with no restart needed. The change is lost on reboot.

```bash
sudo sysctl -w vm.swappiness=10
```

This is equivalent to:

```bash
echo 10 | sudo tee /proc/sys/vm/swappiness
```

### Persistent Configuration

To survive reboot, write settings to a file in `/etc/sysctl.d/`. The naming convention for user files is `99-<label>.conf` so they load last and take precedence over system-shipped defaults.

File format — one parameter per line, comments with `#`:

```conf
# /etc/sysctl.d/99-local-tuning.conf
vm.swappiness = 10
```

Load without rebooting:

```bash
sudo sysctl -p /etc/sysctl.d/99-local-tuning.conf
```

### Load Order and Precedence

When `sysctl --system` or the init system loads all configuration, files are processed in this order:

```
/etc/sysctl.d/*.conf
/run/sysctl.d/*.conf
/usr/local/lib/sysctl.d/*.conf
/usr/lib/sysctl.d/*.conf
/lib/sysctl.d/*.conf
/etc/sysctl.conf               ← loaded last, highest precedence
```

Within each directory, files are sorted lexicographically. A file with the same name in a higher-priority directory replaces the one below it entirely.

## Level 1 Parameters

There are far more parameters available across all subsystems than any single tuning profile covers. The ones below are a practical starting point — selected because each has a narrow, well-defined effect on one subsystem, the impact of changing them is predictable and measurable, and they cover the areas most likely to produce noticeable results on a general-purpose system: memory reclaim behavior, filesystem metadata caching, and network throughput.

Stock defaults before any tuning:

```conf
vm.swappiness = 60
vm.vfs_cache_pressure = 100
net.core.rmem_default = 212992
net.core.wmem_default = 212992
net.core.rmem_max = 212992
net.core.wmem_max = 212992
net.ipv4.tcp_slow_start_after_idle = 1
net.ipv4.tcp_fin_timeout = 60
net.ipv4.tcp_keepalive_time = 7200
net.ipv4.tcp_keepalive_intvl = 75
net.ipv4.tcp_keepalive_probes = 9
```

Each entry below describes what the parameter controls and how changing its value affects kernel behavior. The defaults are not wrong — they are conservative choices for generic hardware. The entries explain when and why moving away from them makes sense.

### Memory Management

#### vm.swappiness

Controls how aggressively the kernel prefers reclaim strategies involving swap over reclaiming page cache.

- Lower values reduce eagerness to push anonymous memory (application data) into swap.
- Higher values treat swap as a more acceptable reclaim target alongside page cache.

Lowering this on systems with sufficient RAM keeps active application memory in physical RAM longer, reducing swap-related latency.

#### vm.vfs_cache_pressure

Controls reclaim pressure on the inode and dentry cache — filesystem metadata held in memory.

- Lower values preserve the VFS cache longer under memory pressure.
- Higher values reclaim it more readily for other uses.

Lowering this benefits workloads that repeatedly access many files or directories, as path lookups and metadata operations hit the in-memory cache more often instead of going to disk.

### Network Socket Buffers

#### net.core.rmem_default / net.core.wmem_default

Sets the default receive and send socket buffer sizes for connections that do not request a specific size.

Increasing these raises the starting point for all sockets, meaning more data can be buffered per connection without applications needing to explicitly request larger buffers.

#### net.core.rmem_max / net.core.wmem_max

Sets the maximum permitted receive and send socket buffer sizes. Applications and kernel autotuning cannot allocate beyond this ceiling.

A higher ceiling lets the TCP stack fully exploit autotuning on high-throughput or high-latency connections. Raising this is a direct lever on web browsing performance: larger buffers in flight mean more data per round trip, which reduces the number of round trips needed to deliver a page.

### TCP Behavior

#### net.ipv4.tcp_slow_start_after_idle

Controls whether TCP falls back to slow start congestion control after a connection has been idle.

- `1`: slow start re-applies after idle
- `0`: reuse the previous congestion window after idle

Disabling this improves performance on connections that go briefly idle between requests, such as interactive HTTP sessions or polling connections.

#### net.ipv4.tcp_fin_timeout

How long a socket in FIN-WAIT-2 stays open before being forcibly closed.

Lowering this cleans up connections that did not complete their close handshake faster, reducing accumulation of lingering half-closed sockets.

#### net.ipv4.tcp_keepalive_time

Idle time on an established connection before the kernel begins sending keepalive probes to test whether the peer is still alive.

Lowering this allows dead connections to be detected and closed sooner, freeing resources.

#### net.ipv4.tcp_keepalive_intvl

Interval between keepalive probes when the peer has not responded.

#### net.ipv4.tcp_keepalive_probes

Number of unanswered probes before the connection is declared dead and closed.

## Example Profile

One Level 1 profile for a desktop or laptop system:

```conf
vm.swappiness = 10
vm.vfs_cache_pressure = 50
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.core.rmem_default = 1048576
net.core.wmem_default = 1048576
net.ipv4.tcp_slow_start_after_idle = 0
net.ipv4.tcp_fin_timeout = 15
net.ipv4.tcp_keepalive_time = 600
net.ipv4.tcp_keepalive_intvl = 30
net.ipv4.tcp_keepalive_probes = 5
```

Expected: web pages load faster, applications stay in RAM longer under normal use, and stale connections are cleaned up sooner.

## Apply

```bash
sudo sysctl -p /etc/sysctl.d/99-local-tuning.conf
```

Or reload all system configuration:

```bash
sudo sysctl --system
```

## Verify

```bash
sysctl vm.swappiness vm.vfs_cache_pressure
sysctl net.core.rmem_default net.core.wmem_default net.core.rmem_max net.core.wmem_max
sysctl net.ipv4.tcp_slow_start_after_idle net.ipv4.tcp_fin_timeout
sysctl net.ipv4.tcp_keepalive_time net.ipv4.tcp_keepalive_intvl net.ipv4.tcp_keepalive_probes
```

Or read directly from `/proc/sys/`:

```bash
cat /proc/sys/vm/swappiness
cat /proc/sys/net/core/rmem_max
```

## Revert

Remove or empty the config file and reload:

```bash
sudo sysctl --system
```

Or restore individual values:

```bash
sudo sysctl -w vm.swappiness=60
sudo sysctl -w net.core.rmem_max=212992
```
