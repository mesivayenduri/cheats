## CREATE, DELETE & MODIFY LOCAL Users Accounts

```bash
# To create a new user
sudo adduser john

# To set a password to a user
sudo passwd john

# To delete a user and group john if no other user is part of group john
sudo deluser john

sudo deluser --remove-home john

sudo adduser --shell /bin/othershell --home /home/otherdirectory/ john

sudo adduser --shell /bin/othershell john

cat /etc/passwd

# To create a user with custom UID
sudo adduser --uid 1100 smith

ls -l /home/

ls -ln /home/

# To view the UID, GID details of the current user
id

# To check the current user name
whoami

# To create systemaccount 'jenkins_user'
sudo adduser --system --no-create-home jenkins_user
# NOTE: Usually daemons use systemaccounts

# To modify a user details
sudo usermod --home /home/newhomedir --move-home john

# To change the username
sudo usermod --login <newusername> <oldusername>

sudo usermod --shell <othershell_path> <username>


# To lock a useraccount
sudo usermod --lock username   |  sudo usermod -L username

# To unlock
sudo usermod --unlock username  | sudo usermod -U username

# To set a date on which the useraccount expires
sudo usermod --expiredate 2028-12-10 jane
    #DATE FORMAT: YEAR-MONTH-DAY
    # If you want to expire the account immediately.. You can choose the date in the past

# To remove the expiration date
sudo usermod --expiredate "" jane 


# We can set expiration on password
# If you want set the password as expired immediately
sudo chage --lastday 0 jane

# To cancel the above password expiration
sudo chage --lastday -1 jane


# If you want to make sure user changes the password once every 30days
sudo chage --maxdays 30 jane

# Password never expires
sudo chage --maxdays -1 jane

# To see the account expiration
sudo chage --list jane
```


## CREATE, DELETE & MODIFY LOCAL GROUP and GROUP MEMBERSHIPS

```bash

sudo adduser john

sudo groupadd developers

sudo gpasswd --add john developers  | sudo gpasswd -a john developers

# To check 
groups john

sudo gpasswd --delete john developers  | sudo gpasswd -d john developers

# To change the primary or logingroup of a user
sudo usermod -g developers john  | sudo usermod --gid developers john

# To rename a group
sudo groupmod --new-name <new gorup name> <old group nam>

sudo groupdel programmers
```


## MANAGE SYSTEM WIDE ENVIRONMENT PROFILES

```bash
# To see the current environment variables
printenv or env

# If a user wants to add their own envionment variables they can use .bashrc file
.bashrc

/etc/environment


# To run a command whenever a user logs in
/etc/profile.d/

sudo vim /etc/profile.d/lastlogin.sh   ( This will now executed every time a user logs in)

```

## MANAGE TEMPLATE USER ENVIRONMENT

What ever you put under /etc/skel/ directory those will be automatically copied to user's home directory whenever you create a new user.


## CONFIGURE USER RESOURCE LIMITS
```bash

# To manage limits of users we can use below file.
/etc/security/limits.conf

<DOMAIN>                <TYPE>      <ITEM>                                          <VALUE>
User/Group/*          Soft/Hard/-   core/rss/nproc/maxlogins/cpu/fsize/etc        Any integer

# To see the limits of the current session
ulimit -a

```


## MANAGE USER PRIVILEGES
```bash
# If a user wants a sudo privileges we can add them to sudo group
sudo gpasswd --add saisiva sudo
#NOTE: This will give the user to run anything.

# To have more fine control we can use /etc/sudoers file
sudo visudo

%sudo          ALL     =       (   ALL     =   ALL)        ALL
  |             |                   |           |           |
User/Group   HOSTNAME/IP     RUN_AS_USER  RUN_AS_GROUP   COMMAND_LIST

Eg: trinity ALL=(ALL)   ALL
    %developers ALL=(ALL)   ALL
    trinity ALL=(aaron,john)    ALL
    trinity ALL=ALL
    trinity ALL=(ALL)   /bin/ls, /bin/stat
    trinity ALL=

```

## MANAGE ACCESS TO THE ROOT ACCOUNT

```bash

# User with sudo access can login to root using

sudo --login
  or
sudo -i

# If user doesn't have sudo access but knows the root password

su -
  or
su -l
  or
su --login


# Some systems might have the root account locked but with sudo access
sudo --login

# If we want to allow the users to login as root with password

sudo passwd root

sudo passwd --unlock root | sudo passwd -u root

# If we want to lock the password based logins ( But still be able to login with ssh )

sudo passwd --lock root  | sudo passwd -l root


```