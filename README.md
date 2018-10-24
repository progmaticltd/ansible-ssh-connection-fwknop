# ansible-ssh-connection-fwknop
An Ansible SSH connection plugin, that open the connection through fwknop.

fwknop stands for the "FireWall KNock OPerator", and implements an
authorization scheme called Single Packet Authorization (SPA). This
method of authorization is based around a default-drop packet filter
(fwknop supports iptables and firewalld on Linux, ipfw on FreeBSD and
Mac OS X, and PF on OpenBSD) and libpcap. SPA is essentially next
generation port knocking.

More details about fwknop on the [fwknop home
page](https://www.cipherdyne.org/fwknop/).

## Installation

In ansible.cfg, specify the path of the python file ssh_fwknop.py, for instance:

```ini
[defaults]
retry_files_enabled = False
stdout_callback = yaml
connection_plugins = {{ playbook_dir }}/../../common/connection-plugins/
```

The configuration settings are going to the host file:

```yaml
all:
  hosts:
    homebox:
      ansible_host: homebox.space
      ansible_user: root
      ansible_port: 22
      # special parameters for fwknop
      fwknop_src: 192.168.64.55
      fwknop_config_name: main.homebox.space
      fwknop_dest: main.homebox.space
      fwknop_rc_file: /home/andre/.fwknop-homebox.space.rc
      fwknop_verbose: true
      fwknop_executable: '/usr/bin/fwknop'

```

Then, specify the connection plugin when running the ansible playbook.

```sh
ansible-playbook -c ssh_fwknop -vvv -i ../config/hosts.yml playbooks/fwknop-server.yml
```

The outpput should be something like this:

```sh
TASK [Gathering Facts] **********************************************************
task path: /home/andre/Projects/homebox/install/playbooks/fwknop-server.yml:4
<homebox.space> ssh_fwknop connection plugin is used for this host
Running '/usr/bin/fwknop -v -a 192.168.64.55 -D main.homebox.space --rc-file /home/andre/.fwknop-homebox.space.rc -n main.homebox.space'
SPA Field Values:
=================
   Random Value: 2055127260157875
       Username: andre
      Timestamp: 1540365432
    FKO Version: 3.0.0
   Message Type: 1 (Access msg)
 Message String: 192.168.33.55,tcp/22
     Nat Access: <NULL>
    Server Auth: <NULL>
 Client Timeout: 0
    Digest Type: 3 (SHA256)
      HMAC Type: 3 (SHA256)
Encryption Type: 1 (Rijndael)
Encryption Mode: 2 (CBC)
   Encoded Data: 2051547260157875:YW5kcmU:1540365432:3.0.0:1:MTkyLjE2OC42NC41NSx0Y3AvMjI
SPA Data Digest: ouGmZ4xPqkdFNTXAWLkyspppyGNs0m0uP8pfhYskwuu
           HMAC: FVmp7QxZgr26JsnurndW6cGBdw1jlaB0rnGYaV8G1X8
 Final SPA Data: +1h6fIcvYM3RNtvcTUU7THrzdrhby5x0wBQhigf0wceTPjskemdjui6JIAThXJjc6RsDOR9gxU44wFt2LJG9jzG8tCX5FI8uYB+PzQ11JE8e7yksQZtyHX74gMqoFUj+Z6iMN2U9Jb6bPgkyHAps+hf2fGArGO0V3ml/PWqqixCYCcjI/z3yYX/FVmp7QxZgr26Dj4O1q1W6cGBdw1jlaB0rnGYaV8G1X8

Generating SPA packet:
            protocol: udp
         source port: <OS assigned>
    destination port: 62201
             IP/host: main.homebox.space
send_spa_packet: bytes sent: 225
...
```

This script was originally part of the [Homebox](https://github.com/progmaticltd/homebox) project.
