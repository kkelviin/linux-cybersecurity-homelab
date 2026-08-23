# Linux Users, Groups and Ownership

## Objective

Understand how Linux identifies users and groups, and how file ownership works on the Debian homelab server.

## Users and Groups

While working on the Debian server, I encountered file information showing:

```text
kelvin    kelvin
```

At first, I was unsure why the same name appeared twice.

I learned that the first `kelvin` represents the **owner** of the file or directory, while the second `kelvin` represents the **group** associated with it.

In this case:

```text
Owner: kelvin
Group: kelvin
```

## File Ownership

Linux assigns ownership to files and directories.

The **owner** is the user account that owns the file, while the **group** is a collection of users that may be given access to the same file or directory.

This means Linux can control access based on:

- The owner
- The group
- Other users on the system

## My User Account

The standard user account created during the Debian installation is:

```text
kelvin
```

I use this account when connecting remotely to the Debian server through SSH from my Mac.

## Why Groups Matter

Groups allow multiple users to share access to files and system resources without giving every user the same level of control.

This is important in Linux administration because permissions can be assigned differently to:

- The file owner
- Members of the file's group
- Everyone else

## What I Learned

From this stage of the homelab, I learned:

- Linux files and directories have an owner
- Linux files and directories also have an associated group
- A username and group name can be the same
- Users can belong to groups
- Ownership is closely connected to Linux permissions

