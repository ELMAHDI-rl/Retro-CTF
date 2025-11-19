# Retro CTF 
This is a quick writeup for Retro CTF .

after THM provided us with machine's IP(it's gonna be diffrent for you) i added the hostname to the hosts file as following command :

   echo "10.10.4.77 retro.thm" > /etc/hosts .
   
i kicked of with nmap scan to discover which ports are open on the machine : 

<img width="758" height="256" alt="Screenshot 2025-11-14 205746" src="https://github.com/user-attachments/assets/fbeb191a-c7a1-4041-bf49-b9c85eae166c" />

  .-Pn option to skip ping check (ICMP packets) to be more stealthier
  
  .-sV is for service version detection scan 
  
i found port 80 is open and Microsoft IIS as it's http srever 
also port 3389 is open which is for RDP 

so this is huge indicator that the machine is running Windows 

as initial process i opened the web page and this is what i found :

<img width="928" height="396" alt="Screenshot 2025-11-19 210023" src="https://github.com/user-attachments/assets/f2198bc3-dc30-4ef8-b3ed-b201648e6d4d" />

since there is no inputs field or any vector to go for , I started a directory brute force with dirsearch tool with dirbuster's medium wordlist as following :

<img width="1449" height="375" alt="Screenshot 2025-11-14 220406" src="https://github.com/user-attachments/assets/7769f9a3-43a2-411b-b4fd-24d6c7e2702e" />

i found /retro directory with 301 status code for redirection so i tried it out .
and as expected it's web page , as initial approch i started with browsing the site to look for any threat vector or useful information and this what i found :

<img width="918" height="684" alt="Screenshot 2025-11-14 220302" src="https://github.com/user-attachments/assets/55de51d5-8d64-457e-8474-2f138dc3f717" />

Wade user posted a comment leaving spicy information as parzival word since he doesn't know how to spell it .

based on what i've enumerated previously RDP port is open , i tried to connect to RDP as wade user and parzival as password with xfreerdp tool : 

 .$ xfreerdp /u: wade /v:10.10.4.77 /p: parzival
 
and indeed i got RDP session :
<img width="1765" height="733" alt="Screenshot 2025-11-14 220203" src="https://github.com/user-attachments/assets/356498e2-bba5-4a9e-a7d8-ae975717c8be" />

after enumerating users,groups with powershell Wade user is not part of Adminitrators group
so i need to elevate privilges .
after enumerating services,process,network information i didn't anything interesting , but since am connected as GUI with RDP i opened chrome browser and this what i found :

<img width="445" height="209" alt="Screenshot 2025-11-14 220553" src="https://github.com/user-attachments/assets/f28ffe4a-e404-4566-a50a-38a1af9dea14" />

i found in the search history NIST site for CVE-2019-1388 description :

<img width="1109" height="559" alt="Screenshot 2025-11-14 220651" src="https://github.com/user-attachments/assets/693d30a3-c902-42ca-ba04-9c8e17ac5c1d" />

this is privilege escalation vulnerability and effecting windows server 2016 which what the machine is running .
i found poc of it in the following github repo:  https://github.com/nobodyatall648/CVE-2019-1388

after going through the poc i escalated my privilges :

<img width="372" height="54" alt="Screenshot 2025-11-14 214528" src="https://github.com/user-attachments/assets/d4f24e30-4023-459a-af25-15588d465414" />

and i got the root flag:

<img width="408" height="27" alt="Screenshot 2025-11-14 215429" src="https://github.com/user-attachments/assets/45154a1e-c9a2-4529-9b11-cc47c9d29fcf" />

