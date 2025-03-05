## To Reboot or Shutdown

```bash
    systemctl reboot
    systemclt shutdown
```

## To forcefully reboot

```bash
    systemctl reboot --force
    systemctl reboot --force --force
```

# Boot or change system into different Operating modes
```bash
    systemctl get-default

    sudo systemctl set-default multi-user.target
    # With this every thing runs as usual ( daemons, services etc ). Only graphical interface will be skipped. Everthing will be text based.

    
    # To switch between the targets with rebooting we use isolate
    sudo systemctl isolate graphical.target
    sudo systemctl isolate multi-user.target

    #NOTE: if we use isolate we can easily switch between the targets without reboot.
    # But this doesn't change the default target. So if we reboot the system it will load the target which was set default only.

    - Emergency.target
        - Will load up only few programs as possible for debugging purpose. In this target root file system will be loaded as read-only

    - Rescue.target
        - Only few essential services will be loaded. you will dropped on the root shell.it will load little more programs than emergency.target but less than multi-user.target

```


# Manage System Startup Processes and Services

INIT: Initialization System

Systemd is the name of a large collection of tools and components, but also the name of the application responsive for system initialization and monitoring the system to ensure smooth operation

Systemd has several units as below
- services
- sockets
- devices
- timer
- etc...

To check out all the units type "man systemd.(tab + tab)"

```bash
man systemd.service

# To edit a service
sudo systemctl edit --full ssh.service

# To revert the changes
sudo systemctl revert ssh.service

# To check the status
sudo systemctl status ssh.service

# To stop the service
sudo systemctl stop ssh.service

# To start the service
sudo systemctl start ssh.service

# To restart the service
sudo systemctl restart ssh.service

# In somescenarios like, there might be users using the service while we are trying to restart the service. They will face issues with the service if we restart. There is a better way to do this.
sudo systemctl reload ssh.service

# Not all services support "reload". To achieve this we have other command.
sudo systemctl reload-or-restart ssh.service

# To disable the service. If you disable the service, it won't comeup automatically after the boot. It will only disables the service but won't stop the running/active serivce
sudo systemctl disable ssh.service

# Other way to check if a service is enabled or disabled
sudo systemctl is-enabled ssh.service

# To enable a serivce to start automatically after boot.
sudo systemctl enable ssh.service
# With systemctl enable system will automatically add the respective service to /etc/systemd/system/multi-user.target.wants/<service-name>

```

Sometimes you install a webserver or database, we want to do two things to automatically start the service after boot.

```bash
systemctl enable <service-name>
Eg: systemctl enable apache2.service

systemctl start <service-name>
Eg: systemctl start apache2.service
```

There is a better approach to do this
```bash
sudo systemctl enable --now apache2.service

sudo systemctl disable --now apaache2.service
```

There are some services that might comeup automcatically even though we disable/stop them during the next boot.
There is a bruteforce way to prevent this happenning using MASKING
```bash
# TO MASK
sudo systemctl mask <service-name>

#TO UNMASK
sudo systemctl unmask <service-name>
```

To List all units of type service
```bash
sudo systemctl list-units --type service --all
sudo systemctl list-units --type timer --all
sudo systemctl list-units --type socket --all
sudo systemctl list-units --type devices --all
sudo systemctl list-units --type mount --all
```


## CREATE SYSTEMD SERVICE
NOTE:All the service files exists in this location "ls /lib/systemd/system"

Example:
```bash
sudo nano /lib/systemd/system/myapp.service

[Unit]
Description=Flask Application
After=network.target

[Service]
User=vboxuser
WorkingDirectory=/home/vboxuser/
ExecStart=/usr/bin/python3 /home/vboxuser/myapp1.py
Restart=always
RestartSec=5
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target


> sudo systemctl daemon-reload
> sudo systemctl start myapp.service

```

## DIAGNOSE AND MANAGE PROCESSES

