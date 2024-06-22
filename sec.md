Enumeration Phase: This is the primary phase, where a hacker gathers information. 

**nmap:** to get info about open ports on a machine. this part is first step in enumeration to scan open ports on a server.  
`nmap [OPTIONS] <ip>`

OPTIONS: 
- `-sV`: To get ports and the service name
- `-p-`: scan all ports
- `-sS`: Perform a TCP SYN scan.  
- `-O`: Attempt to determine the operating system of the target.

**Firewall:**
To list all rules, you can use: `sudo iptables -L -v -n`, iptables is common firewall utility on linux OSs

**FTP:**
To get a file from server: 
1. first connect to the ftp server by: `ftp <host_ip>`
2. the file can be brought by `get <fileName>`


**SMB (Server Message Block):**
To list all available shared on a server: `smbclient -L <host_ip>`
To connect to a share: `sbmclient <host_id> nameOfShare`
To bring a file from share: `get <fileName>`

**Redis:**
To connect to a redis server remotly: `redis-cli -h <host_ip>`
To get all info about the redis server **After connecting to the server:** `> info`

To select a database: `select <nr of db>`




