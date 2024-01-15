# Introduction : 
Kerberos is a network authentication protocol designed to provide secure authentication for client-server applications. The primary goal of Kerberos is to enable secure communication over a non-secure network, such as the Internet.

1.The user initiates a service request and forwards a ticket-granting ticket (TGT) request to the Kerberos Authentication Server (AS).

2.The AS validates the user's identity and responds by providing an encrypted TGT.

3.Upon decrypting the TGT, the user submits it to the Ticket-Granting Server (TGS) to obtain a service ticket.

4.The TGS authenticates the TGT and issues a service ticket in return.

5.With the received service ticket in hand, the user transmits it to the Service Server.

6.At this point, the client gains access to the requested service, leveraging the provided service ticket.
![Capture6663](https://github.com/Nourbh17/kerberos2/assets/92574404/92db684c-8bf1-4036-b4ae-996d96623549)

# Environment Preparation and Initial System Configuration :
## 1-Clock synchronization : 
We begin by verifying and synchronizing the clocks of the two machines : 

Clock synchronization is crucial in the context of Kerberos due to how the protocol manages ticket validity periods. Kerberos relies on timestamps to ensure the security and authenticity of exchanges between parties.

![Screenshot 2024-01-14 144614](https://github.com/Nourbh17/kerberos2/assets/92574404/7b35013d-860f-452f-aa41-9cf4c448e348)

![Screenshot 2024-01-14 144632](https://github.com/Nourbh17/kerberos2/assets/92574404/3fbde299-f6e7-4c8f-9cad-bfd30abbf7bf)

## 2-Changing Hostnames : 
We start by executing this command

`hostnamectl --static set-hostname kdc.example.com`

![hostname1](https://github.com/Nourbh17/kerberos2/assets/92574404/06d45841-cc56-4b3e-a3aa-3b6389471dc7)

We do the same for the client machine

![hostname2](https://github.com/Nourbh17/kerberos2/assets/92574404/c1d6b310-053c-485b-80ab-c66dfb79cc66)

## 3-Getting IP Adresses :
Now we need to get the IP Adresses of all the two machines: 

KDC: 192.168.56.102
Client: 192.168.56.101

![ad ip](https://github.com/Nourbh17/kerberos2/assets/92574404/46791893-d9c1-41c0-a98e-fb0796bc54f6)

![ip2](https://github.com/Nourbh17/kerberos2/assets/92574404/ae6c68e1-6d12-4752-a115-54d8e2c4a791)


## 4-Adding IP Adresses in /etc/hosts files

The next step is to add the IP adresses of the three machines in /etc/hosts files. We execute this command :

`sudo gedit /etc/hosts`


![aa](https://github.com/Nourbh17/kerberos2/assets/92574404/da67517c-7939-4a73-983c-a2ed4def43e5)







