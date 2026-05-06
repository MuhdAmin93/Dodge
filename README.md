# Dodge
Dodge thm  beginner friendly walkthrough
Test your pivoting and network evasion skills.

**Enumeration**

First we start with an nmap scan to see which ports and services are running 


Nmap Scan 




Using the command **nmap -sC -sV [ip]**





<img width="732" height="431" alt="first nmap scan" src="https://github.com/user-attachments/assets/a4d2d149-1d49-4945-9c54-ae37bf274134" />











Great with the nmap scan we can now see that three ports are open:


22/SSH



80/HTTP



443/HTTPS




**Subdomains**




Nmap also gave a list of subdomain




<img width="730" height="345" alt="nmap scan showing us dns" src="https://github.com/user-attachments/assets/9fd4c952-72ad-47b2-8611-def5f94bad6d" />



Now lets add these subdomans to our **/etc/hosts****** folder using the command **nano /etc/hosts**





<img width="719" height="294" alt="etc hosts" src="https://github.com/user-attachments/assets/753a5acf-aa85-4f66-81a4-df21ff70521a" />






Out of these subdomains we can see some of them have some of the subdomains have some information in them and not just 404 error





**https://www.dodge.thm** is the main website 





<img width="911" height="787" alt="dodge" src="https://github.com/user-attachments/assets/57880b27-cfc4-4b5b-bef3-38d8ab1167c3" />





We can also see the **https://dev.dodge.thm**






<img width="909" height="821" alt="dev endpoint" src="https://github.com/user-attachments/assets/431b0f50-34cb-465b-b602-0eb4b62ad9ec" />






We can also see the **https://netops-dev.dodge.thm**






<img width="930" height="828" alt="empty netops" src="https://github.com/user-attachments/assets/3e9feb8c-b33d-4c82-b992-28694fa291e6" />



Interesting here we can see the page is blank so let's look at the source code and see what is on the website 




<img width="450" height="267" alt="source code interesting firewall" src="https://github.com/user-attachments/assets/b0522b87-8329-4bf6-9c12-caae80ce44d0" />




You can see from the source code that in the styling the display is none, so that is why we are unable to see anything so let's remove the styling of display none to see what is on the website










<img width="907" height="789" alt="changing display to nothing" src="https://github.com/user-attachments/assets/d33f1a99-0211-4720-b7a5-62d7cec6804d" />





As you can see this is a form page and we can upload a file into it, the first thought that comes to my mind is uploading an exploit to get a reverse shell but unfortunately the exploit did not run.




We can also see there is a firewall.js file 






<img width="450" height="267" alt="source code interesting firewall" src="https://github.com/user-attachments/assets/e93240cc-4847-4f91-9387-9ae83d0c8fce" />



So let's see what is on the firewall.js file 






<img width="921" height="596" alt="finding firewall dir" src="https://github.com/user-attachments/assets/4603e736-1eeb-44f9-a754-db5ee00b7432" />




ok nothing interesting but if you notice carefully we have another php file that is been fetched firewall10110.php






So lets check it out and see what it contains





<img width="945" height="695" alt="accessing firewall10110 dir" src="https://github.com/user-attachments/assets/0f0667d3-358b-4b2e-af2f-08ecde29b070" />



Ohh wowww it's a page to update firewall rules using ufw (which is a user-friendly interface for managing firewall rules on linux).
We can run simple commands like **sudo ufw status** and for some unknown reason I get invalid command. If we also look carefully we can see that it denies all traffic on port 21 which runs ftp so lets try **sudo ufw allow ftp** and it works nice, also let's try to disable  the firewall and see if it will work using the command **sudo ufw disable**





<img width="713" height="683" alt="running command to disable firewall" src="https://github.com/user-attachments/assets/5fbefc7b-cde8-44a2-8f2e-1376e2ec40b6" />






And it worked now you can see below it says firewall stopped and disabled 








<img width="658" height="608" alt="firwall disabled" src="https://github.com/user-attachments/assets/2dc85163-3491-4bd0-95da-3cf0be879d1c" />



Now to confirm that the firewall is diabled lets rum another nmap scan to see if this time port 21 will be open







<img width="721" height="416" alt="second nmap scan with ftp" src="https://github.com/user-attachments/assets/90e225a0-e96b-44af-a3c3-b01ff0ca0717" />




Great so now ftp port 21 is open, so let connect to the machine using fhe ftp command **ftp dodge.thm** and using the username **anonymous**









<img width="700" height="395" alt="ftp into dodge" src="https://github.com/user-attachments/assets/6ed21ab7-cb85-4c88-881a-cf53c088f02e" />