```bash
> ps a   ( This will show only the processes launched in the current terminal window or session )
> ps aux ( To display all processes in user oriented format )
> top ( to continuously monitor processes )
> ps <PID>
> ps u <PID>
> ps -U <username> ( to check all the processes started by a user)
> ps u -U <username>
> pgrep -a string ( pgrep is used to check for a process which contains a word <string>)
> ps l ( to view the nice value of the processes )
> ps lax ( to view all the processes with nice values )
> ps fax ( to view all the processes with their forest structure)
> nice -n <value> ( to set a nice value while starting a process)
    Eg: nice -n 10 python3 app1.py &
    Eg: nice -n 5 sleep 100 &
> renice <number> processID  ( To renice a process )
NOTE: Once you renice a process to a value 'n', you can only renice it again with higher value.
      To reduce the nice value you should use sudo.
    Eg: renice 12 <PID of app1.py>
    Eg: renice 10 <PID of app1.py> --> will give error
    Eg: renice 13 <PID of app1.py> --> Works fine
    To reduce the renice value, you should a sudo user
    Eg: sudo renice 8 <PID of app1.py>

> kill -L

> ctrl+c ( quit )
> ctrl+z ( Stop )
> sleep 190000 & ( & is used to run the process in the background )
> fg ( to bring a process foreground that runs in background )
> jobs ( to check all the processes that run in background )
> 
```

## LOCATE AND ANALYSE SYSTEM LOGS FILES
```bash
rsyslog  ( stores logs under /var/log/ )
journalctl ( journal daemon )

journalctl /usr/bin/sudo ( display all system logs with sudo )
journalctl -u ssh.service ( display all logs by ssh.service )
journalctl  -p <log-level> 
journalctl -p info -g "^b" ( -g for grep expression to filter logs )
journalctl -S 02:00 ( since )
journalctl -U 11:00 ( Unitl)
journalctl -S 02:00 -U 04:00
jouralctl -S "2025-03-01 01:00:00"

journalctl --list-boots
journalctl -b "id"  "this will show the logs of the each boot"

NOTE: In some operating systems journalctl daemon will store only the logs of last 2 boots only. To make it stores logs of all reboots
we need to create a directory /var/log/journal


last ( This will show list of last logins )
lastlog ( This will each user when was they last logged in )
```


## SCHEDULE TASKS TO RUN AT A SET DATE AND TIME

```bash
> cron : repetivite ( min, hours, days, or specific time)
NOTE: if the computer is off and whenever you starts it up, if the specified time already missed it wont run
> anacron: (day, week, month, year)
NOTE: If the computer is off and whenever you starts it up, anacron checks if it ran for that day or not. If not it will run
> at: For tasks that only run once.


cat /etc/crontab

    *   -   match all possible values
    ,   -   match multiple values ( 15,45 )
    -   -   range of values ( 2 - 4)
    /   -   specfies steps ( */4 )

crontab -l

sudo crontab -l

sudo crontab -e -u jane

crontab -r ( to remove your users crontab )

sudo crontab -r -u jane ( to remove tab of other user )


daily = /etc/cron.daily/
hourly = /etc/cron.hourly/
monthly = /etc/cron.monthly/
weekly = /etc/cron.weekly/


ANACRON: ( daily, montly, once every few days )

sudo apt install anacron

sudo vim /etc/anacrontab

anacron -T  ( to verify the syntax of anacron tab)


AT: 

sudo apt install at

Syntax: at "15:00" <command>
Example: at "15:00" 
Example: at "August 20 2025"
at "2:30 August 20 2025"
at 'now + 30 minutes'
at 'now + 3 hours'
at 'now + 3 days'
at 'now + 3 weeks'
at 'now + 3 months'

If you want to schedule a job using at
> at 'now + 2 min'   ( this will enter you into a prompt where you can enter the script or command to execute )
> at > /usr/bin/bash /home/vboxuser/backup.sh
> at > ctrl + D   (to exit)

atq ( display all the at jobs job-identifiers)

atq -c <JOB-IDENTIFIER>

atrm <JOB-IDENTIFIER>

```


## MANAGE SOFTWARE WITH PACKAGE MANAGER

