Ansible Role: IDM Client (FreeIPA) 
=========

The ansible role is used to install IDM client on a Centos 8 server.  If you have another OS, you will need
to adapt the role for your OS.

Requirements
------------

1.  Centos 8 Operating System
1.  Python Virtual Environment with the following installed:

    1. **ansible==2.9**
 
1. If you intend to use the **molecule** tests, install the following in your Python virtual environment:
    
    1. **molecule==3.0.4**
    1. **molecule[docker]**
    1. **boto**
    1. **boto3**

Role Variables
--------------

1. **defaults/main.yml** variables:

    1. **yum\_installs** -->
    
        ```yaml
        yum_installs:
          - name: "python36"
            install_name: "python36"
          - name: "firewalld"
            install_name: "firewalld"
          - name: "nscd"
            install_name: "nscd"
          - name: "@idm:client"
            install_name:  "ipa-client"
        ```
       
       The variable lists all the required software needed on the Centos 8 target server.  The **name**
       is what **dnf/yum** uses to install the software.  The **install_name** is what the role uses
       to check for the existence of the **dnf/yum** package.
     
     1. **yum\_backend** --> The backend used by the **yum** module.  In the case of Centos 8, the backend
        is **dnf**
        
     1. **idm\_server\_ip\_address** --> The IDM server IP address.  The ansible role uses **nmcli** to
        register this IP address as the DNS server with NetworkManager.
        
     1. **idm\_domain\_name** --> The domain name of the IDM server you are registering the target server in.
     
     1. **idm\_fqdn** --> The FQDN for the IDM server.
     
     1. **idm\_client\_hostname** --> The name of the target server.
     
     1. **idm\_network\_interface\_name** --> The network interface where the target server and the IDM server
        communicate.  Needed to set the DNS server via NetworkManager.
        
     1. **idm\_nmcli\_interface\_name** --> The name of the network connection NMCLI uses.  Needed to set the
         DNS server via Network Manager.
         
     1. **idm\_admin\_password** --> The IDM server **admin** user password.  Needed to register the target
        server with the IDM Server.  This value is encrypted using Ansible Vault.  You will need to encrypt
        your password for your IDM server via **ansible vault** and replace the contents here.
        
1. **vars/main.yml** variables:

    1. **open\_idm\_ports** --> The list of ports to open for the target server to communicate with the IDM server.
    
    1. **freeipa** variables --> The list of variables used by the target server to register as an IDM client.
    

Dependencies
------------

None.

Example Playbook
----------------

    - hosts: servers
      var_files:
        - vars/main.yml
      roles:
         - { role: bmullinix.idm-client }


The **vars/main.yml** should contain the values you are using to override the **default/main.yml**
role variables.  Look above for a description of those variables.

License
-------

BSD

Author Information
------------------

This role was created in 2020 by Bret Mullinix.