Now if we run the command **ls** we can see the file user.txt which holds our first flag but if we try to **get user.txt**  we can't because we don't have the permission so let's run the command **ls -la** to see the hidden files 










<img width="723" height="432" alt="trying to see hidden files in ftp" src="https://github.com/user-attachments/assets/b8613859-7471-414f-94d2-32d1b3db1e42" />



As you can see now we have .ssh directory let's cd into it and now we have our **id_rsa** key but unfortunately we cannot get it because we don't have the permission but lucky for us we can get the **id_rsa_backup** key and the **authorized_key** so let's get them using the command **get authorized_keys** and **get id_rsa_backup **respectively








<img width="729" height="411" alt="authroized key finding the user challenger" src="https://github.com/user-attachments/assets/0d1414c3-ceca-4d86-9561-4e269ebd9c3b" />




After getting the authorized_keys, now I can cat the file and see its contentwhich I can see **challenger@thm** which I am guessing is the name of the user with the ssh key





So let's try to login with the name challenger and our private key intially I tried to login ithout changing the permission and I got an error permission denied so I changed the permission using the command **chmod 400 id_rsa_backup** and tried it again and vaula we are in.











<img width="745" height="414" alt="sshing into challenger" src="https://github.com/user-attachments/assets/da6800dc-de56-42f6-90c4-c5ccd349d6a6" />





Now we can run **ls** again and cat our user.txt file and we have our first flag 












<img width="585" height="124" alt="first flag" src="https://github.com/user-attachments/assets/db9c43ce-fc03-4140-a2a7-1ea2b620c856" />





Greatttt job!!!!! we now have a flag, now for the root.txt file we definetly have to escalate ou privilege to be able to access it so let's get to work.




If you remembered when we ran the command **ls -la** we also had a bash history directory so let's take a look at it and see what it contains 






<img width="656" height="217" alt="listing hidding files in challenger" src="https://github.com/user-attachments/assets/8d8248b4-51cb-4ab0-9e31-570cc10e1af8" />














<img width="605" height="251" alt="listing bash history" src="https://github.com/user-attachments/assets/93dbd560-6e02-49d6-ba06-e9a889ab0f45" />


Now when we cat it we can see two interesting php files which I am guessing they are related to our site. 



lets find the files and see what their content using the find command **find / -type f -name setup.php 2>/dev/null** we found the file but we don't have permission to read it so let's try the other file **find / -type f -name posts.php 2>/dev/null** and now we have the permission to read this file so let's cat it and see it's content and interestingly we have base64 encoded string 












<img width="727" height="435" alt="interesting base64 encoding" src="https://github.com/user-attachments/assets/5490ca0c-28be-4f4a-8227-86948de90ebb" />



Now let's decode the string using cyberchef and see 













<img width="956" height="801" alt="decoding and getting cobra info" src="https://github.com/user-attachments/assets/fe730c5e-6dd9-4f26-a3d9-007f89ab7966" />




Now we have an ssh username and password so lets escalate our privilege to cobra







<img width="694" height="436" alt="successful cobra login" src="https://github.com/user-attachments/assets/ef591b8b-9e47-4a23-9bbb-4abb6711ad95" />




Now I'm logged in as cobra but still I can't cd into root also I checked it's bash_history but nothing important so let's check our sudo privilege using the command **sudo -l**










<img width="720" height="167" alt="checking our sudo privilege" src="https://github.com/user-attachments/assets/29624950-6719-41ce-896b-4da880ddb8be" />




Now we can see we can access **/usr/bin/apt** with sudo, after some playing around I decided to go to https://gtfobins.org for payload inspo
selected apt 









<img width="1740" height="746" alt="finding exploit form gitbin" src="https://github.com/user-attachments/assets/398cba5a-e801-4a03-bb1d-421958444480" />




And selected the last exploit so let's try it out and see if it would work







<img width="725" height="80" alt="first try of root escalation" src="https://github.com/user-attachments/assets/3e7506aa-e236-4010-8ad6-1570b8e77a9e" />



And unfortunately I just get an error and that is because we do not have sudo privilege to **apt-get** but we only have sudo privilege to **apt** so after removing the get and just trying the exploit **sudo apt update -o APT::Update::Pre-Invoke::=/bin/sh**, we now have a root shell and can finally access our flag








<img width="710" height="200" alt="root flag" src="https://github.com/user-attachments/assets/6ac718bf-73c2-447d-9df5-416746fb63ef" />


Great job if you came all this way we now have learnt how to pivot and evade network firewalls 
























































