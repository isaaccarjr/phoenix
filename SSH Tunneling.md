## BLUF


## Five W's
- Who: Members looking to start the Title 10/Title 50 pipeline
- What: Using Local/Remote port forwarding to establish an encrypted tunnel  
- When: Creating an encrypted tunnel to allow certain connections to maneuver through those tunnels 
- Where: Via Unix/Linux CMD line as well as Windows 10 CMD line 
- Why: The main benefit to Local/Remote port fowarding is directing certain traffic travel through encrypted tunnels you have established. 
With the ablitly to extend those tunnels to reach hosts further into the network.
           
           --Local Port Fowarding--
T1 --> T2
note: The Arrow above indicates the direction in which the port is being forwarded. If you are working from your current local host (T1) then the port will be 
forwarded to a remote machine(T2) 

ssh root@<LocalIP> -L <LocalIP><Port#>:<TargetIP>:<Target Port> 
    
    ![GitHub Logo](C:\Users\lessl\Downloads\Remote_Forwarding_illistration.jpg)
    ***SEE Local_Forwarding_illistration.jgp FOR VISUAL AID ***

T1 192.168.159.134 --> T2 192.168.159.132

From T1: 
ssh root@127.0.0.1 -L 127.0.0.1:4444:192.168.159.132:22  (the Kickoff)
 >ssh root@127.0.0.1 -p 4444  
  >>Access to T2 

-L Explained:  
The -L flags specifies a local forwarding. This states that the TCP client and SSH client are both on the same local machine.
When using the flag -L in regards to T1 -> T2 this means that the operator is creating the start of the SSH pipe (The entrance) 
at T1 (local machine IP). The pipe will start at T1 and create an ssh connection with a remote machine and a defined port. The 
SSH pipe will end at T2 and port forward the traffic starting at T1 and sending it to T2 (remote machine IP) to a port you defined. 
           
Note: The -L option is only used for establishing a local port forward for a FUTURE connection. You can only interact with it by logging 
into it via another ssh session. In this case we logged into T2(192.168.159.132) via ssh on port 4444 root@127.0.0.1 (local host).             
           
           ***SEE Local_Forwarding_illistration.jgp FOR VISUAL AID ***
           
Breakdown:
For the 1st part of ssh line syntax, by default it establishes a local port assignment you can add a local loopback address at this point 
           " ssh root@127.0.0.1 "
           
The 2nd part of the syntax determines what port you are assigning locally [If not specified, by default it will assign 127.0.0.1 (localhost) to the -L 4444 port]
           " -L 127.0.0.1:4444: "

The 3rd Part of the syntax covers target ip and destination port. This portion of the syntax tells the machine where to forward the traffic from the locally bound port. 
In this case we want any traffic that is recieved on the locally bound port of 4444, to be forwarded to the remote IP of 192.168.159.132 on port 22.
           " 192.168.159.132:22 "
           
           -- -L Use cases --
127.0.0.1:80 
google.com:443 
All traffic sent to port 80 HTTP on the localhost is now being sent on port 443 HTTPS on a secure connection.
HTTP data on port 80 is sent using cleartext. Since you are now fowarding port 80 traffic to 443 HTTPS, the
web data is now secured with an encrypted connection. 

127.0.0.1:21
FileserverIP:22
All traffic sent to port 21 FTP on the localhost is now being sent on port 22 SFTP on a secure connection.
FTP does data transfer using cleartext but because you established a secure tunnel, that data is now being sent 
over an encrypted connection.

127.0.0.1:23
LinuxServerIP:22
All traffic sent to port 23 Telnet on the localhost is now being sent on port 22 SSH on a secure connection.
System administrators may need to access devices remotely, but Telnet sends data over the wire using plaintext. 
Now, with this secure connection, any duties they perform over the wire is now encrypted over the secure 
tunnel.  

127.0.0.1:3389
homeIP:22
If your work firewall is set to block 3389 RDP connections, you can set a secure tunnel to foward any connection on the local port 3389 
to run through a secure tunnel to your home computer. Now, not only are you allowed to use the RDP protocol, all the data sent over the 
wire will now be encypted as well.
           
           --Remote Port Forwarding--
T1 <-- T2
T1 192.168.159.134 <--T2 192.168.159.132

           ***SEE Local_Forwarding_illistration.jgp FOR VISUAL AID ***

ssh root@<TargetIP> -R <RemoteIP><Remote Port#>:<LocalIP>:<Localport>

From T1
ssh root@192.168.159.132 -R 0.0.0.0:9999:localhost:22
> From T2 
   >>ssh root@127.0.0.1 -p 9999
     >>Access to T2 from T1 sending connection from T2 back to T1 like a listenern and closed connection

-R Explained:
The -R flag is similar to a local port forward but the directions are reversed. 
When using the flag -R in regards to T1 <- T2 this means that the operator from the current machine is going to establish an
ssh connection to a remote machine and create the beinging of the SSH pipe (the entrance) at the remote machine T2. The ssh pipe 
then would port forward the traffic on the defined remote port back to your local machine and end the ssh pipe at 
T1 (operator's local machine). 

Note: The -R option is used for establishing a port on a remote machine that will connect back to your host machine. From the remote machine you can also 2
interact with the connection. In this case we ssh'd to the remote machine, opened port 9999 that connected back to our host. Then from the remote machine (T1) 
we interacted with the connection via root@127.0.0.1 on port 9999.

Breakdown:
For the 1st part of the ssh line syntax, we ssh to the remote machine 
           " ssh root@192.168.159.132 "
           
For the 2nd part of the ssh line syntax, we establish a designated remote port 9999 [If not specified, by default it will assign 0.0.0.0 (all IPv4 address assigned to the 
remote machine) to the -R 9999 port]
           " -R 0.0.0.0:9999 "
           
For the 3rd part of the ssh line syntax we designate the callback connection to our host machine
           " localhost:22 "
           
           
           --Examples--
RDP over ssh connection throuh designated port
ssh -R 0.0.0.0:8181:localhost:3389 user@IP


           T1             T2   
ssh root@workIP -L 127.0.0.1:4444:homeIP:3389  
So in this example T1 would be the work computer. The firewall allows inbound and outbound ssh connections. The firewall does not allow 3389 (RDP) connections. 
Using the -L options you would assign a port (4444) to forward traffic to your "homeIP" on (3389) RDP. 

             T2                T1
ssh root@webserverIP -R 0.0.0.0:8080:homeIP:22 
So in this example T2 would be the webserver. The firewall allows inbound and outbound ssh connections. The firewall does not allow web traffic over 8080. 
Using the -R options we would assign a port (8080) on T2 to forward traffic back to our "homeIP" on port 80 through a secure connection.    

References:
1.man ssh - man page command for ssh on linux
2.https://www.youtube.com/watch?v=AtuAdk4MwWw&t=302s - A video explaination of ssh tunnels
3.https://www.youtube.com/watch?v=g_Row8zEJZc&t=3s - More a visual explaination of ssh tunnels
4.The Secure Shell by Daniel J. Barrett, Richard E. Silverman, and Robert G. Byrnes   
           -Page 350-355 section 9.2.1 Local Forwarding
           -Page 356 section 9.2.1.2 Remote Forwarding