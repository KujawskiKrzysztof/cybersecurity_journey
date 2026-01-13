# Linux Basics

```bash
touch # make file

#displays current directory content command
ls
ls -l #with nodes, rights, etc.
ls -a #all files including hidden

cd #changes the shell network directory
cd ..
cd .
cd /

cp [file name] #copy file, eg. "cp /etc/passwd . "- copy passwd from etc/ to current location;
rm [file name] #deletes file; 
rm -r [directory name/] #deletes directories
mv [/location/file name] [/new location/] #move file and change filename
mv [/location/file name] [/new location/new file name] #move file and change filename
cat [file name] #displays file content
cat [file name] | grep [filter] #displays only filtered results
less [file name] #displays file content enabling scrolling
less [file name] | grep [filter] #displays only filtered results
tail [file name] #dispalys end file
tail - n 2 [file name] #displays last 2 lines
tail -f [filename] #displays current view; possible to use "| grep" for filtering
echo ["string"] > [file name] #add/replace all current content to file
echo ["string"] > [file name] #add content to the end of file
df #shows information about the file system on wich each file resides, or all file systems by default
pwd #displays current location
su #permits to log as another user without logout current user; "su" : log as root and stay in current location; "su -" : log as root and loads root's env var
```

```bash
whoami #shows current user
clear #makes screen clear
apt-get #only root has access
apt-get install #downloading and installing aplications
apt-get update #checking and updating all aplications
usermod #(only root has access) - permition/rights for user
usermod -a -G sudo [user name] #set user as a sudoer
wget [link] #downloading files, apps, websites
sudo dpkg -i [file name] #decompressing and installing
ip a #displays ethernet configuration
ifdown [net interface eg. enp0s3] #turn off ethernetv
ifup [net interface eg. enp0s3] #turn on ethernet
mkdir #makes directory 
mkdir -p #makes directories one by one eg. "mkdir - p 1/2/3/4/"
ln #makes hard symbolic link
ln -s #makes soft symbolic link
man [command name] #displays manual to command eg. "man ls"
```

```bash
#directories
/bin & /sbin #contain softwares that are used to start system
/boot #contains inf's necessary to start sytem
/dev #contains all devices: discs, screen, mouse, keyboard
/etc #contains system and services setup
/home #contains all sytem users home directories (do not include root)
/root #root (admin) home directory
/proc #in virtual file system are making files contains infs about system processes
/var #contains system logs and often websites placed &
~ #home directory for each user
```

```bash
#rights:
chmod [digit][digit][digit] [file name] #changes writes to [file name]
chmod [a]/[o] [file name] #
ls -la [file name] #shows rights, owner, group, made date & file name :
#eg.	-rwxrwxrwx	-	- 1st sign "-" - file, "d" - directory & "l" - symlink 
# 1. 2nd, 3rd & 4th sign - "u" user/file owner rights "r" - read, "w" - write, "x" - execute
# 2. 5th, 6th & 7th sign - "g" group rights "r" - read, "w" - write, "x" - execute
# 3. 8th, 9th & 10th sign - "o" others rights "r" - read, "w" - write, "x" - execute
# 4. "4" - read, "2" - write, "1" - execute, "3" - write & execute, "5" - read & execute, "6" - read & write, "7" - read, write & execute
# 5. "t" - sticky bit in system directories with tmp files
# 6. "s" - sticky bit - set group id: all files inherit rights from directory
```

```bash
Shorcuts:
Ctrl + A #go to entry of the line
!! #repeating last command
Ctrl + L #cleaning screen
; #to execute 2 commands, one after one, second command will execute or return error if first doesn't execute
&& #to execute 2 commands, one after one, second command won't execute if first doesn't
cd - #back to last position
cd #back to home directory
alias #displays actual aliases; "alias '[alias name]=[command name + command function]'" - creates new alias eg. alias 'll=ls -l'
screen #software to launch many screens and work remotly
.bashrc #in home directory - here are more setup bash system shell eg. stored aliases, it is possible to write more aliases or do work with sytem more comfortable
```

```bash
#Searching:
find #searching files
find [directory name] -name "a*" #in directory [directory name] searches files starting from a
find [directory name] -maxdepth 1 -name "a*" #in directory [directory name] searches files starting from a
#parameter [-name "a*"] handle searching files starting with small a
#parameter [-iname "a*"] handle searching files starting with a or A
#parameter [-name "agh*"] handle searching files starting with small a, g and h
#parameter [-iname "*agh"] handle searching fies ending with small a, g, h, A, G and H
#parameter [! -name "a*"] handle searching file starting with everything without small a
#parameter [-name "*.conf"] handle searching all files with extension conf
#parameter [-name "*.conf" -o -name "*.cnf"] handle searching all files with extension conf or cnf
#parameter [-name "n" -a -iname "*.conf"] handle searching all files starting with small a and with extension conf, Conf, COnf, ConF etc.
#parameter [-size +10k] handle searching all files wich size is bigger than 10kB
#parameter [-maxdepth "number"] means searching how deep searching is [-maxdepth 1] = one subdirectory down
find . -iname "*.conf" -o -iname "*.cnf" | xargs cp -t ~szamak77/katalog/ #handle searching all files in current directory "." with extension conf, cnf, Cnf, CnF, CNF, CoNf etc. and next "cp" copy those files to "-t" target directory "~szamak77/katalog/"
find . -iname "*.conf" -o -iname "*.cnf" | xargs cp -t ~szamak77/katalog/ #handle searching all files in current directory "." with extension conf, cnf, Cnf, CnF, CNF, CoNf etc. and next "cp" copy those files to "-vt" verbose - displays all files home directory and target directory, target directory "~szamak77/katalog/"
```