```bash

> sudo apt update
> sudo apt upgarde
> sudo apt instal <package name>

> dpkg --listfiles nginx  ( To view the files in the nginx package )

> dpkg --search "<full-path-of-file>"  ( To view which package a file belongs to )

> sudo apt show <package-name> ( To know more details of a package/dependency )

> sudo apt search nginx

> sudo apt search --name-only nginx

> sudo apt search nginx module image

> sudo apt remove nginx

> sudo apt auto-remove nginx

```


## CONFIGURING THE REPOSITORIES OF PACKAGE MANAGER

```bash
Older Versions of Ubuntu: 
    cat /etc/apt/sources.list
Latet Versions of Ubuntu:
    cat /etc/apt/sources.list.d/ubuntu.sources
----------------------------------------------
-> Sometimes the packages that we want to install might not be available in default ubuntu package repository. To enable our system to download them from a different package repository we need to do the following and those instructions will be provided by respective third party websites.

-> Eg: Here I'm showing the example for docker.

curl "https://download.docker.com/linux/ubuntu/gpg" -o docker.key

gpg --dearmor docker.key

mv docker.key.gpg /etc/apt/keyrings/

sudo nano /etc/apt/sources.list

>
deb [signed-by=/etc/apt/keyrings/docker.key.gpg] https://download.docker.com/linux/ubuntu noble stable

sudo apt update

sudo apt install docker
---------------------------------------------

Now another type of repository you can add is PPA ( personal package archive )

sudo add-apt-repository ppa:saisiva/mylatestapp

To add a other personal ppa

sudo add-apt-repository ppa:xxxxx/xxxxxx

sudo apt update

To remove the added personal ppa

sudo add-apt-repository --remove ppa:xxxxx/xxxxx

sudo apt update

To list any ppa's that enabled

sudo add-apt-repository --list

```

## INSTALLING SOFTWARE BY COMPILING SOURCE CODE

```bash

sudo apt install git

Eg: htop

git clone https://github.com/htop-dev/htop.git

cd htop

Read the readme file for the instructions to install the app.

```

## VERIFY THE INTEGRITY AND AVAILABILITY OF RESOURCES AND KEY PROCESSES

```bash
df ( disk free utility )

df -h

du -sh <path>  ( disk usage )

free -h ( to see the memory usage)

uptime ( how the cpu cores were used by the programs )

lscpu ( To see the CPU info )

lscpu ( To see the details about the hardward on the system )

------ INTEGRITY OF THE FILE SYSTEM
To check the file system for errors we must first unmount them.

Ubuntu uses ext4 filesystem

sudo fsck.ext4 -v -f -p /dev/sbd2

----- TO CHECK KEY PROCESSES

systemctl list-dependencies

```


## CHANGE KERNEL RUNTIM PARAMETERS, PERSISTENT AND NON-PERSISTENT

```bash

sysctl -a

sudo sysctl -a

sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1  ( non-persistent )

sudo sysctl net.ipv6.conf.default.disable_ipv6

sudo nano /etc/sysctl.conf ( persistent : NOT RECOMMENDED as if the system upgardes we might loose the changes if there any upgarde related to this. )

sudo nano /etc/sysctl.d/swap-less.conf ( persistent : RECOMMENDED )

    > vm.swapiness=20

sudo sysctl -p /etc/sysctl.d/swap-less.conf

```



Whenever you want to create a systemd service refer to the above location for samples


Q) Shutdown the system after 2hours
-   sudo shutdown +120

Q) The system is currently booting to a text-only console.
Change it to boot to a graphical desktop by default.
-   sudo systemctl set-default graphical.target

Q) Cancel the scheduled shutdown you configured in the beginning.
-   sudo shutdown -c




# SELINUX

sudo systemctl stop apparmor.service
sudo systemctl disable apparmor.service

sudo apt install selinux-utils auditd

sudo selinux-activate

reboot

getenforce

sestatus

setenforce 1

sudo nano /etc/selinux/config
    SELINUX=permissive/enforcing/disabled


SELINUX:
    -> user
    -> role
    -> type

sudo audit2allow --all -M custom-packge-name

semodule -i custom-package-name.pp

seinfo -u
seinfo -r
seinfo -t

