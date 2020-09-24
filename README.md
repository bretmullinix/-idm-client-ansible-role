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

    ---
    - name: Register server as IDM client
      hosts: nexus_server
      become: true
      vars:
        yum_installs:
          - name: "python36"
            install_name: "python36"
          - name: "firewalld"
            install_name: "firewalld"
          - name: "nscd"
            install_name: "nscd"
          - name: "@idm:client"
            install_name: "ipa-client"
        yum_backend: dnf
        idm_server_ip_address: 10.10.0.111
        idm_domain_name: example2020.com
        idm_fqdn: "idm.{{ idm_domain_name }}"
        idm_client_hostname: "nexus"
        idm_network_interface_name: "eth0"
        idm_nmcli_interface_name: "System {{ idm_network_interface_name }}"
        idm_admin_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          37616332303435313431313964343732336166366363613864303662653137303266353233383266
          3032303064653162386634376464633264643332336263310a373330363466353036346438396331
          65396363353063653166653237623535323738323232323934666434313934373137633234663230
          6636323861323233650a313863393938643064323461626165646233386235326363356535346238
          3762

      roles:
        - idm-client-ansible-role



The **vars** should contain the values you are using to override the **default/main.yml**
role variables.  Look above for a description of those variables.  Remember to
substitute your IDM Server **admin** password as an encrypted ansible vault
string for the **idm_admin_password** variable.

The playbook runs with a custom **ansible.cfg** with the following values set:

```text
inventory = inventory
remote_user = centos
private_key_file = ./my_keypair
```

The playbook **inventory** file contains the following:

```text
[all]
nexus_server ansible_host=100.25.40.101
```
You will need to change the ip address to your target server ip address.

The ansible playbook command was:  `ansible-playbook create_idm_client.yml --ask-vault-pass`

The **create_idm_client.yml** contains the playbook example code above.

License
-------

BSD

Author Information
------------------

This role was created in 2020 by Bret Mullinix.
