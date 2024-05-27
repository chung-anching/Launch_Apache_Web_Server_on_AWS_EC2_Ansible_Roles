# Launch_Apache_Web_Server_on_AWS_EC2_Ansible_Roles
Launch a Apache Web Server on AWS EC2 using Ansible Roles


### 參考：https://shashi-kant.medium.com/launch-a-apache-web-server-on-aws-ec2-using-ansible-roles-d01483253b3d

$ sudo vi /etc/ansible/ansible.cfg


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


$ ansible-vault create WEB2_vault.yml
放入AWS的Key:
```
accesskey: <your aws access key>
secretkey: <your aws secret key>
```
編輯：
$ ansible-vault edit WEB2_vault.yml


```
---
- name: Launch a Apache Web Server on AWS EC2 using Ansible Roles Medium
  hosts: localhost
  vars_files:
    - WEB2_vault.yml

  tasks:
    - name: "Launching EC2 instance on AWS"
      amazon.aws.ec2_instance:  #ec2:
          name: 05277
          key_name: fuck-0522
          aws_access_key: XXXXXXX
          instance_type: t2.micro
          image_id: "ami-0fe630eb857a6ec83"
          wait: yes
          count: 1
          tags: #instance_tags:
            Name: "myAnsibleOS"
          state: present
          region: "us-east-1"
          vpc_subnet_id: subnet-0cc29a8eaa234d572 	
          security_group: sg-09f00013b0673cd98  # default # group_id: sg-0af08fff9a57d28a5  #"sg-0115c41a0b3c39bda"
          network:
            assign_public_ip: true
          aws_secret_key: "{{ aws_secret_key }}"
      register: ec2

    - name: Pause for 60 seconds to start instance
      ansible.builtin.pause:
        seconds: 60

    - name: "Add new Instance to Host group"    
      ansible.builtin.add_host:  #add_host:
        hostname: "{{ item.public_ip_address }}" #"{{ item.public_ip_address }}" #"{{ item.public_ip }}"
        groupname: webserver
        ansible_user: ec2-user #
        ansible_ssh_host_key_checking: false  #
        ansible_ssh_extra_args: '-o StrictHostKeyChecking=no'
        inventory_dir: '/etc/ansible/hosts'
      with_items: "{{ ec2.instances }}"
      #loop: "{{ ec2.instances }}"

    - name: Pause for 60 seconds to start instance
      ansible.builtin.pause:
        seconds: 60

    - name: "Wait for SSH in Instance"
      wait_for:
        host: "{{ item.public_dns_name }}"
        port: 22
        state: started
      loop: "{{ ec2.instances }}"

- hosts: webserver
  gather_facts: yes
  remote_user: ec2-user
  tasks:
    - name: Running role httpdserver
      ansible.builtin.include_role:  
        name: httpdserver
```

