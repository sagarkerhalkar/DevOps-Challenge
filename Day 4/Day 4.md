# 🧰 Production Incident Runbook: Docker Service Triage
> Day 04 - 90DaysOfDevOps Challenge  
> A repeatable, step-by-step diagnostic sequence to assess system resource allocation, storage boundaries, and log history.

Target Service under inspection: `docker.service` (Docker Daemon)

---

1. Environment Basics
Verifying system architecture, kernel parameters, and distribution specs before executing modifications.

   Command 1: `uname -a`  
       Observation: Confirmed system is running Linux Kernel version 6.8.0-amd64. Architecture is x86_64; standard LTS build.
   Command 2: `cat /etc/os-release`  
       Observation: OS identified as Ubuntu 24.04 LTS. Package management and service layout follow standard systemd conventions.

---
2. Filesystem Sanity Check
Testing baseline disk write access and mount stability by staging local throwaway objects.

   Command 3: `mkdir /tmp/runbook-demo`  
       Observation: Directory created instantly. The `/tmp` directory mount is responsive and writable.
   Command 4: `cp /etc/hosts /tmp/runbook-demo/hosts-copy && ls -lh /tmp/runbook-demo`  
       Observation: File copy operation executed with zero I/O delay. Target file permissions correctly preserved as read-only for non-owners.

---
3. Snapshot: CPU & Memory
Analyzing active resource allocations to confirm the target daemon is operating within normal boundaries.

   Command 5: `ps -o pid,pcpu,pmem,comm -p $(pgrep dockerd)`  
       Observation: `dockerd` process running under PID 1204. CPU consumption is stable at 0.4%, and memory footprint is flat at 1.2%.
   Command 6: `free -h`  
       Observation: Out of 8.0 GiB total memory, 3.4 GiB is free, and 2.1 GiB is utilized as buffer/cache. No active swap pressure detected.

---

4. Snapshot: Disk & IO
Confirming storage overhead constraints and evaluating if log accumulations or container layers are nearing storage limits.

   Command 7: `df -h /`  
       Observation: Root filesystem (`/`) currently stands at 48% utilization with 22 GiB of available storage room remaining.
   Command 8: `sudo du -sh /var/log`  
       Observation: Total log footprint is sized at 340M. No runaway file logs or unbounded journal allocations found.

---

5. Snapshot: Network
Validating system socket exposures and confirming outbound routing channels are clear.

   Command 9: `sudo ss -tulpn | grep dockerd`  
       Observation: No output returned—confirms Docker is running exclusively over local Unix domain socket `/var/run/docker.sock` rather than exposed TCP endpoints.
   Command 10: `ping -c 3 8.8.8.8`  
       Observation: 0% packet loss across 3 ICMP iterations. Average latency clocked at 12ms, indicating clear external network egress paths.

---

6. Logs Reviewed
Auditing runtime logging sequences to pinpoint hidden warning markers or unhandled container daemon failures.

   Command 11: `sudo journalctl -u docker -n 50`  
       Observation: Scanned 50 records up to June 2026. Logs confirm clean container initialization loops; zero process panics or resource constraints reported.
   Command 12: `sudo tail -n 50 /var/log/syslog`  
       Observation: Standard kernel allocations and systemd maintenance cycles visible; no core process drops or filesystem anomalies noted.

---

 Quick Findings
The system is operating smoothly. Docker daemon resource usage is negligible, local disk space is well within safe thresholds, and network connectivity is highly responsive. No performance regressions or infrastructure blocks found.

---

If This Worsens (Next Steps)
Should the Docker service experience performance degradation, thread exhaustion, or unresponsiveness, execute the following actions sequentially:

1.  Isolate & Gracefully Restart: Execute a zero-downtime reload if possible, or trigger `sudo systemctl restart docker` to clear dead engine deadlocks.
2.  Increase Engine Log Verbosity: Modify `/etc/docker/daemon.json` to inject `"debug": true`, then issue a `sudo systemctl reload docker` to track exact API traffic errors.
3.  Attach System Trace Tools: Track underlying system calls in real-time by targeting the primary daemon thread via `sudo strace -p $(pgrep dockerd) -c` to locate exact blocking operations or I/O stalls.