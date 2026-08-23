# SSH Log Analysis and Failed Login Investigation

## Objective

Learn how to inspect SSH authentication activity on the Debian homelab server, understand successful login events, deliberately generate failed authentication attempts, and investigate them using Linux command-line tools.

## Viewing SSH Logs

After setting up SSH access from my Mac to the Debian server, I inspected the SSH service logs using:

```bash
journalctl -u ssh
```

This displayed activity associated with the SSH service, including successful authentication events, login sessions, and SSH service status messages.

## Successful SSH Login

One of the log entries I observed was similar to:

```text
Accepted password for kelvin from 192.168.100.x port 60910 ssh2
```

From this entry, I learned how to identify:

- the user attempting to log in
- the source IP address
- the source port
- the SSH protocol
- the authentication method
- whether authentication succeeded

In this case, the successful login was from my Mac to the Debian server.

## SSH Session Opened

I also observed an entry similar to:

```text
pam_unix(sshd:session): session opened for user kelvin
```

This means authentication had succeeded and Linux then opened a login session for the user.

This helped me understand that authentication and session creation are separate events.

## SSH Listening on Port 22

The logs also contained messages similar to:

```text
Server listening on 0.0.0.0 port 22
Server listening on :: port 22
```

I learned that SSH uses port:

```text
22
```

by default.

The IPv4 entry shows SSH listening on IPv4 interfaces, while the IPv6 entry shows it listening on IPv6 interfaces.

## SSH Service Events

I also encountered SSH service messages such as:

```text
Stopped ssh.service
Starting ssh.service
```

These entries helped me understand that `journalctl` records both authentication activity and service events.

In my homelab, these events corresponded with times the Debian machine had been shut down or started again.

## Filtering Successful Logins

Instead of reading the entire SSH log output, I learned how to filter for successful logins using:

```bash
sudo journalctl -u ssh | grep "Accepted"
```

This uses `grep` to keep only SSH log entries containing the word `Accepted`.

## Generating a Failed SSH Login

To better understand what failed authentication looks like, I deliberately attempted to connect to the Debian server from my Mac and entered the wrong password twice.

This generated real failed authentication events on the server.

When I inspected the logs, I saw entries similar to:

```text
Failed password for kelvin from 192.168.100.x port 51050 ssh2
Failed password for kelvin from 192.168.100.x port 51050 ssh2
```

## Reading a Failed Authentication Event

A failed SSH entry can be broken down like this:

```text
Failed password for kelvin from 192.168.100.x port 51050 ssh2
```

The entry shows:

- `Failed password` — authentication was unsuccessful
- `kelvin` — the username used in the attempt
- `192.168.100.x` — the source system
- `51050` — the temporary source port used by the client
- `ssh2` — the SSH protocol version

This was my first controlled authentication event generated specifically so I could investigate it from the server side.

## DHCP Observation

During this exercise, I also noticed that the private IP address assigned to my Mac was different from an earlier SSH session.

I learned that this can happen because my router uses DHCP to assign private IP addresses dynamically.

This means the same device may receive a different local IP address at a later time.

## Filtering Failed Login Attempts

I filtered the SSH logs specifically for failed password events using:

```bash
sudo journalctl -u ssh | grep "Failed password"
```

This removed unrelated SSH events and displayed only failed authentication attempts.

## Counting Failed Attempts

I then used:

```bash
sudo journalctl -u ssh | grep "Failed password" | wc -l
```

This command combines several tools:

```text
journalctl
    ↓
Collect SSH logs

    |

grep "Failed password"
    ↓
Keep failed authentication events

    |

wc -l
    ↓
Count the number of matching lines
```

Because I had deliberately entered the wrong password twice, the result was approximately:

```text
2
```

## Log Analysis Pipeline

This introduced me to a simple but important log-analysis pattern:

```text
logs | filter | count
```

Instead of manually reading every log entry, command-line tools can be chained together to narrow down and summarize the information I need.

This is a basic version of the type of searching and filtering performed at a much larger scale by security monitoring and SIEM platforms.

## Thinking Like an Analyst

The exercise also helped me understand how a security analyst could investigate suspicious SSH activity.

For example, if the server showed repeated failed attempts from an unfamiliar address using several usernames, useful questions would include:

- What system is generating the attempts?
- How many attempts were made?
- Which usernames were targeted?
- Did any authentication eventually succeed?
- Is the activity expected or suspicious?

This moved the exercise beyond simply reading logs and toward understanding how authentication events can be investigated.

## Why SSH Logs Matter

SSH logs can help an administrator or security analyst investigate:

- successful remote logins
- failed authentication attempts
- unusual source systems
- repeated password attempts
- unexpected usernames
- SSH service activity
- possible unauthorized access attempts

## What I Learned

From this stage of the homelab, I learned:

- `journalctl -u ssh` can be used to inspect SSH service logs
- successful authentication appears as `Accepted password`
- failed authentication appears as `Failed password`
- SSH logs show usernames, source addresses and source ports
- `pam_unix(sshd:session)` can indicate that a session was opened
- SSH listens on port 22 by default
- DHCP can cause a device's local IP address to change
- `grep` can filter authentication logs
- `wc -l` can count matching events
- Linux commands can be chained together for log analysis
- controlled test events can be useful for learning how security logs behave
- authentication logs can be investigated from a blue-team perspective

