
The **/etc/passwd** file is the most important file in Linux operating system. This file stores essential information about the users on the system. This file is owned by the root user and to edit this file we must have root privileges. But try to avoid edit this file. Now let's see actually how this file look

This file contains one entry per line. That means it stores one user's information on one line. The user information contains seven fields and each field is separated by the colon ( **:** )symbol. Each entry in the /etc/passwd file looks like this:

![](https://media.geeksforgeeks.org/wp-content/uploads/20210721154851/Screenshotfrom20210721154827.png)

#### Now let's understand each field one by one:

1. **Username:** This field stores the usernames which are used while login into the system. The length of this field is between 1 and 32 characters.
2. **Password**: This field store the password of the user. The **x** character indicates the password is stored in /etc/shadow file in the encrypted format. We can use the **passwd** command to update this field.
3. **User ID(UID)**: User identifier is the number assigned to each user by the operating system to refer the users. The 0 UID is reserved for the root user. And 1-99 UID are reserved for other predefined accounts. And 100-999 are reserved by the system for administrative and system accounts/groups.
4. **Group ID(GID)**: Group identifier is the number indicating the primary group of users. Most of the time it is the same as the UID.
5. **User ID Info (GECOS)**: This is a comment field. This field contain**s** information like the user phone number, address, or full name of the user. This field is used by the [**finger**](https://www.geeksforgeeks.org/linux-unix/finger-command-in-linux-with-examples/) command to get information about the user.
6. **Home directory**: This field contains the absolute path of the user's home directory. By default**,** the users are created under the /home directory. If this file is empty**,** then the home directory of that user will be /
7. **Login shell**: This field store the absolute path of the user shell. This shell is started when the user is log in to the system.

Now we have understood the file structure of the /etc/passwd file now let's see one example of this file. You can view the content of file using the cat file like:


cat /etc/passwd

![[Pasted image 20251217112343.png]]


Linux has **three types of users**:

### 1) System users (services & daemons)

These run background services like:

- networking
    
- logging
    
- display manager
    
- cron jobs

daemon
mail
ftp
www-data
systemd-network
systemd-resolve
polkitd
avahi
lightdm


### 2) Root user

`root:x:0:0:root:/root:/bin/zsh`

- UID = `0` → **superuser**
    
- Shell = `/bin/zsh` (means root _can_ log in)
    
- Home = `/root`

### 3) Normal human users

Example:

`nishant:x:1000:1000::/home/nishant:/usr/bin/zsh`

How to identify real users:

- UID ≥ **1000**
    
- Valid shell (`/bin/bash`, `/bin/zsh`)
    
- Home directory in `/home/`

