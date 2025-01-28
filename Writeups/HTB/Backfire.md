We recently tackled the second machine of HackTheBox Season 7: “BackFire.” Although labeled as a medium-level Linux box, I’d rate it closer to hard or even insane difficulty. Solving this machine took me an entire day.

# Availability of the Article

This article will be public for 24 hours, after which it will become members-only. To access such articles for free, subscribe to the newsletter and follow this account so you don’t miss out.

**Note:** This write-up adheres to HTB guidelines and will not serve as a full walkthrough. Instead, it will provide hints and resources to help you achieve both the user and root flags.

# Initial Enumeration

Start with a basic `nmap` scan:

nmap -A -p- 10.10.11.49

## Key Observations:

- **Port 8000**: Reveals a Havoc C2 profile.
- Found files: `disable_tls.patch` and `havoc.yaotl`.

# Exploitation

Identifying the Havoc C2 profile led to research into relevant exploits. A key resource was: [GitHub — chebuya/Havoc-C2-SSRF-poc](https://github.com/chebuya/Havoc-C2-SSRF-poc): This exploit targets CVE-2024–41570. Combining it with a Bash reverse shell payload established initial access as:

ilya@backfire

The user flag can be retrieved at this stage.

## Shell Stabilization:

To stabilize the initial shell, add your SSH key to `~/.ssh/authorized_keys` and log in using that key.

# Privilege Escalation: User to Root

Escalation required moving through an intermediate user, **serge.** Enumeration under `ilya` revealed a crucial hint:

"Sergej said he installed HardHatC2..."

This pointed to HardHatC2.

## Setting Up HardHatC2

1. Install dependencies:

wget https://packages.microsoft.com/config/debian/11/packages-microsoft-prod.deb -O packages-microsoft-prod.deb  
sudo dpkg -i packages-microsoft-prod.deb  
sudo apt update  
sudo apt install -y dotnet-sdk-7.0

2. Clone and run HardHatC2:

git clone https://github.com/DragoQCC/HardHatC2  
cd HardHatC2/TeamServer  
dotnet run -- 127.0.0.1:5000

3. Forward the necessary ports (5000 and 7096) and log in with default credentials.

Using HardHatC2, create a new user and obtain an interactive shell as `serge.`

## Privilege Escalation: Serge to Root

The final step required exploiting `iptables` with elevated privileges. Refer to [Shielder’s guide](https://www.shielder.com/) for using `sudo iptables` to escalate privileges.

Modified command:

sudo -u root /usr/sbin/iptables -A INPUT -i lo -j ACCEPT -m comment --comment $'\nssh-ed25519 <your-public-key> user@ubuntu\n'

Once your SSH key is added, log in as `root` and capture the root flag.

# Conclusion

This was a challenging machine, requiring a mix of enumeration, research, and exploitation of multiple tools and frameworks. I hope these hints and resources help you achieve both flags. Happy hacking!