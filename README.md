# Ansible-docker-
image creation and container startup


Image creation using war file which is deployed through Jenking CICD. located on /opt/docker


- hosts: all
  become: root

  tasks:
  - name: create docker image using war
    command: docker build -t simple-dev-image:latest .
    args:
      chdir: /opt/docker

  - name: create tag to image
    command: docker tag simple-dev-image bakkuri/test-image
    ignore_errors: yes

  - name: push images onto dockerhub
    command: docker push bakkuri/test-image


  - name: remove docker images from ansible servers
    command: docker rmi simple-dev-image:latest bakkuri/test-image
    ignore_errors: yes


Container Creation using above image
- hosts: all
  become: root

  tasks:
  - name: stop running containers
    command: docker stop simple-dev-container
    ignore_errors: yes

  - name: delete containers
    command: docker rm simple-dev-container
    ignore_errors: yes

  - name: delete old images
    command: docker rmi bakkuri/simple-dev-image:latest
    ignore_errors: yes

  - name: pull docker image from dockerhub
    command: docker pull bakkuri/test-image:latest

  - name: create container
    command: docker run -d --name simple-dev-container -p 8080:8080 bakkuri/test-image:latest

