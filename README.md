![image](https://github.com/Nourbh17/kerberos/assets/87276542/0d0b7f9c-357d-43f8-8390-335e927d1a80)# Install and Configure OpenLDAP
## Install slapd and change the instance suffix
to install the server , we run this command :

  `sudo apt install slapd ldap-utils`
  
![image](https://github.com/Nourbh17/kerberos/assets/98901671/990b96e0-b326-4e48-b1b6-2a206ffe1ceb)


to change the Directory Information Tree suffix , we run the following command : 

  `sudo dpkg-reconfigure slapd`    we used dc=example,dc=com

![image](https://github.com/Nourbh17/kerberos/assets/98901671/e3ec1c7a-e249-45e3-ba55-9cd6a9d9a471)

## Create Users and Groups 
We create the following file called add-users.ldif : 



Now to implement the change, we run the below command : 

  `ldapadd -x -D cn=admin,dc=example,dc=com -W -f add-users.ldif `

![image](https://github.com/Nourbh17/kerberos/assets/98901671/b55d0437-b902-430f-9c30-c8c966e926ac)


Users have been added to the server .

Now we do the same thing to add groups to the server :
We create the file called add-groups.ldif file

![image](https://github.com/Nourbh17/kerberos/assets/98901671/4f6b5d40-13cd-45f1-8551-293681f527c4)


we run this command to implement the change : 

  `ldapadd -x -D cn=admin,dc=example,dc=com -W -f add-groups.ldif `
  
![image](https://github.com/Nourbh17/kerberos/assets/98901671/ba9b37a1-83d2-4b61-8f02-341304a8fded)


## Modify Users
To modify a user , we create a file called modify-user.ldif:

![image](https://github.com/Nourbh17/kerberos/assets/98901671/1ffd3630-b021-408e-ab85-61035e64dc57)


To implement the changes , we run the following command : 

  `ldapmodify -x -D cn=admin,dc=example,dc=com -W -f modify-user.ldif`
  
![image](https://github.com/Nourbh17/kerberos/assets/98901671/752dfd05-e2a8-4108-93cc-80a7ea1e666e)


## Certificats
### 1- Generate Self Signed SSL certificates
1-1- First we create the key by running the following command 

  `openssl genrsa -aes128 -out example.com.key 4096 `  
  
![image](https://github.com/Nourbh17/kerberos/assets/98901671/80caf028-5c15-43ac-a974-6f1d8c80b51c)


1-2- Now to remove the passphrase from the generated private key we run 

  `openssl rsa -in example.com.key -out example.com.key`
  
![image](https://github.com/Nourbh17/kerberos/assets/98901671/e61fad10-6d04-4f0e-8271-15fd5c8b51d3)


1-3- Now we generate the request : 

  `openssl req -new -days 3650 -key example.com.key -out example.com.csr`
  
![image](https://github.com/Nourbh17/kerberos/assets/98901671/ab708dbd-ebc7-4409-8703-cb223b52c495)


1-4- finally, we sign the certificate by running this command : 

  `sudo openssl x509 -in example.com.csr -out example.com.crt -req -signkey example.com.key -days 3650`

![image](https://github.com/Nourbh17/kerberos/assets/98901671/99a9827f-ac0a-4f65-99b5-a568f44e185e)


### 2- Configure SSL on openLDAP Server 
2-1- Now let's copy the certificates and key to `/etc/ldap/sasl2` directory : 
We run 

  `sudo cp example.com.{key,crt} /etc/ldap/sasl2/
   sudo cp /etc/ssl/certs/ca-certificates.crt /etc/ldap/sasl2`
   
![image](https://github.com/Nourbh17/kerberos/assets/98901671/b245f724-d024-4055-9546-760f51a032f6)
![image](https://github.com/Nourbh17/kerberos/assets/98901671/7bb7d01c-82d7-44cd-ad28-c0c9b54f7a95)


2-2- Now we change Ownership of the certificate to openldap User . We run this command

  `sudo chown -R openldap:openldap /etc/ldap/sasl2/`

to see the changes we run ` ll /etc/ldap/sasl2/`

![image](https://github.com/Nourbh17/kerberos/assets/98901671/40e77e31-311e-41b4-94c8-39d7c17d48d6)

2-3- We create LDAP configuration file called SSL-LDAP.ldif : 

![image](https://github.com/Nourbh17/kerberos/assets/98901671/685813c7-1fee-4be7-9530-d1778e2524ed)

2-4- To configure LDAP Server to use SSL Certificates , We run :

  `sudo ldapmodify -Y EXTERNAL -H ldapi:/// -f SSL-LDAP.ldif`
  
![image](https://github.com/Nourbh17/kerberos/assets/98901671/fa36a709-86a7-4c8b-850e-c41a1c10563b)

2-5
# Partie 2 : Gestion des Services Réseau avec DNS : 
## What is DNS?
The Domain Name System (DNS) is the phonebook of the Internet. Humans access information online through domain names, like nytimes.com or espn.com. Web browsers interact through Internet Protocol (IP) addresses. DNS translates domain names to IP addresses so browsers can load Internet resources.

Each device connected to the Internet has a unique IP address which other machines use to find the device. DNS servers eliminate the need for humans to memorize IP addresses such as 192.168.1.1 (in IPv4), or more complex newer alphanumeric IP addresses such as 2400:cb00:2048:1::c629:d7a2 (in IPv6).
![image](https://github.com/Nourbh17/kerberos/assets/87276542/4c0dc6ef-e6e3-4171-97e1-9c4247e2113c)

## DNS server configuration:
### install Bind9: 
The reference DNS server, BIND (Berkeley Internet Name Domain), is from the Internet Software Consortium.

`sudo apt-get install bind9`

![image](https://github.com/Nourbh17/kerberos/assets/87276542/1d9957fe-c0e2-4aef-baa1-f01ce18f87ca)

### configuration:
The main configuration of BIND9 is done in the following files:

/etc/bind/named.conf
/etc/bind/named.conf.options
/etc/bind/named.conf.local

![image](https://github.com/Nourbh17/kerberos/assets/87276542/3a105990-42df-4590-93fc-d9d265060a3e)


We will configure a forwarder, which is a DNS server that will handle requests that your server cannot resolve, essentially all requests except those for the zone that we will host for our local machines.

To do this, we will modify named.conf.options with the command:

`sudo nano /etc/bind/named.conf.options`

We uncomment the forwarders block and specify, for example, the IP address of the DNS server provided by the DHCP server of your router. We have set the IP address of a Google DNS like 8.8.8.8 and 8.8.4.4

![image](https://github.com/Nourbh17/kerberos/assets/87276542/d8b98e5f-2b90-4f8d-9567-cb0079d3374f)


To host our own zone as the master server, we will modify the named.conf.local file using the command:

`sudo nano /etc/bind/named.conf.local`

![image](https://github.com/Nourbh17/kerberos/assets/87276542/bc219510-69be-4138-a73d-f24d8048bf6f)



Then, we need to create the file /etc/bind/local.lan to go along with it. To do this, we will copy db.local, which serves as a reference. You type:

`sudo cp /etc/bind/db.local /etc/bind/local.lan`

Edit the file with

`nano /etc/bind/local.lan`
we modify the zone. Here we define the Start Of Authority (SOA). For this, we declare ns (for Name server). We also declare a second name, root, here

After defining the SOA, still ns, we also declare one or more A-type records. An A-type record allows mapping a DNS name to an IP address.

![image](https://github.com/Nourbh17/kerberos/assets/87276542/be6b4326-0bbd-441e-bd69-1b49829e9fe7)

we need to create the file /etc/bind/inv.lan

`sudo cp /etc/bind/db.127 /etc/bind/inv.lan`

Edit the file with

`nano /etc/bind/inv.lan`

![image](https://github.com/Nourbh17/kerberos/assets/87276542/4a4a0ae8-65f3-4c34-aac6-e2ac0d77a05f)

we have also to edit /etc/resolv.conf

`sudo nano /etc/resolv.conf`

![image](https://github.com/Nourbh17/kerberos/assets/87276542/e5f89ea9-4a78-4336-b727-4b41db1cb515)


### Add DNS records for OpenLDAP, Apache and OpenVPN:

we have first to add zones in named.conf.local

![image](https://github.com/Nourbh17/kerberos/assets/87276542/28490b31-dc26-49e2-bb11-496e5ac9a1ee)


then add records in local.lan

![image](https://github.com/Nourbh17/kerberos/assets/87276542/028e0621-6df8-4054-9201-d889fd7ae11e)

and then w create the files apache.lan, ldap.lan and openvpn.lan using

`sudo cp /etc/bind/db.127 /etc/bind/apache.lan`

`sudo cp /etc/bind/db.127 /etc/bind/ldap.lan`

`sudo cp /etc/bind/db.127 /etc/bind/openvpn.lan`

edit them with:

`nano /etc/bind/apache.lan`

![image](https://github.com/Nourbh17/kerberos/assets/87276542/88a795c4-e38f-4a56-a110-085efd2ae942)

`nano /etc/bind/ldap.lan`

![image](https://github.com/Nourbh17/kerberos/assets/87276542/85d10420-5d1a-4808-a8f1-0b7c70773d80)

`nano /etc/bind/openvpn.lan`

![image](https://github.com/Nourbh17/kerberos/assets/87276542/a5c956f3-58c9-453a-a111-b7f3dc0433e2)


## testing
Restart BIND9 with

`sudo service bind9 restart`

From there, on another machine, you should be able to test the resolution of ns.local.lan. On a Windows machine, you can type in a command prompt:

`nslookup`
 
We instruct nslookup to use your Linux server as the DNS server


`server 192.168.56.102`

Now, we try to resolve ns.local.lan

`ns.local.lan`

![image](https://github.com/Nourbh17/kerberos/assets/87276542/d2cbf987-1cb8-45ad-b998-8a88b9e82086)

`apache.local.lan`

![image](https://github.com/Nourbh17/kerberos/assets/87276542/ad438b62-c0fc-48f1-90db-a33dbc2f95d5)

`ldap.local.lan`

![image](https://github.com/Nourbh17/kerberos/assets/87276542/2f359581-1a18-4e2d-a8b2-408fe04ec01a)

`openvpn.local.lan`

![image](https://github.com/Nourbh17/kerberos/assets/87276542/66b9fe8f-84f1-4f7a-a348-ccff71ab49ec)
now in reverse: 
![image](https://github.com/Nourbh17/kerberos/assets/87276542/0688848d-d218-445d-9366-e76977e73a44)



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

we also add the host : 

![Screenshot 2024-01-14 170514](https://github.com/Nourbh17/kerberos2/assets/92574404/ed367fa8-24c9-4e56-aa4d-d1ab3eee3614)

![Screenshot 2024-01-14 180207](https://github.com/Nourbh17/kerberos2/assets/92574404/9ddbe668-b721-4b01-b3d2-b5f93f7321fc)




# SSH Authentication : 

The authentication principle in Kerberos with SSH involves using the Kerberos protocol to establish the identity of users and secure communications over a network. SSH, which stands for Secure Shell, is a secure network protocol that allows secure access to remote machines over an unsecured network. 

### SSH (Secure Shell):

SSH is a secure communication protocol used for encrypted access to remote machines. It is commonly used for remote server administration, secure file transfers, and executing commands on remote machines.
Authentication in SSH can be achieved using various methods, such as password entry, the use of public/private keys, and also through integration with Kerberos.

### Integration of Kerberos with SSH:

When a user wants to connect to a remote machine via SSH, they can use their Kerberos ticket to authenticate without entering an additional password.
The Kerberos ticket serves as proof of the user's identity, eliminating the need to share passwords directly with the remote server.

![ssh_1112](https://github.com/Nourbh17/kerberos/assets/92574404/c572607c-2130-4f01-a4b5-3888d871c501)


## 1-Installation :

![installssh](https://github.com/Nourbh17/kerberos/assets/92574404/9d082a13-99d2-4d97-b210-f576261cc0d8)

## 2-Configuration :

We start by modifying two files: sshd_config and ssh_config, uncommenting these two lines: GSSAPIAuthentication yes and GSSAPICleanupCredentials yes.

"GSSAPIAuthentication yes" enables authentication via the Generic Security Services Application Program Interface (GSSAPI), which is a standard authentication framework used in various systems.

"GSSAPICleanupCredentials yes" specifies that GSSAPI credentials should be cleaned up after the connection. This allows for the release of resources associated with GSSAPI authentication once the connection is terminated.

By uncommenting these lines in the SSH configuration files (sshd_config for the SSH server and ssh_config for the SSH client), we activate GSSAPI authentication. This modification may be necessary in environments where Kerberos ticket-based authentication is used, for example, to enhance SSH access security. It provides an additional authentication method based on the system's security mechanisms, thereby expanding authentication options for users and system administrators.


![ssh](https://github.com/Nourbh17/kerberos/assets/92574404/e9609a98-7110-4068-b803-3d066168287f)

![sshd](https://github.com/Nourbh17/kerberos/assets/92574404/08c2edc4-32ae-4f00-bcd9-b64c0565cec5)

## 3-Authentication
Firstly, verify the list of principals (user and admin) and check the keytab associated with root/admin. 

![principals+ajoutkeytab](https://github.com/Nourbh17/kerberos/assets/92574404/0fae95e6-50ba-492d-a1a5-b329064e6f19)

Create the host and add the keytab for the host.

![add host](https://github.com/Nourbh17/kerberos/assets/92574404/4869010a-23f8-4764-95bd-194ec6791334)

Then, add a user. Initially, when attempting to access kds.example.com, the user need to enter the password 

![faire un user](https://github.com/Nourbh17/kerberos/assets/92574404/7e5c8ac2-8c09-46b8-a6bc-43743a557ddf)


### Goal :  Enable the user to access without entering a password.

Assigning a ticket to this user involves using the `kinit` command to generate a ticket for 'user1.
After generating the ticket, you can use the `klist` command to confirm its presence. 

![generer le ticket tgt](https://github.com/Nourbh17/kerberos/assets/92574404/f7725e34-0759-429c-a1ef-ea11bdc65a7b)

Then, when you attempt to SSH into 'kdc.example.com', the user should be able to access the machine without providing a password.

![le user a bien acceder a cette machine](https://github.com/Nourbh17/kerberos/assets/92574404/59e76768-59b6-4db9-9765-9fbfabdae09e)

# Configure the client machine:

Perform the same steps by installing Kerberos (krb5-user) and configuring it.

Verify the krb5.conf file as shown in the screenshot below:

![Screenshot 2024-01-14 192119](https://github.com/Nourbh17/kerberos/assets/92574404/14fff732-f100-4a11-8a05-3941f8c3463e)

Then, install OpenSSH server and modify the configuration files, similar to what was done on the KDC machine
 
Add a user and attempt to access the machine. Initially, there is no ticket.

Access 'kdc.example.com' (initially with the password). 

Generate a ticket and confirm its generation, as shown in the screenshot:


![Screenshot 2024-01-14 194506](https://github.com/Nourbh17/kerberos/assets/92574404/0facac21-d996-4597-8262-5bc6d8f455d4)

Access the machine  without entering the password, as depicted in the screenshots:

![Screenshot 2024-01-14 194801](https://github.com/Nourbh17/kerberos/assets/92574404/3809f246-7643-4593-afa2-d081ef463089)

On the KDC machine, view the log of the client machine accessing it :

![Screenshot 2024-01-14 194746](https://github.com/Nourbh17/kerberos/assets/92574404/03c5fff1-b531-4b2b-8d80-1d074c81045d)

![Screenshot 2024-01-14 194849](https://github.com/Nourbh17/kerberos/assets/92574404/a3a861d8-d482-4396-a1c7-ead0f0c099f8)

Perform a simple test:

On the KDC machine, echo the hostname into a file. 

Test on the client machine to verify the consistency of results. 

KDC Machine: 

![Screenshot 2024-01-14 195230](https://github.com/Nourbh17/kerberos/assets/92574404/07689e16-062e-4cfe-8e1a-b4beb11ab7da)

Client Machine: 

![Screenshot 2024-01-14 195244](https://github.com/Nourbh17/kerberos/assets/92574404/3ac67ed9-48d2-4f0a-a4a8-5f08692fc51b)