sudo chcon -t var_log_t /var/log/auth.log
sudo chcon -r object_r /var/log/auth.log
sudo chcon -u object_u /var/log/auth.log


sudo chcon --reference=/var/log/<anyfile> /var/log/auth.log

sudo restorecon -R /var/www/   --> restorecon by default restores only type 

sudo restorecon -F -R /var/www/  ---> To restore user and role you need to pass -F

suppose if you sent any type context using "chcon", those are temporary and might get changed whenever you run autorelabel again.

To make it permananent you need to add to fcontext like below

sudo semanage fcontext --add --type var_log_t /var/www/1  -> sudo restorecon /var/www/1

sudo semanage fcontext --add --type var_log_t "/var/www(/.*)?"  -> sudo restorecon /var/www/*

sudo semanage boolean --list

sudo setsebool virt_use_nfs 1

sudo getsebool virt_use_nfs

sudo semanage port --list

sudo semanage port --add --type ssh_port_t --proto tcp 2222

sudo semanage port --delete --type ssh_port_t --proto tcp 2222


# CREATE AND MANAGE VIRTUAL CONTAINERS

QEMU-KVM
QEMU -> Quick EMULator
KVM -> Kernal based Virtual machines

VIRSH is the command line cli we use to manage virtual machines.

sudo apt install virt-manager


touch testmachine.xml

<domain type="qemu">
    <name>TestMachine</name>
    <memory unit="GiB">1</memory>
    <vcpu>1</vcpu>
    <os>
        <type arch="x86_64">hvm</type>
    </os>
</domain>


virsh define testmachine.xml

virsh list
virsh list --all

virsh start TestMachine

virsh reboot TestMachine   ( reboot is graceful )

virsh reset TestMachine

virsh destroy TestMachine   (This is actually powering off the machine)

virsh undefine TestMachine

virsh undefine --remove-all-storage TestMachine

virsh autostart TestMachine

virsh autostart --disable TestMachine

virsh dominfo TestMachine

To increase the VCPUS
    sudo virsh setvcpus TestMachine 2 --config
    sudo virsh setvcpus TestMachine 2 --config --maximum   
        VCPUS count will not reflect immediately as the VM is still running. So we have to power it off and start...
    sudo virsh destory TestMachine
    sudo virsh start TestMachine
    sudo virsh dominfo TestMachine


To increase the Memory

    sudo virsh setmaxmem TestMachine 2Gb --config

    sudo virsh setmem TestMachine 2Gb --config

    sudo virsh destroy TestMachine

    sudo virsh start TestMachine



qemu-img info ubuntu.img

qemu-img resize ubuntu.img 10G

/var/lib/libvirt

Till now we used virsh cli tool to create vms.
Also vms we created till now doesn't have OS installed.

# Creating VMs from a PRE-INSTALLED Operating system images.

Now we can use virt-install tool. virt-install is also a virtual machine management cli tool.(simplified)

Download ubuntu image from : 

Copy the image to /var/lib/libvirt/images/

virt-install --osinfo ubuntu24.04 --name ubuntu-vm --memory 1024 --vcpus 1 --import --disk /var/lib/libvirt/images/<image> --graphics none

virt-install --osinfo ubuntu24.04 --name ubuntu-vm --memory 1024 --vcpus 1 --import --disk /var/lib/libvirt/images/<image> --graphics none --cloud-init root -password-generate=on

virsh console <ubuntu-vm>   ( to login to the console of vm created by above command )

If you don't know the OS and version...

virt-install --osinfo detect=on --name ubuntu-vm --memory 1024 --vcpus 1 --import --disk /var/lib/libvirt/images/<image> --graphics none --cloud-init root -password-generate=on

# Creating a VM and installing OS manually

virt-install --osinfo debian12 --name debian1 --memory 1024 --vcpus 1 --disk size=10 --location /var/lib/libvirt/boot/debian12-netinst.iso --graphics none --extra-args "console=ttys0"

virt-install --osinfo debian12 --name debian1 --memory 1024 --vcpus 1 --disk size=10 --location https://deb.debian.org/debian/dists/bookworm/main/installer-amd64/ --graphics none --extra-args "console=ttys0"