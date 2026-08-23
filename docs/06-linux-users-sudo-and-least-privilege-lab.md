# Linux Users, Sudo and Least Privilege Lab

## Objective

Practically understand Linux user accounts, administrative privileges, file permissions, and the security principle of least privilege.

This lab builds on previous concepts around users, groups, ownership, and permissions by creating a separate test account and observing how access changes between users.

## Creating a Test User

To understand privilege separation, I created a second Linux user account without administrative privileges.

The command used was:

```bash
sudo adduser analystkelvin
```

During creation, Linux requested additional account information such as:

- Full Name
- Room Number
- Work Phone
- Home Phone
- Other information

For this lab, the optional fields were left empty.

## Verifying the User

The new account was verified using:

```bash
id analystkelvin
```

This displayed information about the account including:

- User ID (UID)
- Group ID (GID)
- Groups associated with the account

Linux identifies users internally using numerical IDs rather than only usernames.

Example:

```text
uid=1001(analystkelvin)
gid=1001(analystkelvin)
```

## Switching Between Users

Instead of creating a new SSH session, I switched users from my existing session using:

```bash
su - analystkelvin
```

The current user was verified with:

```bash
whoami
```

The output confirmed that I was operating as:

```text
analystkelvin
```

## Testing Sudo Privileges

The purpose of this test user was to demonstrate the difference between a normal user and a privileged user.

When attempting:

```bash
sudo whoami
```

as `analystkelvin`, the command failed because the account had not been granted sudo privileges.

This demonstrated that not every Linux account can perform administrative actions.

## Testing Protected Files

I also tested access to the protected password information file:

```bash
cat /etc/shadow
```

The `analystkelvin` account could not access this file.

The system returned:

```text
Permission denied
```

This is expected because `/etc/shadow` contains sensitive authentication information and should only be accessible by privileged users.

## Comparing With Kelvin's Account

After returning to my normal account:

```bash
exit
```

I confirmed I was back as:

```text
kelvin
```

The same protected file could then be accessed using administrative privileges:

```bash
sudo cat /etc/shadow
```

This demonstrated the difference between:

```text
analystkelvin
      ↓
normal user
      ↓
limited access


kelvin
      ↓
sudo privileges
      ↓
administrative access
```

## Understanding File Ownership

To understand ownership and permissions practically, I created a test file:

```bash
touch secret.txt
```

I checked its ownership and permissions using:

```bash
ls -l secret.txt
```

The output showed:

```text
-rw-r--r-- 1 kelvin kelvin
```

The important sections were:

```text
OWNER      GROUP      OTHERS

rw-        r--        r--
```

The file owner was:

```text
kelvin
```

and the group was:

```text
kelvin
```

## Adding Content to the File

I added content using:

```bash
echo "Infosec lab secret" > secret.txt
```

The contents were verified using:

```bash
cat secret.txt
```

## Testing Access as Another User

I switched back to:

```bash
su - analystkelvin
```

and attempted:

```bash
cat /home/kelvin/secret.txt
```

Initially, access was successful.

This was because the permissions allowed:

```text
OTHERS
r--
```

meaning any other user on the system could read the file.

This demonstrated that Linux permissions work exactly according to the rules assigned, not based on assumptions about ownership.

## Restricting File Access

I returned to the `kelvin` account and changed the file permissions:

```bash
chmod 600 secret.txt
```

The permissions changed from:

```text
-rw-r--r--
```

to:

```text
-rw-------
```

The new permission structure became:

```text
OWNER      GROUP      OTHERS

rw-        ---        ---
```

Only the owner could read and write the file.

## Testing the New Permissions

After switching back to:

```bash
su - analystkelvin
```

I attempted again:

```bash
cat /home/kelvin/secret.txt
```

This time Linux returned:

```text
Permission denied
```

The permission change successfully prevented another user from reading the file.

## Understanding chmod 600

Linux permissions can also be represented numerically.

Each permission has a value:

```text
Read    = 4
Write   = 2
Execute = 1
```

Values are added together:

```text
Read + Write = 4 + 2 = 6
Read + Execute = 4 + 1 = 5
Read + Write + Execute = 4 + 2 + 1 = 7
```

Permissions are applied in three sections:

```text
OWNER   GROUP   OTHERS
```

Therefore:

```bash
chmod 600 secret.txt
```

means:

```text
6       0       0

rw-     ---     ---
```

Only the owner has read and write permissions.

## Common Permission Examples

Some common Linux permission combinations include:

```text
600  rw-------     private file

644  rw-r--r--     owner writes, others read

700  rwx------     private executable/directory

755  rwxr-xr-x     owner full access, others read/execute
```

## Security Lessons

This lab demonstrated the practical meaning of least privilege.

The difference between users was:

```text
analystkelvin
      ↓
limited account
      ↓
cannot access protected resources


kelvin
      ↓
sudo privileges
      ↓
can perform administrative tasks
```

The file permission experiment also showed that access depends on:

- ownership
- groups
- permission settings

not simply who created the file.

## What I Learned

From this lab, I learned:

- how to create Linux users
- how UID and GID identify accounts
- how normal users differ from privileged users
- why not every account should have sudo access
- how sudo temporarily provides administrative privileges
- how Linux protects sensitive files such as `/etc/shadow`
- how ownership affects file access
- how `chmod` changes permissions
- how numeric permissions work
- how least privilege improves system security
