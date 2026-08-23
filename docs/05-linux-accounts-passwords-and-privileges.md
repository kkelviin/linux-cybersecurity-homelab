# Linux Accounts, Passwords and Privileges

## Objective

Understand how Linux manages user and system accounts, protects password information, and separates standard user access from administrative privileges.

## Exploring Linux Accounts

While working on the Debian homelab server, I began exploring how Linux stores information about users and system accounts.

I inspected:

```text
/etc/passwd
```

using:

```bash
cat /etc/passwd
```

The output contained many account entries, including accounts I had created as well as accounts created automatically by the operating system.

At first, the amount of information displayed was unfamiliar, so I began learning what the different types of accounts represented.

## My Standard User Account

The user account I created during the Debian installation is:

```text
kelvin
```

This is the account I normally use when working on the Debian server and when connecting remotely from my Mac through SSH.

Using a standard user account means I do not automatically have unrestricted administrative access to the entire system.

## The Root Account

I also encountered:

```text
root
```

I learned that `root` is the main administrative account in Linux.

The root account has significantly greater privileges than a normal user and can make system-wide changes.

This helped me understand why `root` and my normal `kelvin` account serve different purposes.

## System and Service Accounts

While inspecting account information, I noticed many accounts that I had never created myself.

I learned that Linux creates accounts for services and background processes in addition to normal human users.

This means that an entry inside `/etc/passwd` does not necessarily represent a person who logs into the server.

One of the service-related accounts I encountered was:

```text
systemd-timesync
```

This account is associated with a system service rather than a normal interactive user.

## Password Information

As I continued exploring Linux accounts, I learned that normal readable passwords are not stored directly inside `/etc/passwd`.

Sensitive password-related information is kept separately in:

```text
/etc/shadow
```

This file has more restrictive access because it contains security-sensitive account information.

## Password Hashes

Linux does not normally store a user's password as readable plain text.

Instead, password information is represented using a **hash**.

A password hash is a one-way representation used when verifying authentication credentials.

This means someone viewing the account information should not simply be able to read a user's original password.

## Understanding a Shadow Entry

While exploring this area, I encountered an entry similar to:

```text
systemd-timesync:!*:20679:::::1:
```

At first, the format looked confusing.

I learned that this was account information associated with the `systemd-timesync` service.

The password field contained:

```text
!*
```

which indicated that this service account was not intended to authenticate using a normal password like my `kelvin` account.

This helped reinforce the difference between normal user accounts and accounts created for system services.

## Standard User vs Administrative Privileges

During this part of the homelab, I also questioned why I could not simply give my own account unrestricted access to everything.

I learned that separating normal user access from administrative access is an important Linux security principle.

My standard account:

```text
kelvin
```

is used for normal activity, while privileged administrative access should only be used when it is actually required.

Giving a normal account unrestricted access could increase the impact of:

- Accidental commands
- Incorrect system changes
- Malicious software
- A compromised user account

## Sudo

I was also introduced to the concept of:

```bash
sudo
```

`sudo` allows an authorized user to perform an administrative command when elevated privileges are required instead of permanently operating as the root account.

The general format is:

```bash
sudo COMMAND
```

At this stage, my focus was on understanding what `sudo` and elevated privileges mean rather than making advanced privilege or `sudoers` configuration changes.

## Principle of Least Privilege

This introduced me to an important cybersecurity concept called **least privilege**.

The principle of least privilege means that users and processes should only receive the level of access necessary to perform their required tasks.

Instead of giving every account full access, Linux permissions and privileges can limit what each account is allowed to do.

## How the Concepts Connect

At this stage, I began seeing how several Linux security concepts work together:

```text
User Account
Ownership
Permissions
Privileges
Access to System Resources
```

My earlier work with users, groups, ownership, and `r`, `w`, and `x` permissions helped provide the foundation for understanding why Linux separates normal users, service accounts, and administrative access.

## What I Learned

From this stage of the homelab, I learned:

- Linux stores basic account information in `/etc/passwd`
- `kelvin` is my standard Linux user account
- `root` is the main administrative account
- Linux creates additional accounts for system services
- Not every Linux account represents a human user
- Sensitive password information is separated from `/etc/passwd`
- `/etc/shadow` contains protected password-related account information
- Linux does not normally store passwords as readable plain text
- Password hashes are used as part of password authentication
- Service accounts may have normal password authentication disabled
- Standard users should not automatically have unrestricted system access
- `sudo` is used to perform privileged operations when authorized
- Least privilege is an important Linux and cybersecurity security principle