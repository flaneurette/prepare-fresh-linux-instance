## Howto install and prepare a Linux Ubuntu server instance with Zesle.

Follow the minimal steps below to prepare your instance, install Zesle and harden it's security. SSH into the instance with an account that has root level privileges.

Update system.
```
apt-get update
```
Upgrade system (optional, risky)
```
apt-get -y upgrade && apt-get -y full-upgrade
```
Install programs.
```
apt-get install curl - required for Zesle.
apt-get install nmap
apt-get install htop
apt-get install dig
apt-get install ntp
```
Set timezone. (optional)
```
timedatectl set-ntp on
timedatectl set-timezone UTC
```
Install Zesle.
```
cd /home && sudo curl -o latest -L http://zeslecp.com/release/latest && sudo sh latest
```
Run update.
```
apt-get update
```
Install firewall.
```
apt-get install ufw
ufw status numbered
ufw allow 22
ufw allow 80
ufw allow 443
ufw allow 2078 (Zesle port)
ufw enable
```
In Zesle, first create your SSH keys in the admin panel. Follow the Zesle tutorial on how to do that (very easy). 

If you done this, then proceed, disabling password login to the server:
```
nano /etc/ssh/sshd_config
PasswordAuthentication no
systemctl reload sshd
```
More security hardening. (optional)
```
nano /etc/fstab tmpfs /run/shm tmpfs defaults,noexec,nosuid 0 0
```
Sample config for hardening (optional)
```
nano /etc/sysctl.conf

#IP Spoofing protection
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1

#Ignore ICMP broadcast requests
net.ipv4.icmp_echo_ignore_broadcasts = 1

#Disable source packet routing
net.ipv4.conf.all.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0 
net.ipv4.conf.default.accept_source_route = 0
net.ipv6.conf.default.accept_source_route = 0

#Ignore send redirects
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0

#Block SYN attacks
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 2048
net.ipv4.tcp_synack_retries = 2
net.ipv4.tcp_syn_retries = 5

#Log Martians
net.ipv4.conf.all.log_martians = 1
net.ipv4.icmp_ignore_bogus_error_responses = 1

#Ignore ICMP redirects
net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0 
net.ipv6.conf.default.accept_redirects = 0

#Ignore Directed pings
net.ipv4.icmp_echo_ignore_all = 1
```

Run update.
```
apt-get update
```
Do nmap scan.
```
nmap -A -v IP
```
See last 100 lines of journal.
```
journalctl -n 100
```
OK? then clean and exit.
```
history -c
```
Proceed logging into your Zesle account and manage the server from there.
