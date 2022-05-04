# Automated-LAMP-stack-set-up-in-Linux-and-Ubuntu-machines

Here we will build a LAMP server for Linux and Ubuntu OS servers using Ansible, also we add sample website template to those setup. Here we use LAMP services, HTML sample site template and set_fact service as well.

## Prerequisites

- Need 3 Amazon LInux instances, one with ansible installed (Manager) and one with Linux and other with Ubuntu OS.
- A sample HTML site template for EC2 instance, you can use this link for getting [sample HTMl templates](https://www.tooplate.com/).
- Two domain names, to point the Linux and Ubuntu machines public Ip.

## Install ansible on an instance

First install the python on the machine.
```bash
sudo amazon-linux-extras install python3.8 -y
```
then install the ansible using pip3.8

```bash
pip3.8 install ansible
```
to check the ansible version

```bash
ansible --version
```
![image](https://user-images.githubusercontent.com/100775027/166636421-e8f09bde-b510-4961-b779-f1d836fa56ad.png)

## Configure Hosts file (Inventory file)

The orginal Hosts file for Ansible is "/etc/ansible/hosts", but am not using this file as my Hosts file, instead am creating new file with name "inventory". 

```bash
# vim inventory

[linux]
 <your-linux-server-private-ip> ansible_user="ec2-user" ansible_port=22 ansible_ssh_private_key_file="<your-private-key-file-name>"
 
[ubuntu]
 <your-ubuntu-server-private-ip> ansible_user="ec2-user" ansible_port=22 ansible_ssh_private_key_file="<your-private-key-file-name>"
 ```
 The headings in brackets are group names [linux] & [ubuntu], which are used in classifying hosts and deciding what hosts you are controlling at what times and for what purpose. 
 >  Make sure that you add the "your-server-private-ip" and "your-private-key-file-name" of worker instances on the manager server. Change the "your-private-key-file-name" permission to "400" !
  
 For checking SSH connection to worker server/instance:
  
  ```bash
  ansible -i inventory -f 1 linux,ubuntu -m ping
  ```
  ![image](https://user-images.githubusercontent.com/100775027/166636594-f379bfd0-9b8a-4d26-b54f-fdecd320ba69.png)


## Sample template

Here I'm using HTML template for EC2 instance, you can use this link for getting [sample HTML templates](https://www.tooplate.com/). 
To get sample HTML template


```bash
wget https://www.tooplate.com/zip-templates/2121_wave_cafe.zip ; unzip 2121_wave_cafe.zip ; mv 2121_wave_cafe website; rm -rf 2121_wave_cafe.zip
```

## Create playbook

Playbooks are expressed in YAML format with a minimum of syntax. The terms ‘playbook’ and ‘play’ are sports analogies. Each play executes part of the overall goal of the playbook, running one or more tasks. Each task calls an Ansible module.

Here we are going to write the yml file for creating LAMP stack for Linux and Ubuntu:

```bash
vim main.yml
```
then add,

```bash
---

- name: "Installing Apache On Amazon & Ubuntu Os"
  become: true
  hosts: all       
  tasks:
    
    - name: "Amazon - Setting variables"
      when: ansible_os_family == "RedHat" and ansible_distribution == "Amazon"
      set_fact:
        package: httpd
        httpd_user: apache
        httpd_group: apache
        httpd_service: httpd
            
    - name: "Ubuntu - Setting variables"
      when: ansible_os_family == "Debian" and ansible_distribution == "Ubuntu"
      set_fact:
        package: apache2
        httpd_user: www-data
        httpd_group: www-data
        httpd_service: apache2
            
    
    - name: "Amazon - Installing Packages"
      when: ansible_os_family == "RedHat" and ansible_distribution == "Amazon"
      yum:
        name: "{{ package }}"
        state: present
        
    - name: "Amazon - Creating Sample Website"
      when: ansible_os_family == "RedHat" and ansible_distribution == "Amazon"
      copy:
        src: ./website/
        dest: /var/www/html/
        owner: "{{ httpd_user }}"
        group: "{{ httpd_group }}"
            
    - name: "Amazon - Restarting/Enabling Service"
      when: ansible_os_family == "RedHat" and ansible_distribution == "Amazon"
      service:
        name: "{{ httpd_service }}"
        state: restarted
        enabled: true
            
            
    - name: "Ubuntu - Installing Packages"
      when: ansible_os_family == "Debian" and ansible_distribution == "Ubuntu"
      apt:
        name: "{{ package }}"
        state: present
        update_cache: true
            
    - name: "Ubuntu - Creating Sample Website"
      when: ansible_os_family == "Debian" and ansible_distribution == "Ubuntu"
      copy:
        src: ./website/
        dest: /var/www/html/
        owner: "{{ httpd_user }}"
        group: "{{ httpd_group }}"
            
    - name: "Ubuntu - Restarting/Enabling Service"
      when: ansible_os_family == "Debian" and ansible_distribution == "Ubuntu"
      service:
        name: "{{ httpd_service}}"
        state: restarted
        enabled: true
```

 **Check syntax**
 
 Once the playbook is created, we need to check the syntax and execute the playbook:
 
 ```bash
 ansible-playbook -i hosts docker-container --syntax-check
 ```
 ![image](https://user-images.githubusercontent.com/100775027/166636654-f9480cdc-1ee3-4ee3-8383-1f758f539cf6.png)

**Execute the playbook**
 
 ```bash
 ansible-playbook -i hosts docker-container
 ```
 Now our setup is complete and you can point your linux and ubuntu servers public IP to the domain name.

## Conclusion

 This is how we create LAMP server for Linux and Ubuntu OS servers using Ansible. .Please contact me when you encounter any difficulty error while using this terrform code. Thank you and have a great day!

 ### ⚙️ Connect with Me
<p align="center">
<a href="https://www.linkedin.com/in/radin-lawrence-8b3270102/"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white"/></a>
<a href="mailto:radin.lawrence@gmail.com"><img src="https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white"/></a>
