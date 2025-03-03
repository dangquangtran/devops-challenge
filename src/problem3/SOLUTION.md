Provide your solution here:

*Troubleshooting High Memory Usage on Ubuntu 24.04 VM Running NGINX Load Balancer
Step 1: Confirm the Memory Usage
First, validate the monitoring alert to ensure the memory usage issue is persistent.

Run:

free -h
This shows total, used, and available memory.

Check which processes are consuming the most memory:

ps aux --sort=-%mem | head -10
If nginx is consuming most of the memory, the issue likely stems from NGINX misconfiguration or excessive load.
Alternatively, use:
top
to check real-time memory consumption.

*Possible Root Causes & Resolutions
1. High Number of Open Connections
a.Cause
-The VM is handling a large number of concurrent connections, leading to excessive memory consumption.
-worker_connections is set too high in the NGINX configuration.
b.Impact
-Increased memory usage.
-Possible slow response times or failure to accept new connections.
c.Troubleshooting
-Check current open connections:

netstat -anp | grep ':80' | wc -l
or
ss -s

-Check NGINX connection limits:

grep -E 'worker_connections|worker_processes' /etc/nginx/nginx.conf

d.Recovery Steps
-Reduce worker connections
Edit /etc/nginx/nginx.conf:
worker_processes auto;
worker_connections 2048;

-Enable Keepalive connections
Modify /etc/nginx/nginx.conf:
keepalive_timeout 10;
keepalive_requests 1000;

-Restart NGINX
systemctl restart nginx


2. Memory Leak in NGINX or a Module
a.Cause
-A bug in NGINX or third-party modules (e.g., Lua, WAF, logging).
-Unreleased memory due to improper handling of worker processes.

b.Impact
-Memory usage keeps increasing until the system crashes or becomes unresponsive.

c.Troubleshooting
-Check if NGINX workers are consuming high memory:
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | grep nginx

-Check logs for errors:
cat /var/log/nginx/error.log | tail -20

-Check memory allocation patterns:
pmap -x $(pgrep nginx) | tail -20

d.Recovery Steps
-Restart NGINX and monitor memory
systemctl restart nginx

-Update NGINX to the latest stable version
apt update && apt upgrade nginx

-Disable unnecessary modules
Comment out unused modules in /etc/nginx/nginx.conf.

-Enable worker process automatic restarts
worker_rlimit_nofile 65535;

-Consider switching to a lighter alternative (e.g., HAProxy or Envoy) if the issue persists.


3. Excessive Logging Overloading Memory
a.Cause
-NGINX access/error logs are consuming too much memory due to high verbosity.
-Logs stored in memory (tmpfs).

b.Impact
-Increased disk writes and memory consumption.
-Slower performance.

c.Troubleshooting
-Check log file sizes:
du -sh /var/log/nginx/

-Check if logs are in memory:
df -h | grep tmpfs

d.Recovery Steps
-Rotate logs using logrotate
Edit /etc/logrotate.d/nginx:
/var/log/nginx/*.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
    sharedscripts
    postrotate
        systemctl reload nginx
    endscript
}

-Reduce log verbosity
access_log off;

-Move logs to persistent storage
mv /var/log/nginx /mnt/logs/nginx

-Restart NGINX
systemctl restart nginx


*Final Step: Monitor Post-Fix
After applying fixes, monitor the system:
free -h
top
nginx -t
If memory usage remains high, consider upgrading the VM or using a dedicated load balancer (AWS ALB, Nginx Plus, HAProxy).