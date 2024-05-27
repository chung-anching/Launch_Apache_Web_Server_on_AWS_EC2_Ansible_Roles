# Launch_Apache_Web_Server_on_AWS_EC2_Ansible_Roles
Launch a Apache Web Server on AWS EC2 using Ansible Roles


### 參考：https://shashi-kant.medium.com/launch-a-apache-web-server-on-aws-ec2-using-ansible-roles-d01483253b3d

$  vi /etc/ansible/ansible.cfg


```
[defaults]
inventory = /etc/ansible/hosts
host_key_checking = False
remote_user = ec2-user
  
[privilege_escalation]
become = true
become_method = sudo
become_user = root
become_ask_pass = false
```


