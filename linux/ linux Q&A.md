1. How do you check the Linux kernel and operating system version?

The kernel version can be checked using uname -r, while OS and distribution details are found in /etc/os-release.
This information is important for troubleshooting compatibility and package issues.
2. How do you list all files including hidden files?

ls -la
Hidden files usually store application or user configuration and begin with a dot (.).

3. How do you view a large file efficiently?

Instead of opening the entire file, use:

less filename for paginated viewing
head or tail to view specific sections
This avoids performance issues with large logs.

4. A log file is updating continuously. How do you monitor it?

tail -f /var/log/syslog
This is commonly used in production environments to monitor live system or application logs.

5. How do you search for a specific word inside a file?

grep "error" filename
grep is widely used for identifying errors or patterns in log files.

6. How do you search for a word recursively in multiple files?

grep -Ri "error" /var/log/
This helps in system-wide troubleshooting when the error source is unknown.

7. How do you find a file by name in Linux?

Files can be located using:

find /path -name filename for real-time search
locate filename for faster, database-based search

8. How do you check disk usage on a Linux system?

df -h shows filesystem usage
du -sh directory shows directory size
These commands are essential for diagnosing disk space issues.

9. A filesystem is full. What steps do you follow to identify the cause?

Check disk usage using df -h
Identify large directories with du -sh *
Remove unnecessary files or rotate logs

10. How do you check memory usage?

free -h
This command displays total, used, and available memory in a human-readable format.

11. A server is slow. Which command do you run first and why?

top
It provides real-time visibility into CPU, memory usage, and resource-consuming processes.

12. How do you view all running processes?

ps aux
This command lists all processes along with their resource usage.

13. How do you terminate a process in Linux?

kill PID sends a graceful termination signal
kill -9 PID forcefully terminates the process
Force kill should be used only when necessary.

14. How do you change file permissions?

chmod 755 filename
This grants full access to the owner and read/execute permissions to group and others.

15. How do you change file ownership?

chown user:group filename
This is often required when correcting access-related issues.

16. A user gets a “Permission denied” error. What do you check?

This error usually occurs when the user does not have the required access rights or when security policies restrict the operation.

File permissions: Use ls -l to verify read, write, and execute permissions for the owner, group, and others.
File ownership: Check whether the file is owned by the correct user or group and correct it using chown if required.
SELinux or AppArmor policies: Even with correct permissions, mandatory access control may block access; verify enforcement status and logs to identify policy restrictions.

17. How do you create a new user in Linux?

useradd username
passwd username
User creation is a common administrative task.

18. A user cannot log in. What possible reasons do you check?

Account expiration or lock
Incorrect login shell
Password expiry

19. How do you check system uptime and load average?

uptime
Load averages help determine whether the system is under heavy load.

20. How do you schedule a task to run automatically at specific times in Linux?

Recurring tasks in Linux are scheduled using cron by editing the crontab file:

crontab -e
You define the execution time and the command or script, which is commonly used for backups, log cleanup, and maintenance jobs.

21. How do you schedule a script to run every day at 1 AM?

You add an entry in the crontab using:

crontab -e
0 1 * * * /path/script.sh
This is commonly used for automated backups and maintenance tasks.

22. How do you check the IP address and network interfaces on a Linux system?

Use the following command:

ip addr
It displays all network interfaces along with their assigned IP addresses and status.

23. How do you test basic network connectivity from a Linux server?

Use ping to verify network reachability to a host
Use curl to confirm that a service or application endpoint is responding
These commands help distinguish network issues from application issues.

24. A web service is not accessible. What do you verify first?

Check whether the service is running
Confirm the service is listening on the expected port
Verify firewall rules are not blocking traffic
This helps quickly isolate the root cause.

25. What is a symbolic link and why is it commonly used?

A symbolic link is a pointer to another file or directory.
It is commonly used to manage file paths, versioned directories, and application configurations without changing the actual files.

26. How do you create a symbolic link in Linux?

You create a symbolic link using:

ln -s source target
This allows multiple references to the same file or directory.

27. How do you view system and application logs in Linux?

Logs are typically located under /var/log and can be viewed using:

less for file-based logs
journalctl for systemd-based logs
Logs are essential for troubleshooting system issues.

