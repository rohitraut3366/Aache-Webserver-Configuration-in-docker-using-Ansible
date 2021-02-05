# Aache-Webserver-Configuration-in-docker-using-Ansible

# [ Ansible Basic ](https://rohitraut3366.medium.com/-9840b4f8bea7)

# [ Docker Installation ](https://rohitraut3366.medium.com/how-to-install-docker-on-centos-8-rhel-8-bc626591410)

# ANSIBLE PLAYBOOK AND AD HOC COMMANDS
Ad-hoc commands can run a single, simple task against a set of hosts in inventory hosts. The real power of ansible understands using playbook we have to configure infrastructure on multiple servers. The playbook is a text file written in YAML format and saved using yml extension. It is easy to read, write and understand.\n
The playbook can consist of multiple plays and each play is an ordered set of tasks run against hosts from the inventory file to reach the desired state of a managed node.

# Description:
```
⭐ Configure yum for docker.
⭐ Install Docker.
⭐ Start and enable Docker services.
⭐ Pull the httpd server image from the Docker Hub.
⭐ Run the docker container and expose it to the public.
⭐ Copy the HTML code and start the webserver.
```
# Pre-requisite:
1. Ansible should be installed in Node and this node is called a Controller Node. for installation refer the above article.
#TO see the version of ansible Installed
```
ansible --version
```
![](https://miro.medium.com/max/700/1*ewodnzDB2G6E74V93iiyYQ.png)

# List of Hosts in the inventory file.
![](https://miro.medium.com/max/700/1*zajLd3bkrQ8sW9x2UWZO_A.png)

# Ansible Configuration File
![](https://miro.medium.com/max/700/1*9b6CyHmsoEqlgkizRK_8kA.png)

# Let's Start
## Configure Yum for docker
```
- hosts: all
  tasks:
      - name: "Configuring yum for docker software"
        yum_repository:
             file: "docker_repo"
             baseurl: "https://download.docker.com/linux/centos/7/x86_64/stable/"                                                                                   
             name: "docker"
             gpgcheck: no
             description: "docker repo"
 ```
## Docker Installation
```
- name: "Docker Installation"
  package:
      name: "docker-ce-18.09.1-3.el7.x86_64"
      state: present
```
## Starting Docker Service
```
- name: "Starting Docker service"
  service:
       name: "docker"
       state: started
       enabled: yes
```
## Installing Python3
```
- name: "Installing Python3"
  package:
      name: "python3"
      state: present
```
## Installing docker-py
To perform docker operation behind the scene Ansible uses docker SDK
To Install Docker SDK:
```
- name: "Installing docker SDK"
  pip:
      name: "docker-py"
      state: present
```
## Creating a WebPage Directory in managed Node
```
- name: "Creating directory In managed Node"
  file:
      path: "/root/webpages"
      state: directory
```
## Copy Webpages to Directory
```
- name: "Copy webpages to directory"
  copy:
     src: "/root/Ansible_playbooks/docker-webserver/webpages/index.html"
     dest: "/root/webpages"
```
## Pulling HTTPD Image From Docker Hub
```
- name: "Pulling HTTPD Docker Container Image"
  docker_image:
      name: "httpd"
      source: pull
      state: present
```
## Adding rules to the firewall
```
- name: "Adding firewall rule"
  firewalld:
      immediate: yes
      permanent: true
      port: "8081/tcp"
      state: enabled
- name: "Masquerade"
  firewalld:
      masquerade: enable
      zone: public
      state: enabled
      permanent: yes
```
## Restart Firewall
```
- name: "firewall restart"
  service:
      name: "firewalld"
      state: restarted
```
## Launching Docker container
```
- name: "Launch httpd container"
  docker_container:
       name: "Webserver"
       image: "httpd"
       ports:
         - "8081:80"
       state: started
       volumes:
          - /root/webpages:/usr/local/apache2/htdocs/
```
