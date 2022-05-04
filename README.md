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

## Create playbook
