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

sudo systemctl disable --now apaache2.service (NOTE: Avoid using this command in VMs)
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