28. How do you view logs for a specific service?

Use:

journalctl -u servicename
This command displays logs related only to the specified service, making debugging easier.

29. How do you check which ports are open and listening on a system?

Run:

ss -tulpn
This command shows active listening ports along with the associated services and process IDs.

30. A service is running but still not accessible. What checks do you perform?

Verify the service is listening on the correct port
Check firewall and SELinux/AppArmor rules
Validate service configuration files
These checks help identify access or security-related issues.

Mid-Level Linux Professionals (2–5 Years Experience)
After gaining hands-on experience, professionals move into mid-level roles involving automation, cloud platforms, and production system management. At this stage, Linux skills are combined with DevOps practices, cloud technologies, security, and monitoring tools.

Linux System Administrator (Mid-Level)
DevOps Engineer
Cloud Engineer (AWS / Azure / GCP)
Site Reliability Engineer (SRE)
Infrastructure / Operations Engineer
Below are the interview questions that will help you crack interviews for the above mid-level Linux professional roles.

31. How do you check why a service failed to start?

You should first check the service status and logs:

systemctl status servicename
journalctl -u servicename
These commands provide error messages and startup failure reasons.

32. How do you permanently mount a filesystem?

Persistent mounts are defined in /etc/fstab.
After adding the entry, always verify it using:
mount -a
This helps avoid boot-time failures.

33. What is LVM and why is it used in production?

LVM provides flexible disk management, allowing volumes to be resized without downtime.
It simplifies storage expansion, snapshots, and recovery compared to fixed partitions.

34. How do you extend a logical volume and filesystem?

Typical steps include:

Extend the volume using lvextend
Resize the filesystem using resize2fs (ext4) or xfs_growfs (XFS)
Most modern filesystems support online resizing.

35. A server disk usage suddenly increased. How do you investigate?

Identify the affected filesystem using df -h
Locate large files using du -sh * or ncdu
Check log growth and application dumps

36. How do you manage and control services in systemd?

Systemd services are managed using systemctl:

start / stop / restart services
enable or disable at boot
Systemd also supports dependency and restart policies.

37. How do you check which process is using a specific port?

ss -tulpn | grep PORT
This helps identify conflicts when services fail to start.

38. How do you configure a firewall on Linux?

Firewall configuration varies by Linux distribution:

Ubuntu uses ufw for simplified firewall management
RHEL/CentOS uses firewalld for zone-based firewall rules
Example:

ufw allow 22
firewall-cmd --add-service=http --permanent
Firewalls are used to restrict unauthorized access and protect services.

39. A service is running but users cannot access it. What do you check?

Port listening status
Firewall rules
SELinux enforcement
Application configuration

40. What is SELinux and how do you check its status?

SELinux enforces mandatory access control policies.
Check status using getenforce or sestatus.

41. How do you temporarily and permanently change kernel parameters?

Temporary: sysctl -w parameter=value
Permanent: add entry in /etc/sysctl.conf or /etc/sysctl.d/
Kernel tuning is common in performance optimization.

42. How do you monitor CPU, memory, and I/O performance?

Common tools include:

top / htop for live monitoring
vmstat, iostat, iotop for deeper analysis

43. How do you copy files securely between servers?

scp file user@server:/path
or

rsync -avz source/ user@server:/dest/
rsync is preferred for large or repeated transfers.

44. What is rsync and why is it better than scp?

rsync transfers only changed data and supports compression and resume.
This makes it faster and more efficient for backups and sync jobs.

45. How do you configure passwordless SSH access?

Generate keys using ssh-keygen
Copy the public key to the server using ssh-copy-id
This improves security and automation.

46. How do you check scheduled cron jobs for a user?

crontab -l
System-wide cron jobs are located in /etc/cron.* directories.

47. A cron job is not running. What do you verify?

Cron service status
Script permissions
Absolute paths inside the script
Cron logs

48. How do you rotate logs in Linux?

Log rotation is handled using logrotate.
It prevents logs from consuming excessive disk space.

49. How do you identify zombie processes?

Zombie processes appear with state Z in ps aux.
They indicate a parent process issue and do not consume resources.

50. How do you troubleshoot high load average?

Check CPU usage and runnable processes
Identify blocked I/O
Analyze memory pressure
Load average alone does not always mean high CPU usage.

