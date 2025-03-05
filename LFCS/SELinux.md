



## To install SELinux
sudo apt install selinux-basics auditd

## To check if SELinux is enabled in our system
sestatus

## To activate SELinux
sudo selinux-activate

Note: Reboot the system after the above command.

## To check in which mode the SELinux is running
getenforce

## We can check what SELinux recorded in audit log with below command
sudo audit2why -all | less


## To enable everything that SELinux witnessed till now 
sudo audit2allow -all -M mymodule

This will generate 2 files.
    -   mymodule.pp ( linux understandable format binary with all rules )
    -   mymodule.te ( Text format for humans to understand the rules )


## WARNING NOTE:

Make sure to execute "fixfiles -F onboot" before changing the selinux mode to enforcing.
Then reboot. Otherwise system might crash and goes into unrecoverable mode.

## To change the mode of SELinux from permissive to Enforcing mode
sudo setenforce 1

NOTE: Above command put SELinux in Enforcing mode. But that doesn't survice reboots.

## To put SELinux in enforcing mode permanantely
Edit /etc/selinux/config
Update SELINUX value to enforcing from permissive


## To change the user/role/type labels of a file.
sudo chcon -u unconfined_u /var/log/auth.log
sudo chcon -r unconfined_r /var/log/auth.log
sudo chcon -t unconfined_t /var/log/auth.log


NOTE: By default we will be having the user/role/type as unconfined.
NOTE: Whatever the labels ( user/role/type ) we assigned above using chcon are not valid. To assign a valid labels we need go through huge documentation.
There is a simple way to check.

## To check all the available valid user labels
```bash
ubuntu@ip-172-31-28-107:~$ seinfo -u

Users: 7
   root
   staff_u
   sysadm_u
   system_u
   unconfined_u
   user_u
   xdm

```

## To check all the available valid role lables
```bash
ubuntu@ip-172-31-28-107:~$ seinfo -r

Roles: 15
   auditadm_r
   dbadm_r
   guest_r
   logadm_r
   nx_server_r
   object_r
   secadm_r
   staff_r
   sysadm_r
   system_r
   unconfined_r
   user_r
   webadm_r
   xdm_r
   xguest_r
```

## To check all the available valid type labels 
```bash
ubuntu@ip-172-31-28-107:~$ seinfo -t

Types: 3952
   NetworkManager_etc_rw_t
   NetworkManager_etc_t
   NetworkManager_exec_t
   NetworkManager_initrc_exec_t
   NetworkManager_log_t
   NetworkManager_runtime_t
   NetworkManager_t
......
```

