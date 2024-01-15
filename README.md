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

The next step is to add the IP adresses of the two machines in /etc/hosts files. We execute this command :

`sudo gedit /etc/hosts`


![aa](https://github.com/Nourbh17/kerberos2/assets/92574404/da67517c-7939-4a73-983c-a2ed4def43e5)


now we can test the communication with:

`ping client` 

`ping kdc`

![Screenshot 2024-01-14 144402](https://github.com/Nourbh17/kerberos2/assets/92574404/746ace56-5fbf-4652-9ab3-9e9b7b006d05)

![Screenshot 2024-01-14 144442](https://github.com/Nourbh17/kerberos2/assets/92574404/6d319a83-998d-42eb-b2ee-5ff49d303bdb)




# Installation and Configuration of Kerberos Server :

## 1-Installation :

 We first start exectuing these commands in order to install krb5-kdc, krb5-admin-server and krb5-config libraries needed for this step:

`sudo apt-get update`

`sudo apt-get install krb5-kdc krb5-admin-server krb5-config`

![Screenshot 2024-01-14 150812](https://github.com/Nourbh17/kerberos2/assets/92574404/fe4d17a4-2561-4737-a224-36d83e38b68d)

krb5-kdc : provides the central authentication server for Kerberos.

krb5-admin-server : provides the administration server for the KDC.

krb5-config : provides configuration files and scripts for setting up and managing a Kerberos realm.

![Screenshot 2024-01-14 151327](https://github.com/Nourbh17/kerberos2/assets/92574404/2e20fb00-afe9-452c-849c-18558b4a3a58)

![Screenshot 2024-01-14 151651](https://github.com/Nourbh17/kerberos2/assets/92574404/2c0c17fb-0cc7-46eb-9d9b-d1cb6c3009ad)


## 2-Configuration :
We now execute this command :

`sudo krb5_newrealm`

This command create a new Kerberos realm on a server. It will create new Kerberos database and administration server key, generate a new Kerberos configuration file for the new realm and set up the initial set of administrative principals and policies for the new realm.

When installing the packages, some prompts will appear in order to configure the KDC server

Realm : EXAMPLE.COM (must be in uppercase) 

Kerberos server : kdc.example.com 

Administrative server : kdc.example.com (in our case it's the same as the kdc server)


We find that these files were created : 

![Screenshot 2024-01-14 152044](https://github.com/Nourbh17/kerberos2/assets/92574404/5abcd088-d30e-4c27-9051-0df22d47dfcd) 


stash:

The stash file is often associated with securing the Kerberos database. It's typically a stash file used to securely store the master key of the Kerberos Key Distribution Center (KDC).

In other words, during the Kerberos server configuration, the master key of the KDC is often stored in a stash file. This file needs to be protected as it contains sensitive information.

kadm5.acl:
The kadm5.acl file specifies access rules for administering the Kerberos server. It determines which individuals or principals have the right to perform administrative operations on the Kerberos database. Administrative operations include creating principals, modifying password policies, etc.





# Adding The Principals: 

Here is the list of principals before adding new ones :

![Screenshot 2024-01-14 152738](https://github.com/Nourbh17/kerberos2/assets/92574404/34ee8353-4889-4a5a-9db1-2113838417a8)

We start with the user principal:

`sudo kadmin.local`

`kadmin.local:  add_principal user1`


We can verify that the principal is created

`kadmin.local:  list_principals`

![Screenshot 2024-01-14 153334](https://github.com/Nourbh17/kerberos2/assets/92574404/f2e6b7ce-0376-4f7d-a939-01771a489a1d)

we can verify the details of the principal created : 

![Screenshot 2024-01-14 153635](https://github.com/Nourbh17/kerberos2/assets/92574404/cf0b723f-a514-45b8-a3a4-ddb4c87a4263)

![Screenshot 2024-01-14 153843](https://github.com/Nourbh17/kerberos2/assets/92574404/1188e1e5-642e-40b9-b81a-d011807a5ce4)

![Screenshot 2024-01-14 154246](https://github.com/Nourbh17/kerberos2/assets/92574404/7153e20e-5511-4dc7-b67e-0116e606dbcd)

Now we create the admin principal:

`kadmin.local:  add_principal root/admin`


Next, we need to grant all access rights to the Kerberos database to admin principal root/admin in the configuration file /etc/krb5kdc/kadm5.acl

`sudo nano /etc/krb5kdc/kadm5.acl`

![Screenshot 2024-01-14 152241](https://github.com/Nourbh17/kerberos2/assets/92574404/aeecb2e0-ea5b-47dd-b6ed-7a53c498e90d)


# Keytab Creation : 
To establish authentication between a Kerberos client and server without requiring user interaction, a keytab file is used. This file contains the principal and its associated key, allowing services to authenticate without user input.

## 1-Generate a Keytab File:

Use the ktutil utility to create a keytab file. 

## 2-Verify the algorithm used :

![algo cherché ](https://github.com/Nourbh17/kerberos2/assets/92574404/127fac55-d1eb-495a-8475-63b8dc93875d)

## 3-Add a Principal to the Keytab:
Inside ktutil, assign  a key to root/admin. 

![keytab](https://github.com/Nourbh17/kerberos2/assets/92574404/2b159011-0f0a-4232-9da6-fb4e27fbfcf8)


![Screenshot 2024-01-14 164310](https://github.com/Nourbh17/kerberos2/assets/92574404/81c967e7-f904-4d46-be90-8705dd9a4b6f)

![Screenshot 2024-01-14 170514](https://github.com/Nourbh17/kerberos2/assets/92574404/ed367fa8-24c9-4e56-aa4d-d1ab3eee3614)

![Screenshot 2024-01-14 180207](https://github.com/Nourbh17/kerberos2/assets/92574404/9ddbe668-b721-4b01-b3d2-b5f93f7321fc)


