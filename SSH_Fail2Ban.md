  This Fail2ban configuration protects the SSH service from brute-force login attempts by monitoring authentication logs and automatically banning IP addresses that exceed a defined number of failed login attempts. Providing an additional layer of security on top of SSH's built-in protections.

# Setup
1. Install Fail2ban package `fail2ban`
2. Enable the service: `systemctl enable failpro2ban.service`
3. Configuration files are in `/etc/fail2ban`
    - Most familiar is `jail.conf`
    - In most cases you should’n edit the `jail.conf` file, so you create `jail.local` file and set your configurations there

# Configuration
1. First you are going to set the jail disabled, so that you test a brute-force attack to the server, and see the difference when enabling the jail for ssh.
2. Create `/etc/jail.local` file
```
[sshd]
enabled = false
port = ssh
filter = sshd
logpath = /var/log/auth.log           # This is for Debain based systems. For RedHat & Arch the ssh log file is /var/log/audit/audit.log
maxretry = 3 ( max attempts )
bantime = 3600 ( 1h )
ignoreip = ( white list )
```

3. After writing any changes to the jail configuration file you need to restart the service & check the status of the service (if not active there is an issue of the syntax of the configuration file).

# Operations
1. When the jail is disabled, you can try how many attempts you want on ssh login (by default you have 3 false attempts before you should relogin)
	- When the jail is disabled, even you fail login after the 3 first attempt, you can simply try to do it again.
2. Enable the jail on `jail.local` file and try false attempts again or use hydra to brute-force it
	- `hydra -l root -P /path/to/wordlist ssh://<Server_IP> -I -v`
	- Connection refused, and the IP is banned for 1h
3. Unban the IP: `fail2ban-client set sshd unbanip <Cliet_IP>`

# Queries
1. Check how many jails are configured (in your case only sshd): `fail2ban-client status`
2. Check the logs for ssh service: `fail2ban-client status sshd`
	- Total failed
	- Currently banned
	- Total banned
	- Banned IP list …
