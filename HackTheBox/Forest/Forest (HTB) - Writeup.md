This report details the compromise of the Forest (HTB) machine, including enumeration, initial access via AS-REP Roasting, privilege escalation via group membership, and DCSync attack to obtain DA privileges.

---

## Enumeration
### Nmap:
![[Pasted image 20250705175906.png]]
![[Pasted image 20250705175922.png]]
found (FQDN: FOREST.htb.local)

### enum4linux:
![[Pasted image 20250705180713.png]]
found (usernames: sebastien, lucinda, svc-alfresco, andy, mark, santi)

### automated AS-REP Roasting:
![[Pasted image 20250705181609.png]]
found TGT-hash for svc-alfresco

### hashcat:
![[Pasted image 20250705181659.png]]
found (creds: svc-alfresco:s3rvice)

---
## Initial Access
### evil-winrm:
![[Pasted image 20250705181849.png]]
found (user.txt):
![[Pasted image 20250705182120.png]]

---
## PrivEsc
### Manuel Enum:
![[Pasted image 20250705191825.png]]
found (Group: Remote Management Users)

The found group gives our user the privilege to create new users and give them higher priviliges! We create a new user john and add him to high privilege groups.

### Creating a john and giving them high privileges:
![[Pasted image 20250705192014.png]]
### Adding john to privileget groups:
![[Pasted image 20250705192134.png]]

### Uploading PowerView.ps1:
![[Pasted image 20250705192601.png]]
we will need the Add-ObjectACL function from PowerView to give john DCSPrivileges.
### Running PowerView.ps1:
![[Pasted image 20250705195016.png]]

### Using Add-ObjectACL with johns credentials and giving him DCSync rights:
![[Pasted image 20250705193206.png]]


### secretsdump:
![[Pasted image 20250705193436.png]]
using secretsdump with john will dump all credentials on the domain because of johns DCSPrivileges.

### psexec:
![[Pasted image 20250705193829.png]]
we coppied the admin passwd NTLM hash and connected through pass-the-hash technique with psexec.

Found root.txt:
![[Pasted image 20250705195559.png]]

---
## Tools Used
- nmap
- enum4linux
- GetNPUsers.py (Impacket)
- hashcat
- evil-winrm
- PowerView.ps1
- secretsdump.py (Impacket)
- psexec.py (Impacket)
- 
This box reinforced my knowledge of AS-REP Roasting and privilege escalation via group membership and DCSync. It also gave me hands-on experience with PowerView and pass-the-hash attacks.
