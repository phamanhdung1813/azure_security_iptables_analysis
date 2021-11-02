# WINDOW SERVER ANALYZE IPTABLES 
### In this project we have 2 Local Router CentOS7 and Ubuntu 20.4 (Yellow field) + 3 Azure Machines on Cloud service.
### We have to configure and establish the connection between cloud machines and local machines by using private IP address.
### Generate the connection via secure PORT and capturing packets by using Wireshark

![Diagram](https://user-images.githubusercontent.com/71564211/139769791-36802c81-ba3b-4379-807d-85e3e44b3c70.JPG)

## WINDOW SERVER PREDEPLOYEMNT CONFIGURATION

1. Flush everything
* sudo iptables -F
* sudo iptables -X
* sudo iptables -t nat -F
* sudo iptables -t nat -X

![1](https://user-images.githubusercontent.com/71564211/139760068-0cc4d597-de2b-452f-abab-9e7d77f36ed3.PNG)
![2](https://user-images.githubusercontent.com/71564211/139760136-92f31c9a-5a7e-4867-bc39-c3e7876a4d68.PNG)

2. RDP and SSH iptables
* sudo iptables -t nat -A PREROUTING -p tcp --dport 3021 -j DNAT --to-destination 172.16.21.4:3389
* sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

3. Allow traffic through dport and sport
* sudo iptables -A INPUT -p tcp -m state --state ESTABLISHED,RELATED -j ACCEPT
* sudo iptables -A INPUT -p tcp --sport 53 -j ACCEPT
* sudo iptables -A INPUT -p udp --sport 53 -j ACCEPT
* sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
* sudo iptables -A INPUT -p udp --sport 123 -j ACCEPT
* sudo iptables -A INPUT -s 168.63.129.16 -j ACCEPT
* sudo iptables -A OUTPUT -d 168.63.129.16 -j ACCEPT
* sudo iptables -A FORWARD -p tcp -s 172.16.21.4 --sport 3389 -j ACCEPT
* sudo iptables -A FORWARD -p tcp -d 172.16.21.4 --dport 3389 -j ACCEPT

4. Drop INPUT, FORWARD and save iptables 
* sudo iptables -P INPUT DROP
* sudo iptables -P FORWARD DROP
* sudo service iptables save

## INSTALLING AND CONFIGURING AZURE WINDOW SERVER
![120960768_341520167271422_2510116761226265687_n](https://user-images.githubusercontent.com/71564211/139760427-b0f03f45-d637-40b9-aff2-63fb9938bdd4.png)
![121161406_349476793139794_5192862947454595393_n](https://user-images.githubusercontent.com/71564211/139760467-36f10aab-e247-4135-845f-5cbffff964af.png)

### Assign your IP address and Select the basic configuration as similar as the picture above

## Wireshark Deployment for PORT network

#### In my project I used port 3021 
* Open CMD on your local machine and get connection via SSH 
* sudo vim /etc/sysctl.conf 
* Then, adding the line below into the config file to allow the IPv4 foward

![3](https://user-images.githubusercontent.com/71564211/139763531-3afc5afc-319f-41b1-a205-9d4558fba6b8.PNG)

*  Press Window + R to open RUN terminal and type mstsc
*  Then, get the remote connection into Azure machine by using the port 3021

![4](https://user-images.githubusercontent.com/71564211/139765533-be03bf7a-f3f0-4ef9-939c-a98104d25e40.PNG)

* Then, accept the connection and type your password. After finished, we have the suceessful connection as the screenshot below:

![5](https://user-images.githubusercontent.com/71564211/139768263-013964ae-7f27-4521-b7ae-d5805c2033ac.PNG)

## INSTALLING AND CONFIGURING AZURE LINUX SERVER
SIMILAR AS WINDOW SERVER INSTALLATION ABOVE, BUT WE HAVE TO ADD THE "SSH KEY" TO ALLOW REMOTE CONNECTION BY USING SSH

![Inked1_LI](https://user-images.githubusercontent.com/71564211/139768960-3d6c3090-dc99-48f6-ad99-2a77abf5aca1.jpg)
![Inked2_LI](https://user-images.githubusercontent.com/71564211/139768992-4263ae56-56f5-4f24-8634-d0c2a2729f66.jpg)

1. On your Linux local machine edit the iptables-services to allow connection on custom port
2. My project used port 4021 
* sudo iptables -t nat -A PREROUTING -p tcp --dport 4021 -j DNAT --to-destination 172.16.21.5:22
* sudo iptables -A FORWARD -p tcp -d 172.16.21.5 --dport 22 -j ACCEPT
* sudo iptables -A FORWARD -p tcp -s 172.16.21.5 --sport 22 -j ACCEPT
* sudo service iptables save

## INSTALLING WIRESHARK TO ANALYZE THE PACKAGE
### Futhermore information, see my document file
#### Do the steps below to start the wireshark recording on your CentOS machine
1. tcpdump -i any -qns0 > /home/user/packetlog.txt
2. SSH into your Azure Linux from your Ubuntu local machine
3. Stop the capture and see the tcpdump packets with source and destination is traveled on port TCP/4021 

* sudo cat /var/log/secure | grep "Invalid user" | head -1 > /home/adpham1/secure.txt
* sudo cat /var/log/secure | grep "possible break in attemps" | head -1 >> /home/adpham1/secure.txt  

![6](https://user-images.githubusercontent.com/71564211/139769618-11f978d3-862a-4b54-8d54-0bd1e5070f30.PNG)

* sudo tcpdump -i any -qnns0 > /home/adpham1/packetlog.txt

* sudo cat packetlog.txt | grep “4021.*tcp” | head -3 | tail -2 > /home/adpham1/packetlog-new.txt

![7](https://user-images.githubusercontent.com/71564211/139769703-9758d5a7-092d-443b-b055-5d79f985086b.PNG)

![8](https://user-images.githubusercontent.com/71564211/139769713-d0a04240-f59e-407b-85cc-401fbc256f77.PNG)

