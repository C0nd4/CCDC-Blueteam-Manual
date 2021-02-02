# CCDC Blueteam Manual

## Pre-Competition Methodology

The events that you will encounter in CCDC will be correlated to real paths attackers will take, but the competition is a bit gamified in order to present a challenge to both the blue team and red team. Many of the things you learn to defend systems in this competion will directly translate to real world applicable cybersecurity skills.

When initially given the blue team manual before the qualifier round, the number one thing to do is enumerate the attack surfaces of your new machines. Here are some good questions that you should have answered as a team long before the competition begins.

1. What operating systems are running? 
2. What services are running on these boxes? 
3. Are there any CVEs for these services? 
4. How do you change the default credentials for these services? 
5. What ports are required to stay open?

### If you can't build the environment, there is no way you can defend it!

Once you are told what the environment that you need to defend is going to look like, it is vital to replicate this environment as fast and as accurately as possible. The team should get practice with the services at hand and know how they operate, how they are configured, and how to make changes to them on the fly.

### Stop Chasing Ghosts!

Many people will get caught up with the idea that a red team could be in their systems and will panic at everything they see that they do not recognize. Often times the unknown files, processes, or user accounts are standard Linux or Windows features. Chasing these leads that turn out to be nothing malicous will only eat up time and will allow the red team to gain an advantage. **It is vital to understand the machine you are defending!** If you do not know what the baseline normal is for a machine, you cannot possibly identify an anomoly. This is a major reason for replicating the target environment before the competition. It would be wise to get a list of the common files, processes, and user accounts for the operating systems that you will be defending in order to minimize your time chasing ghosts.

## Initial Cleaning

As soon as you gain access to your new machines during the competition, every single person needs to have a solid plan of action. Often teams will have a plan for the first 15 minutes, then it falls apart when people get stuck or notice suspected red team activity. This is the responsibility of the team leader to ensure everyone is working together and as a single unit. **Your team will either succeed together or fail together.** Here are some common things to address immediately after gaining access to your systems.

### Linux

**Commands listed are not all encompassing and are for a baseline only!**

1. Change **all** user passwords. *Yes, especially the credentials given to you!*

    `# passwd username`
2. Remove all SSH keys present on the box.

    `# find / -name authorized_keys 2> /dev/null`

    `# find / -name id_rsa 2> /dev/null`
3. Audit sudo access given to users.

    `# cat /etc/sudoers`

    `# cat /etc/sudoers.d/*`

    `# getent group sudo | cut -d: -f4` (The *sudo* group is Debian, *wheel* is for RHEL)
4. Audit /etc/passwd to check for account shells and UIDs.

    `$ cat /etc/passwd | grep :0:`

    `$ cat /etc/passwd | grep -v /bin/false | grep -v /sbin/nologin`
5. Verify that there are not any non-standard cron jobs on the system.

    `# cat /etc/cron.d/*`

    `# for user in $(cut -f1 -d: /etc/passwd); do crontab -u $user -l; done`
6. Remove packages that could be used for malicious purposes if not needed.

    `# apt remove socat nc ncat nmap netcat` (*apt* is for Debian, *yum* is for RHEL). Other packages must be manually inspected in order to prevent taking down critical services.
7. Stop services that are not critical to the system or competition needs.

    `# systemctl --type=service --state=active`

    ` systemctl stop servicename`
8. Identify SUID and SGID files. Cross reference with https://gtfobins.github.io/ to narrow down malicous instances of SUID and SGID files.

    `# find / -perm -4000 -print 2>/dev/null` for SUID

    `# find / -perm -2000 -print 2>/dev/null` for SGID

9. Identify world writable files and directories.

    `# find / -type d \( -perm -g+w -or -perm -o+w \) -exec ls -adl {} \;` for directories

    `# find / ! -path "*/proc/*" -perm -2 -type f -print 2>/dev/null` for files

#### Additional Resources

- [Linux Privilege Escalation Guide](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md)
- [Linux Persistence Guide](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Persistence.md)

#### Useful Tools

- [LinEnum](https://github.com/rebootuser/LinEnum)
- [pspy](https://github.com/DominicBreuker/pspy)
