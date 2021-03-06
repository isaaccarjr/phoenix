![](https://github.com/isaaccarjr/phoenix/blob/master/The-Shadow-Knows.jpg?raw=true)

## BLUF

 
 
## Five W's
- Who: System administrators, developers, people who work remotely  
- What: Using Local/Remote port forwarding to establish an encrypted tunnel, accessing resources over a secure connection, how to use the ssh tunneling mechanism 
- When: Creating an encrypted tunnel to allow certain connections to maneuver through those tunnels 
- Where: Ignoring NAT, Firewall, and routing concepts for implementations. Accessing different servers with needed recources     
- Why: Building a strong foundation of core concepts and expose different utilities of ssh mechanics 

## Local Port Fowarding
#####                                       T1 --> T2
Note: The arrow above (and below in the illustration below) indicates the direction in which the bind_port is being forwarded to. If you are working from your current bind\_address(T1) then the bind_port will forward the connection to the Server\_IP and hostport(T2).


 
```sh
                      $ ssh root@[localhost] -L [bind_address:]bind_port:host:hostport
                              T1 192.168.159.134 --> T2 192.168.159.132
```
From our T1 bind_address:

![](https://github.com/isaaccarjr/phoenix/blob/master/A%20to%20B.PNG?raw=true) 

>Command is  "ssh root@127.0.0.1 -L 127.0.0.1:4444:192.168.159.132:22"

![](https://github.com/isaaccarjr/phoenix/blob/master/A%20to%20B%202.PNG?raw=true)

>ssh root@127.0.0.1 -p 4444
>>Access to T2 

![](https://github.com/isaaccarjr/phoenix/blob/master/Local_Forward_illistration.jpg?raw=true)


-L Explained:  
The -L flags specifies a local port forwarding. This states that the TCP client and SSH client are both on the same "Client" machine.
When using the flag -L in regards to T1 -> T2 this means that the user is creating the start of the SSH pipe (The entrance) 
at T1 (bind_address IP). The pipe will start at T1 and create an ssh connection to a Server machine and a defined hostport. The SSH pipe will end at T2 and the bind_port will forward the traffic starting at T1 and sending it to T2 (Server machine IP) and hostport you have defined. 
           
Note: The -L option is only used for establishing a local port forward for a FUTURE connection. You can only interact with it by logging 
into it via another ssh session. In this case we logged into T2(192.168.159.132) via ssh on port 4444 root@127.0.0.1 (local host).             
           
           
           
Breakdown:
For the 1st part of ssh line syntax, by default it establishes a local port assignment you can add a local loopback address at this point 
           " ssh root@127.0.0.1 "
           
The 2nd part of the syntax determines what port you are assigning locally [If not specified, by default it will assign 127.0.0.1 (bind_address) to the -L 4444 bind_port]
           " -L 127.0.0.1:4444: "

 

The 3rd Part of the syntax covers target ip and destination port. This portion of the syntax tells the machine where to forward the traffic from the locally bound port. 
In this case we want any traffic that is recieved on the locally bound port of 4444, to be forwarded to the remote IP of 192.168.159.132 on port 22.
           " 192.168.159.132:22 "
           
##    -L Use cases 
127.0.0.1:80 
google.com:443 
All traffic being sent to the bind\_port 80 (HTTP) on the bind\_address is now being sent to the remote hostport 443 (HTTPS) on a secure connection. HTTP data on port 80 is sent using cleartext. Since you are now fowarding traffic to the bind\_address port 80 to the remote\_hostport on 443 (HTTPS), the web data is now secured over an encrypted connection. 

 
127.0.0.1:21
FileserverIP:22
All traffic being sent to the bind\_port 21 (FTP) on the bind\_address is now being sent to the remote hostport 22 (SFTP) on a secure connection. FTP completes data transfer using cleartext but because you established a secure tunnel, that data is now being sent over an encrypted connection.

 
127.0.0.1:23
LinuxServerIP:22
All traffic being sent to the bind\_port 23 (Telnet) on the bind\_address is now being sent on the remote hostport 22 (SSH) on a secure connection.System administrators may need to access devices remotely, but Telnet sends data over the wire using plaintext. Now, with this secure connection, any duties they perform over the wire would now be encrypted.  

 
127.0.0.1:3389
homeIP:22
If your work firewall is set to block 3389 RDP connections, you can set a secure tunnel to foward any connections on the bind\_port 3389 to run through a secure tunnel to your home computer on the remote hostport 22. Now, not only are you allowed to use the RDP protocol, all the data sent over the wire will now be encypted as well.

#####                                       T1 <-- T2
Note: The arrow above indicates the direction in which the bind\_port is being forwarded to. If you are working from your current bind\_address (in this case T2) then the bind\_port will forward the connection to the Server_IP and hostport(T2).


 
```sh
                    $ ssh root@[localhost] -L [bind_address:]bind_port:host:hostport
                            T1 192.168.159.134 <-- T2 192.168.159.132
```
From our T2 bind_address:
 
![](https://github.com/isaaccarjr/phoenix/blob/master/B%20to%20A.PNG?raw=true)

>Command is "ssh root@127.0.0.1 -L 127.0.0.1:4444:192.168.159.134:22"

![](https://github.com/isaaccarjr/phoenix/blob/master/A%20to%20B%202.PNG?raw=true)
>ssh root@127.0.0.1 -p 4444
>>Access to T1

The similar concept applies when local port forwarding is done from T2 to T1. In this scenario T2 is the SSH Client and T1 is the SSH Server. The bind_address is set to 4444, so any traffic that calls for 4444 on T2 will be sent to the remote host and hostport. Reaching T1 on IP 192.168.159.134 on port 22.

##           Remote Port Forwarding
-R Explained:
The -R flag is similar to a local port forward but the directions are reversed. When using the flag -R in regards to T1 --< T2 this means that the user from the current client machine machine is going to establish an ssh connection to a Server IP and create the beinnging of the SSH pipe (the entrance) at the Server machine T2. This establishes an Server Listener on the Server machine. Any application server that is being forwarded will then return and make a connection to the ssh client and application client at your own local machine.

T1 --< T2
Note: The Arrow above as well as in the illustration below indicates the direction in which the port is being forwarded. If you are working from your current local host client (T1) then the port will be forwarded from the Server(T2) back to T1

```sh
                               T1 192.168.159.134 --< T2 192.168.159.132
```

![](https://github.com/isaaccarjr/phoenix/blob/master/Remote_Forwarding_illistration.jpg?raw=true)

## Syntax
```sh
                   $ ssh user@remote_host_IP> -R [remote_host_IP]:remote_hostport:bind_address:bind_port
``` 

From T1

![](https://github.com/isaaccarjr/phoenix/blob/master/4.PNG?raw=true)

>Command is "ssh root@192.168.159.132 -R 0.0.0.0:9999:127.0.0.1:22"

Note: "0.0.0.0" indicates that the listining socket on the Remote_Host_Port should listen on all interfaces

From T2 Remote_Host, you can interact with the connection from the terminal:

![](https://github.com/isaaccarjr/phoenix/blob/master/3.PNG?raw=true)

>Command is "ssh root@127.0.0.1 -p 9999

Note: The -R option is used for establishing a port on a remote machine that will connect back to your host machine. From the Remote_Host you can also interact with the connection. In this case we ssh'd to the Remote_Host, opened port 9999 that connected back to our Bind_Address_IP. Then from the Remote_Host (T2) we interacted with the connection via root@127.0.0.1 on port 9999.

Breakdown:
For the 1st part of the ssh line syntax, we ssh to the remote machine 
           " ssh root@192.168.159.132 "
           
For the 2nd part of the ssh line syntax, we establish a designated remote port 9999 [If not specified, by default it will assign 0.0.0.0 (all IPv4 address assigned to the remote machine) to the -R 9999 port]
           " -R 0.0.0.0:9999 "
           
For the 3rd part of the ssh line syntax we designate the callback connection to our Bind_Address_IP machine 
           " localhost:22 "

Now with understanding -R T1 --< T2 there is also the opposite direction as well. With the same concept as setting up a Server listener from a client, we can go also go the opposite direction. T2 >-- T1. With The next Example we will port forward traffic from the Server Listener at 192.168.159.134 back to the client at 192.168.159.132

![](https://github.com/isaaccarjr/phoenix/blob/master/11.PNG?raw=true)

>Command is "ssh root@192.168.159.134 -R 0.0.0.0:9999:127.0.0.1:22"

Now on the Server Listener we can verify this connection with netstat to show we created a listening Server Socket 

![](https://github.com/isaaccarjr/phoenix/blob/master/13.PNG?raw=true)

To interact with this connection we can do the same as before by logging into the connection locally from 192.168.159.134

![](https://github.com/isaaccarjr/phoenix/blob/master/14.PNG?raw=true)

With that concludes 2 separate ways to use the -R flag with similar syntax.
           
           
##          --Example--
RDP over ssh connection throuh designated port
ssh user@workIP -L 127.0.0.1:4444:homeIP:3389  

So in this example user@workIP  would be the work computer (the bind_address_IP). The firewall allows inbound and outbound ssh connections. The firewall does not allow 3389 (RDP) connections to or from work. Although rom our work computer if you needed to access your home machine, you can set up a local Listener that will forward any returning 3389 port traffic back to the work computer over port 4444. Using the -L options you would assign a port (4444) to forward traffic to your "homeIP" on (3389) RDP. 
Likewise for the -R but the direction of the port forward is reversed. Still the firewall at work will not allow RDP traffic over port 3389 either inboud or bound. Using the command "ssh -R 0.0.0.0:8181:localhost:3389 user@IP" from your home computer, you can create an ssh connection to your work computer. Then allowing an assigned port 8181 to call back to your home computer locally on 3389. This will allow 3389 RDP traffic to be forwarded back over to 8181 through the firewall over ssh

ssh root@webserverIP -R 0.0.0.0:8080:homeIP:22 

So in this example T2 would be the webserver. The firewall allows inbound and outbound ssh connections. The firewall does not allow web traffic over 8080. 
Using the -R options we would assign a port (8080) on T2 to forward traffic back to our "homeIP" on port 80 through a secure connection.    

## References:
- https://linux.die.net/man/1/ssh
- man ssh - man page command for ssh on linux
- https://www.youtube.com/watch?v=AtuAdk4MwWw&t=302s - A video explaination of ssh tunnels
- https://www.youtube.com/watch?v=g_Row8zEJZc&t=3s - More a visual explaination of ssh tunnels
- The Secure Shell by Daniel J. Barrett, Richard E. Silverman, and Robert G. Byrnes   
    - Page 350-355 section 9.2.1 Local 
    - Page 356 section 9.2.1.2 Remote Forwarding

## BONUS BONUS BONUS

\>A 192.168.159.134 

![](https://github.com/isaaccarjr/phoenix/blob/master/15.PNG?raw=true)

This is the the first hop locally from your client host port 8787 to B. The bind_port will direct the traffic 
to the server host and server port "192.168.159.132:8787"

\->B 192.168.159.132

![](https://github.com/isaaccarjr/phoenix/blob/master/16.PNG?raw=true)

Now we can open new terminal and interact with the conntection from A to B by logging locally to B. This will then will port forward from B to C at 192.168.159.132 on port 8787 then creating a client listener from B to C at  192.168.159.137 on port 7878

\-->C 192.168.159.137

![](https://github.com/isaaccarjr/phoenix/blob/master/18.PNG?raw=true)

By opening a 3rd terminal and logging in locally, You can then interact with the final target C at 192.168.159.137 all by the client localhost

>ssh root@127.0.0.1 -L 8787:192.168.159.132:22

>>ssh root@127.0.0.1 -p 8787 -L 7878:192.168.159.137:22

>>>ssh jim@127.0.0.1 -p 7878 
