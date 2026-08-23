# SSH Setup

## Objective

Configure and verify remote access to the Debian homelab server using SSH from my Mac.

## Why SSH Is Used

SSH allows me to securely access and manage the Debian server remotely over my local network.

Instead of working directly on the physical HP desktop, I can administer the Linux system from my Mac using the terminal.

## SSH Service

SSH was installed on the Debian server during the initial setup.

The SSH service can be checked using:

```bash
systemctl status ssh
```
This confirms whether the SSH service is running and available for remote connections.

# Network Configuration
The Debian server and my Mac are connected to the same local network.
The server's network information can be checked using:
```bash
ip addr
```
The server uses a private local IP address, which is not published in this repository.

# Connecting From My Mac
From the Terminal application on my Mac, I connect to the Debian server using the SSH command:
```bash
ssh kelvin@SERVER_IP
```
Where:
- kelvin is the Linux user account on the Debian server.
- SERVER_IP represents the private IP address assigned to the Debian machine.
After entering the correct password, I am given access to the Debian command line remotely.

# Verification
After connecting successfully, I verified that I was working on the Debian server by running basic commands such as:
```bash
pwd
whoami
hostname
```
These commands help confirm the current user, system, and working environment.

# Result
SSH remote access was successfully configured and tested.
The Debian homelab server can now be administered remotely from my Mac without needing direct physical access to the machine.

## What I Learned
This stage helped me understand:
- What SSH is used for
- How remote Linux administration works
- How a client machine connects to a Linux server
- The importance of IP addressing in remote access
- How to verify an SSH connection
- The difference between working locally and remotely