### EXAMPLE
```bash
# We have a file /var/log/auth.log with below permissions
ubuntu@ip-172-31-28-107:~$ ls -Z /var/log/auth.log
    system_u:object_r:var_log_t:s0 /var/log/auth.log

# Now we can update the user/role/type of this file
ubuntu@ip-172-31-28-107:~$ sudo chcon -u unconfined_u /var/log/auth.log
ubuntu@ip-172-31-28-107:~$ sudo chcon -r unconfined_r /var/log/auth.log
ubuntu@ip-172-31-28-107:~$ sudo chcon -t unconfined_t /var/log/auth.log
ubuntu@ip-172-31-28-107:~$ ls -Z /var/log/auth.log
    unconfined_u:unconfined_r:unconfined_t:s0 /var/log/auth.log

# We by mistake update the user/role/type of file with wrong user/role/type.
# How to know what are the actual role/user/type of a file.
# There are 2 ways...
# 1) We can look a the lables of other files under /var/log directory. If we are lucky we can find some other files in the directory.
# But manually setting user/role/type can be tedious. We can all 3 properties in one shot.
sudo chcon --reference=/var/log/otherfile /var/log/auth.log

Eg: 
ubuntu@ip-172-31-28-107:~$ ls -Z /var/log/
           system_u:object_r:var_log_t:s0 README                            system_u:object_r:var_log_t:s0 dmesg
           system_u:object_r:var_log_t:s0 alternatives.log                  system_u:object_r:var_log_t:s0 dmesg.0
           system_u:object_r:var_log_t:s0 amazon                            system_u:object_r:var_log_t:s0 dpkg.log
           system_u:object_r:var_log_t:s0 apport.log                system_u:object_r:systemd_journal_t:s0 journal
       system_u:object_r:apt_var_log_t:s0 apt                               system_u:object_r:var_log_t:s0 kern.log
        system_u:object_r:auditd_log_t:s0 audit                             system_u:object_r:var_log_t:s0 landscape
unconfined_u:unconfined_r:unconfined_t:s0 auth.log                          system_u:object_r:lastlog_t:s0 lastlog
           system_u:object_r:faillog_t:s0 btmp                              system_u:object_r:var_log_t:s0 private
   system_u:object_r:chronyd_var_log_t:s0 chrony                            system_u:object_r:var_log_t:s0 syslog
           system_u:object_r:var_log_t:s0 cloud-init-output.log         system_u:object_r:sysstat_log_t:s0 sysstat
           system_u:object_r:var_log_t:s0 cloud-init.log                    system_u:object_r:var_log_t:s0 unattended-upgrades
           system_u:object_r:var_log_t:s0 dist-upgrade                         system_u:object_r:wtmp_t:s0 wtmp

ubuntu@ip-172-31-28-107:~$ sudo chcon --reference=/var/log/dmesg /var/log/auth.log

ubuntu@ip-172-31-28-107:~$ ls -Z /var/log/auth.log

system_u:object_r:var_log_t:s0 /var/log/auth.log

# 2) Lets suppose if we create new files, what labels do they have now.

sudo mkdir /var/www
sudo touch /var/www/{1..10}  # This will create 10 files. What labels do they have now ?

Eg:
ubuntu@ip-172-31-28-107:~$ sudo mkdir /var/www
ubuntu@ip-172-31-28-107:~$ sudo touch /var/www/{1..10}
ubuntu@ip-172-31-28-107:~$ ls -Z /var/www/
unconfined_u:object_r:var_t:s0 1   unconfined_u:object_r:var_t:s0 3  unconfined_u:object_r:var_t:s0 6  unconfined_u:object_r:var_t:s0 9
unconfined_u:object_r:var_t:s0 10  unconfined_u:object_r:var_t:s0 4  unconfined_u:object_r:var_t:s0 7
unconfined_u:object_r:var_t:s0 2   unconfined_u:object_r:var_t:s0 5  unconfined_u:object_r:var_t:s0 8

# We can see the labels are incorrect. So fix this we run restorecon command ( restore context)

ubuntu@ip-172-31-28-107:~$ sudo restorecon -r /var/www/
ubuntu@ip-172-31-28-107:~$ ls -Z /var/www/
unconfined_u:object_r:httpd_sys_content_t:s0 1   unconfined_u:object_r:httpd_sys_content_t:s0 4  unconfined_u:object_r:httpd_sys_content_t:s0 8
unconfined_u:object_r:httpd_sys_content_t:s0 10  unconfined_u:object_r:httpd_sys_content_t:s0 5  unconfined_u:object_r:httpd_sys_content_t:s0 9
unconfined_u:object_r:httpd_sys_content_t:s0 2   unconfined_u:object_r:httpd_sys_content_t:s0 6
unconfined_u:object_r:httpd_sys_content_t:s0 3   unconfined_u:object_r:httpd_sys_content_t:s0 7

# As these are part of apache2 webserver it fixed the context of all files to httpd_sys_content_t

# NOTE: restorecon will by default restore the label for type only.
# NOTE: To forcce restorecon to restore the user/role labels we can pass force option

ubuntu@ip-172-31-28-107:~$ sudo restorecon -F -r /var/www/
ubuntu@ip-172-31-28-107:~$ ls -Z /var/www/
system_u:object_r:httpd_sys_content_t:s0 1   system_u:object_r:httpd_sys_content_t:s0 4  system_u:object_r:httpd_sys_content_t:s0 8
system_u:object_r:httpd_sys_content_t:s0 10  system_u:object_r:httpd_sys_content_t:s0 5  system_u:object_r:httpd_sys_content_t:s0 9
system_u:object_r:httpd_sys_content_t:s0 2   system_u:object_r:httpd_sys_content_t:s0 6
system_u:object_r:httpd_sys_content_t:s0 3   system_u:object_r:httpd_sys_content_t:s0 7


######### NOTE ######
# There is a drawback if we relabel user/role/type with chcon option.
# Anytime in the future if the filesytem is relabeled again just like how it happens when we install and activated the SELinux, our changes will be lost.

# To make the changes future proof
# Lets suppose /var/www/10 file always needs to be have var_log_t type.
sudo semanage fcontext --add --type var_log_t /var/www/10
sudo restorecon /var/www/10


# To see the list of all default file context labels
sudo semanage fcontext --list


# SELinux uses labels to make decisions about what should allow and what not to.
# But we can booleans to flip what to allow and what to disallow in SELinux a set of actions.
# We can check the list supported SELinux booleans with the below command
sudo semanage boolean --list

# To set boolean
sudo setsebool virt_use_nfs 1

# To see the boolean
sudo getsebool virt_use_nfs


# Another thing that SELinux enforces is  what ports certain daemons can bind to listen to for incoming connection.
# To list port bindings that are allowed
sudo semanage port --list

ubuntu@ip-172-31-28-107:~$ sudo semanage port --list | grep ssh
ssh_port_t                     tcp      22

#Lets suppose you want to allow ssh to listen to port 2222 also.
ubuntu@ip-172-31-28-107:~$ sudo semanage port --add --type ssh_port_t --proto tcp 2222

#Lets suppose you don't want to allow ssh to listen to port 2222.
ubuntu@ip-172-31-28-107:~$ sudo semanage port --delete --type ssh_port_t --proto tcp 2222

```