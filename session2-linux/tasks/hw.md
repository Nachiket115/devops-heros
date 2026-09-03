# Linux Homework

## Task 1: Soft Link and Hard Link

A **soft link (symbolic link)** works like a shortcut to another file. It stores the path of the file it points to. If the original file is deleted or moved, the soft link will no longer work.

To create a soft link:

```bash
ln -s original.txt softlink.txt
```

To delete the soft link:

```bash
rm softlink.txt
```

A **hard link** is another name for the same file. Both the original file and the hard link point to the same inode.

To create a hard link:

```bash
ln original.txt hardlink.txt
```

To check the inode numbers:

```bash
ls -li
```

To delete the hard link:

```bash
rm hardlink.txt
```

The main difference is that a **soft link points to a file path**, whereas a **hard link points to the same inode as the original file**. If the original file is deleted, a soft link becomes broken, but the hard link will continue to work because the data still exists through the other directory entry.

Soft links can point to directories and can also point to files on different filesystems. Hard links generally cannot cross filesystem boundaries and are normally not created for directories.

---

## Task 2: adduser vs useradd

`useradd` is a command used to create a new Linux user. It is a lower-level utility and usually requires us to specify the options we need.

For example:

```bash
sudo useradd -m testuser
```

The `-m` option creates a home directory for the new user.

`adduser` is a more user-friendly command. It is interactive and asks for information such as the user's password and other details while creating the account.

For example:

```bash
sudo adduser testuser
```

For creating a normal user on Ubuntu, I would generally use **adduser** because it is simpler and takes care of more of the setup automatically.

To check the user:

```bash
id testuser
```

To delete the test user:

```bash
sudo deluser testuser
```

In simple terms, **adduser is easier and more interactive**, while **useradd provides more low-level control and is useful when we want to specify the options ourselves or use it in scripts**.

---

## Task 3: journalctl

`journalctl` is used to view and search system logs on Linux systems that use `systemd`. It is useful for troubleshooting services and finding errors.

To view the system logs:

```bash
sudo journalctl
```

To view the latest 50 log entries:

```bash
sudo journalctl -n 50
```

To view logs from the current boot:

```bash
sudo journalctl -b
```

We can also view the logs for a particular service. For example, to check SSH:

```bash
sudo journalctl -u ssh
```

To continuously monitor new logs:

```bash
sudo journalctl -u ssh -f
```

The `-u` option is used to specify a particular systemd service, while the `-f` option follows the logs and displays new entries as they are generated.

`journalctl` is especially useful when a service is not working correctly because the logs can help identify what went wrong.

---

## Task 4: Linux Command Cheat Sheet

Below are some of the Linux commands I practiced and their basic uses.

### `pwd`

Shows the current working directory.

```bash
pwd
```

### `ls`

Lists files and directories.

```bash
ls
ls -la
```

### `cd`

Used to move to another directory.

```bash
cd /home
```

### `mkdir`

Creates a new directory.

```bash
mkdir test
```

### `touch`

Creates a new empty file.

```bash
touch file.txt
```

### `cp`

Copies a file or directory.

```bash
cp file.txt backup.txt
```

### `mv`

Moves or renames a file.

```bash
mv file.txt newfile.txt
```

### `rm`

Deletes a file.

```bash
rm file.txt
```

### `cat`

Displays the contents of a file.

```bash
cat file.txt
```

### `grep`

Searches for a particular string or pattern in a file.

```bash
grep "error" file.txt
```

### `whoami`

Displays the username of the current user.

```bash
whoami
```

### `id`

Displays information about a user, including their user ID and group IDs.

```bash
id
```

### `chmod`

Changes the permissions of a file or directory.

```bash
chmod 755 script.sh
```

### `chown`

Changes the owner of a file or directory.

```bash
sudo chown user file.txt
```

### `ps`

Displays information about running processes.

```bash
ps aux
```

### `top`

Displays running processes along with system resource usage.

```bash
top
```

### `df -h`

Shows available and used disk space in a human-readable format.

```bash
df -h
```

### `free -h`

Shows memory and swap usage.

```bash
free -h
```

### `ip addr`

Displays information about network interfaces and IP addresses.

```bash
ip addr
```

### `ping`

Checks whether another system or IP address can be reached over the network.

```bash
ping 8.8.8.8
```

### `systemctl`

Used to manage and check the status of systemd services.

```bash
systemctl status ssh
```