51. How do you mount an NFS share?

Configure export on server
Mount on client using mount server:/share /mnt
NFS is commonly used for shared storage.

52. How do you synchronize system time on Linux?

Time synchronization is handled using chrony or systemd-timesyncd.
Accurate time is critical for logs and authentication.

53. What is swap and when should it be used?

Swap provides additional virtual memory when RAM is exhausted.
It helps prevent crashes but should not replace physical memory.

54. How do you add a swap file?

Steps include:

Create file using fallocate
Set permissions
Format with mkswap
Enable using swapon

55. How do you troubleshoot DNS resolution issues?

Check /etc/resolv.conf
Use dig or nslookup
Verify network and firewall rules

56. What is /proc filesystem used for?

/proc is a virtual filesystem that provides kernel and process information.
It is commonly used for monitoring and tuning.

57. How do you limit resource usage for a user or process?

Resource limits can be set using:

ulimit
/etc/security/limits.conf
systemd service limits

58. A system rebooted unexpectedly. How do you investigate?

Check previous boot logs using journalctl -b -1
Review system logs and hardware messages

59. How do you check which files are consuming the most space?

du -sh * | sort -h
This helps quickly locate disk space issues.

60. How do you ensure changes are not lost after reboot?

Persist configurations in proper config files
Enable services at boot
Avoid temporary runtime-only changes
Senior & Expert Linux Professionals (5+ Years Experience)
With extensive experience, Linux professionals transition into advanced, architect, or leadership roles. These positions focus on system design, scalability, security architecture, and leading technical teams or enterprise platforms.

Senior DevOps Engineer / DevOps Architect
Linux Systems Architect
Cloud Architect
Senior SRE / Reliability Architect
Platform Engineer / Infrastructure Architect
Below are the interview questions that will help you crack interviews for the above advanced Linux professional roles.

61. How do you design a highly available Linux-based application stack?

A highly available stack is designed by removing single points of failure at every layer.

This typically includes load balancers, multiple application nodes, replicated databases, health checks, and automated failover to ensure continuous service availability.

62. A production server suddenly becomes unreachable. What is your troubleshooting approach?

Firstly follow a layered approach, starting from the lowest level:

Verify network connectivity (ping, routing, firewall rules)
Check whether services are running and accessible
Inspect system resources (CPU, memory, disk)
Review recent configuration changes or deployments
This approach helps isolate the issue quickly without assumptions.

63. How do you analyze a high load average on a Linux system?

High load average can be caused by CPU saturation, blocked I/O, or stuck processes.
I always correlate load average with CPU usage, I/O wait, memory pressure, and process states before concluding the root cause.

64. How do you perform zero-downtime deployments on Linux servers?

Zero-downtime deployments are achieved by updating systems incrementally rather than all at once.
Common strategies include rolling deployments, blue-green setups, and traffic shifting through load balancers to ensure users are never impacted.

65. How do you investigate kernel panic issues?

Kernel panic investigation involves collecting and analyzing crash data:

Enable and collect dumps using kdump
Review kernel logs and dmesg
Analyze the crash dump using the crash utility
This helps identify hardware issues, driver bugs, or kernel-level failures.

66. How do you tune Linux for high network throughput?

Network tuning includes adjusting TCP buffers, connection limits, and kernel networking parameters.
All changes must be tested under realistic load conditions to ensure performance gains without introducing instability.

67. How do you manage Linux systems at scale?

At scale, manual configuration does not work.
Systems are managed using configuration management tools, Infrastructure as Code, and standardized base images to ensure consistency and reduce configuration drift.

68. How do you identify and fix configuration drift?

Configuration drift is detected by comparing the actual system state with the desired state defined in automation tools.
It is fixed and prevented through continuous enforcement using configuration management and immutable infrastructure practices.

69. A service works after restart but fails again later. How do you debug this?

This usually indicates a recurring underlying issue rather than a one-time failure:

Memory leaks or resource exhaustion
Unstable dependencies
Gradual performance degradation
Long-term monitoring and trend analysis are essential to identify the root cause.

70. Logs are growing rapidly and filling disk space. How do you fix this?

Firstly identify the source application and verify log rotation:

Ensure logrotate is configured
Reduce log verbosity if needed
Compress or archive old logs

71. A server suddenly becomes slow during peak hours. How do you troubleshoot?

Start by checking real-time resource usage using:

top
If CPU usage is normal, then check I/O wait and memory pressure using vmstat and iostat.
This helps determine whether the issue is CPU-bound, memory-bound, or disk-related.

72. Disk space on /var is 100% full. What steps do you take?

First, Identify what is consuming space:

df -h
cd /var && du -sh * | sort -h
I usually find logs or application dumps, then clean or rotate them safely without deleting critical files.

73. A service is running but users cannot access it. How do you debug?

Firstly verify in this order:

Whether the service is listening on the expected port
Firewall rules allowing the traffic
SELinux or AppArmor blocking access
Only after OS-level checks do I move to application logs.

74. A Linux server reboots automatically at night. How do you investigate?

I first check previous boot logs:

journalctl -b -1
Then I review system logs for OOM kills, kernel panics, or scheduled reboots to identify the root cause.

75. A process is consuming high CPU continuously. How do you handle it?

I identify the process using:

top
Then I check whether it is expected behavior.
If not, I investigate logs or dependencies before restarting or limiting the process.

76. A cron job is not running as expected. What do you check?

I verify:

Cron service status
Script permissions
Use of absolute paths inside the script
Cron logs for errors
Most failures are due to environment differences in cron.

77. SSH login suddenly stops working for users. How do you troubleshoot?

I check:

SSH service status
Firewall rules on port 22
Authentication logs (/var/log/auth.log or journalctl)
Disk space (SSH can fail if disk is full)

78. A filesystem needs to be expanded without downtime. What do you do?

If LVM is used, I extend the logical volume and then resize the filesystem online.
This allows expansion without unmounting or impacting running services.

79. A service crashes after running for several hours. How is this issue debugged?

This behavior often points to memory leaks, resource exhaustion, or unstable dependencies.
The issue is analyzed by monitoring memory and resource usage over time and reviewing service logs instead of repeatedly restarting the service.

80. A system shows a high load average but low CPU usage. What does this indicate?

This typically indicates I/O wait or processes blocked on disk or network operations.
Disk and process monitoring tools are used to confirm whether the load is caused by waiting tasks rather than CPU saturation.

81. How do you troubleshoot performance issues without restarting services?

Live troubleshooting involves analyzing processes, memory usage, I/O, and thread behavior using profiling and tracing tools.
Restarting services is treated as a last resort after diagnosis.

82. How do you handle database-related outages from the OS perspective?

From the OS side, I verify:

Disk and I/O health
Memory pressure and swapping
Network connectivity and latency
Once OS health is confirmed, application and database teams are involved.

83. How do you design Linux systems for scalability?

Scalable systems are designed to be stateless where possible.
They scale horizontally, use automation for provisioning, and rely on load balancing rather than vertical growth.

84. How do you validate system changes before production rollout?

Changes are validated through:

Staging and pre-production environments
Automated tests
Peer reviews and controlled rollout processes
This minimizes production risk.

85. How do you reduce blast radius during failures?

Blast radius is reduced by isolating services, deploying changes progressively, and containing failures to the smallest possible scope.
This prevents localized issues from becoming system-wide outages.

86. How do you manage on-call responsibilities effectively?

Effective on-call management includes clear escalation paths, well-documented runbooks, and post-incident reviews.
The goal is fast resolution and continuous improvement, not burnout.

87. How do you investigate suspected security breaches?

Security incidents are handled by isolating affected systems, analyzing logs and forensic data, rotating credentials, and patching vulnerabilities.
Systems are often rebuilt rather than patched in place.

88. How do you balance performance, cost, and reliability?

This balance is achieved through right-sizing resources, autoscaling where possible, and continuous monitoring.
Decisions are driven by data rather than assumptions.

89. How do you mentor junior Linux engineers?

Mentorship includes reviewing configurations, sharing operational knowledge, and encouraging automation and ownership.
The goal is to build independent and reliable engineers.

90. How do you stay updated with Linux and infrastructure changes?

I stay updated by reading release notes, following upstream communities, and continuously experimenting in lab or non-production environments.
Hands-on learning is critical to staying relevant.