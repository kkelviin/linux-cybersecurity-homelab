# Linux File Permissions

## Objective

Understand how Linux controls access to files and directories using permissions.

## Permission Categories

I learned that Linux permissions are separated into three categories:

- Owner – the user who owns the file or directory
- Group – users who belong to the group associated with the file
- Others – users who are neither the owner nor members of the assigned group

This builds on the ownership concepts covered previously.

## Read, Write and Execute

Linux commonly represents permissions using three letters:

```text
r = read
w = write
x = execute
```

### Read

`r` allows a user to read or view the contents of a file.

### Write

`w` allows a user to modify the contents of a file.

### Execute

`x` allows a file to be run as a program or script when the file is executable.

For directories, execute permission also affects whether a user can access or move through the directory.

## How Permissions Are Applied

Permissions can be different for each category:

```text
Owner
Group
Others
```

This means the owner of a file may have more access than members of the group, while other users may have even less access.

## Why Not Give Everyone Full Access?

During this stage, I questioned why I would not simply give my own account full access to everything.

I learned that Linux permissions are designed to limit unnecessary access.

Giving users unrestricted permissions can increase the impact of mistakes or security incidents.

A more secure approach is to give a user only the level of access required for the task being performed.

This is closely related to the security principle of **least privilege**.

## Why Permissions Matter in Cybersecurity

File permissions are an important part of Linux security because they help control:

- Who can view sensitive files
- Who can modify files
- Who can execute programs
- Which users can access particular directories
- How much access a compromised account may have

## What I Learned

From this stage of the homelab, I learned:

- Linux permissions use `r`, `w`, and `x`
- Permissions are applied separately to the owner, group, and others
- Execute permission does not simply mean "full access"
- File ownership and permissions work together
- Giving every user unrestricted access is not good security practice
- Limiting access helps protect the Linux system