```bash
grep #searching in file content
grep [<string>, <number> etc.] [directory mame] [file name] #searches in [file name] for [searching string, number etc.] and displays  owner, shell and home directory eg. "grep szamak77 /etc/passwd"
grep -c [<string>, <number> etc.] [directory mame] [file name] #counts and displays lines quantity including [searching string, number etc.] eg. "grep -c szamak77 /etc/passwd"
grep -r [<string>, <number> etc.] [directory mame] #searches in [directory mame] and displays all files including [searching string, number etc.] located in [directory mame] and subdirectories eg. "grep -r szamak77 ."
grep -v [<string>, <number> etc.] [file name] #searches in [file name] and dispalys everything except [searching string, number etc.]
```

```bash
#services:
sudo service #displays srvies options
sudo service --status-all #displays all services and their status
```

```bash
#processes - running apps:
ps #displays running processes with PID's;
kill [PID number] #turns off app with equal PId number
ps - u [user name] #displays apps running for [user name]
```

```bash
#SSH - Secure Shellk: service for remote connecting with servers and computers, offering encrypted terminal connection, protects data against interception

cd /etc/ssh/
sudo less sshd_config #displays content sshd_config
sshd_config #contains sshd server configuration
#commented line, doesn't participate in execution
Port 22 #(standard) - is standard for SSH but admins don't use it because many bots are scrawling web to find open port 22, trying to find standard name users like Adam, John etc., endeavor to log in. It leaves many trash logs. Unreasonable user could use simple password. Than bad actor can log in to our server, use it do smethin bad or log in to another server and directs suspicion at us.
LogLevel INFO #(standard) - to get more information admins use "LogLevel VERBOSE"
PermitRootLogin prohibit-password #(stanndard) - makes impossible to remote login with password, forces to use special keys; "PermitRootLogin no" - forces to remote login only for standard user and then use command "su" or "sudo" to login as a root
ListenAddress 0.0.0.0 #(standard) - makes posssible to listen on IP adresses, possibles to lsiten from many adresses or to cut some
MAxAuthTries 6 #(standard) - dissconnects after 6 unsuccessful login tries, good practice is set up 3; it is clearly visible in logs
Banner no #(standard) - to set up banner path eg. /etc/ssh/banner (banner - file with banner), banner displays information before use password

sudo service ssh restert #restarts ssh
ssh localhost #logs in to local host
ssh -l [remote host name] [ip adress] #logs in to remote host [remote host name]
ssh -l [remote host name]@[ip adress] #logs in to remote host [remote host name]
ssh -p [port number] [remote host name] #logs to remote host, if port number is different than 22
```

```bash
#SCP - transfer file protocol

scp [file name] [remote host name]@[ip adress]: #send file to remote host
scp -P [port number] [file name] [remote host name]: #send file to remote host, if port number is different than 22
command: "scp [remote host name]@[ip adress]:[file name] [target directory name] - download [file name] from [remote host name] to [target directory name]
command: "scp -P [port number] [remote host name]:[file name] [target directory name]" - download file from remote host, if port number is different than 22
```

```bash
#VIM basics

moving: arrow keys or [h], [j], [k] and [m] keys
command: ": set number" - numbers lines
[number] key + [arrow] key - moves [number] key lines or rows
[:] + [number] key + [Enter] - moves direct to [number] key line
[Ctrl] + [u] - moves many lines up
[Ctrl] + [b] - moves many lines up
[Ctrl] + [d] - moves many lines down
[Ctrl] + [f] - moves many lines down
/[word] + Enter - searches [word], [n] key - next [word], [x] key - deletes sign under cursor, [Esc] key - backs to command mode
[i] key - turns on insert mode, enables writing
[c] key + [w] key - turns on insert mode
[o] key - createst one line under current line and goes to new line into insert mode
double [d] key - deletes line
[:] + [W] + [q] - writes file and exits
[:] + [X] - writes file
[Shift] + doble [z] key - writes file
double [y] key - copy line
[p] key - paste line
```

```bash
#CRONTAB - repetetive tasks in Linux

crontab -e #run/edit Crontab 
#TIP: Use whole and precise paths to files with commands or fies located in directories like: /usr/bin, /usr/sbin, /bin, /sbin.
#Cron only executes commands and doesn't repeat errors.
#TIP: Cron is terminate with createor user rights. Eg. We need to think taht is it necessary to create cron from root account.
```

```bash
#BASH basics
```

```bash
#NETCAT basics<br>
nc -vzw 1 [ip adress] [port number] #verbose scanning, timeout for connects, zero-I/O mode used for scanning]
nc -l #listen mode, for inbound connects
nc -lvp [port number] > [file name] #listening mode on local port [port number], input is writing to [file name]
nc -n [ip adress] [port number] < [file name] #[file name] expousure for host [ip adress] on [port number]
nc -lvp [port number] | tar vzxf - #listening mode on local port [port number], decompresses and shows input
tar czvf - [folder name] | nc -n [ip adress] [port number] #compresses [folder name] and send to host with [ip adress]
```

```bash

#RCR - remote code execution
```