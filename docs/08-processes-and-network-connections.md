# Processes and Network Connections

## Objective

Understand what is running on the Debian homelab server, which services are listening for network connections, and how active network sessions can be connected back to specific processes.

After investigating SSH authentication logs, I moved from asking:

> Who is logging into the server?

to:

> What is actually running on the server, and what network activity is taking place?

This is an important skill in system administration and cybersecurity investigations.

---

# Viewing Running Processes

I used:

```bash
ps aux
```

to display the processes currently running on the Debian server.

`ps` means **process status**.

The output provides information such as:

```text
USER       PID   %CPU   %MEM   COMMAND
```

## Understanding Process Information

### USER

The `USER` column shows which Linux account is running the process.

Examples:

```text
root
kelvin
```

Some system processes run with root privileges, while user processes may run under normal accounts.

---

### PID

`PID` means **Process ID**.

Every running process on Linux receives a unique number.

The PID allows administrators to identify and investigate a specific process.

---

### CPU and Memory Usage

The process list also displays:

```text
%CPU
%MEM
```

These show how much system resources each process is consuming.

This is useful when troubleshooting issues such as:

- High CPU usage
- High memory consumption
- Unexpected resource usage

---

### COMMAND

The `COMMAND` field shows the program or service that is running.

This connects a process ID to the actual application or service behind it.

---

# Monitoring Processes in Real Time

I also used:

```bash
top
```

Unlike `ps aux`, which provides a snapshot, `top` continuously updates.

It displays:

- Running processes
- CPU usage
- Memory usage
- Process IDs
- Users running processes

During testing, the Debian server showed:

```text
CPU: approximately 99.8% idle
```

and had plenty of available memory.

This confirmed that the lightweight Debian installation was running comfortably on the available hardware.

To exit `top`:

```text
q
```

---

# Inspecting Listening Network Services

To see which services were waiting for network connections, I used:

```bash
sudo ss -tulpn
```

This command shows listening TCP and UDP services and the processes responsible for them.

## Understanding the Command

```text
ss  → inspect sockets

-t  → TCP connections

-u  → UDP connections

-l  → listening services

-p  → show associated processes

-n  → show numerical addresses and ports
```

The purpose of the command is:

> Show me the network services listening on this machine and tell me which processes own them.

---

# SSH Service Investigation

The main TCP service identified was:

```text
0.0.0.0:22
[::]:22
```

This showed that SSH was listening on port:

```text
22
```

The responsible process was:

```text
sshd
```

with an associated PID.

This created an important relationship:

```text
Program
   ↓
sshd

Process ID
   ↓
PID

Protocol
   ↓
TCP

Port
   ↓
22

Purpose
   ↓
Remote SSH administration
```

When my Mac connects using:

```bash
ssh kelvin@SERVER_IP
```

it connects to:

```text
SERVER_IP:22
```

---

# Understanding Ports

I learned that an IP address identifies a machine, while ports identify services running on that machine.

Example:

```text
Debian Server

192.168.100.40

      |
      |
      ├── :22
      |      └── SSH
      |
      ├── :80
      |      └── HTTP
      |
      └── :443
             └── HTTPS
```

A simple analogy:

```text
IP address = building address

Port = specific service door
```

---

# DHCP Network Services

The server also showed UDP services associated with:

```text
dhcpcd
```

These included:

```text
UDP port 68
UDP port 546
```

These are related to DHCP network configuration.

Port 68 is associated with IPv4 DHCP client activity.

Port 546 is associated with DHCPv6 client activity.

These services were expected because the server needs network configuration to communicate on the local network.

---

# Establishing a Network Baseline

At this stage, I established what normal network activity looked like on the Debian server.

The baseline was:

```text
Debian Homelab Server

|
├── TCP 22
|      |
|      └── sshd
|              |
|              └── Remote administration
|
├── UDP 68
|      |
|      └── dhcpcd
|              |
|              └── IPv4 network configuration
|
└── UDP 546
       |
       └── dhcpcd
              |
              └── IPv6 network configuration
```

This introduced the cybersecurity concept of a **baseline**.

A baseline represents normal expected activity.

Knowing the normal state of a system makes it easier to identify unusual activity later.

---

# Viewing Active Connections

After viewing listening services, I checked active TCP connections using:

```bash
sudo ss -tnp
```

The difference is that the previous command used:

```text
-l
```

which means:

```text
listening
```

Removing it shows current active TCP connections.

---

# Understanding an Established SSH Connection

Because I was connected to the Debian server from my Mac through SSH, I observed an established connection:

```text
ESTAB

192.168.100.40:22
        |
        |
        ↓
192.168.100.7:51059
```

From the server's perspective:

```text
Mac
192.168.100.7

      |
      |
      | SSH connection
      |

Debian Server
192.168.100.40:22
```

`ESTAB` means:

```text
Established
```

meaning an active TCP connection currently exists.

---

# Listening vs Established Connections

This helped me understand the difference between:

## Listening Service

Example:

```text
0.0.0.0:22 LISTEN
```

Meaning:

> SSH is waiting for incoming connections.

---

## Established Connection

Example:

```text
192.168.100.40:22 ESTAB
192.168.100.7:51059
```

Meaning:

> A device is actively connected to SSH right now.

---

# IP → Port → Connection → Process → PID → User → Logs

This stage connected many concepts together:

```text
IP Address
     ↓
Port
     ↓
Connection
     ↓
Process
     ↓
PID
     ↓
User
     ↓
Logs
```

This is the same thinking pattern used during security investigations.

For example:

A suspicious open port leads to:

1. Identify the port
2. Find the process using it
3. Find the PID
4. Identify the user running it
5. Review logs
6. Determine if the activity is expected

---

# Security Relevance

Understanding processes and network connections helps answer questions such as:

- What services are running?
- Which ports are exposed?
- Who is connected?
- Which process owns a connection?
- Is this activity expected?
- Has the server changed from its normal baseline?

This is an important foundation for incident response and security monitoring.

---

# What I Learned

From this stage of the homelab, I learned:

- How to view running processes with `ps aux`
- How to monitor system activity with `top`
- How Linux assigns PIDs to processes
- How to inspect listening services with `ss -tulpn`
- How SSH operates through TCP port 22
- How DHCP services appear in network connections
- What a system baseline means
- The difference between listening and established connections
- How to connect network activity back to processes and users
- How analysts investigate unexpected services and connections
