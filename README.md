# Jenkins-Master-Agent

## Objective
- Document the process of creating a Jenkins node agent/ slave for a Master Jenkins set-up
- This is to make the set-up of a Jenkins Agent very intuitive.

## Steps

1) Create two EC2 instances on AWS: Jenkins-Master and Jenkins-Agent1
   
   - Jenkins-Master
    ##### On security-group allow SSH, HTTP, and HTTPS
    ##### Also attach the Custom TCP 8080 inbound rule from the Internet

   - Jenkins-Agent1
     ##### On security-group allow SSH, HTTP, and HTTPS

   ![Screen Shot 2023-09-19 at 7 00 27 PM](https://github.com/leoimewore/Jenkins-Master-Agent/assets/95531716/86c44d07-7698-4943-a085-20b4bb32966c)


2) SSH and Install Jenkins on the Jenkins-Master Server
   
   - I used this link to install Jenkins on my master server: https://www.cherryservers.com/blog/how-to-install-jenkins-on-ubuntu-22-04

    ![Screen Shot 2023-09-19 at 7 37 58 PM](https://github.com/leoimewore/Jenkins-Master-Agent/assets/95531716/37f5f456-5df4-4fb4-b8cf-90a7f006ee3b)


3) SSH into Jenkins-Agent1, ensure to install java
   - As this is a requirement for it to connect with the master
   - You should see the code in the link provided above
   ![Screen Shot 2023-09-19 at 7 26 24 PM](https://github.com/leoimewore/Jenkins-Master-Agent/assets/95531716/9c827ca8-ffcf-4dae-8793-f142a71fe796)

4) Any time Jenkins is installed on a Server a user called "jenkins" is created on the Master server
   
    cat /etc/passwd
   
![Screen Shot 2023-09-19 at 7 39 17 PM](https://github.com/leoimewore/Jenkins-Master-Agent/assets/95531716/7a519a38-3a79-4d9b-a6c7-066a23b6b7bc)

  -  Create password for the jenkins user and make jenkins the superuser
    
    sudo passwd jenkins
    su - jenkins


5) Create the ssh public and private key on the jenkins user for the Jenkins Master. This is critical for allowing Master Agent connection

   -  Create ssh key

     ssh-keygen
   
     cd .ssh
   
     ls

   
<img width="392" alt="Screen Shot 2023-09-19 at 7 52 06 PM" src="https://github.com/leoimewore/Jenkins-Master-Agent/assets/95531716/0160c321-775b-44bc-9dd0-c5e68921b684">

6) The next step is to copy the Public key on the Jenkins-Master to the Jenkins Agent
   ### First Step
    - on the Jenkins agent sshd_config file change the Password Authentication to yes and create a password for the root user to allow the ssh-copy from the Jenkins-Master to go through
      
     sudo nano /etc/ssh/sshd_config
    - Restart the server
     sudo systemctl restart sshd
     sudo passwd ubuntu            This is to create a password that will be use when copying ssh pub key from Jenkins Master

   - Finally copy pub key from master to Agent and provide password of the Jenkins Agent ubuntu user

    <img width="776" alt="Screen Shot 2023-09-19 at 8 23 54 PM" src="https://github.com/leoimewore/Jenkins-Master-Agent/assets/95531716/3f402f07-bc09-47e9-97ee-c25ebd836971">


    On Jenkins agent server use linux code
   
   - sudo cat /home/ubuntu/.ssh/authorized_keys

   and you see the public key from the jenkins user on the Jenkins-master
![Screen Shot 2023-09-19 at 8 51 11 PM](https://github.com/leoimewore/Jenkins-Master-Agent/assets/95531716/b9ed303b-f042-4a57-838c-e1e73fdb9035)




7) Now open the Jenkins user interface with "ip-address of Jenkins-Master":8080
   
![Screen Shot 2023-09-19 at 9 11 22 PM](https://github.com/leoimewore/Jenkins-Master-Agent/assets/95531716/784299d2-ddd0-4c58-9569-38a327b2cbea)

![Screen Shot 2023-09-19 at 9 12 52 PM](https://github.com/leoimewore/Jenkins-Master-Agent/assets/95531716/e6cfd221-f360-4d04-83ba-6c813cf0e9ea)

8) Click Manage Jenkins click on Node to create a Jenkins Agent

![Screen Shot 2023-09-19 at 9 14 29 PM](https://github.com/leoimewore/Jenkins-Master-Agent/assets/95531716/eae5b931-3d7c-4962-a789-1afea9a88eee)
   
![Screen Shot 2023-09-19 at 9 16 15 PM](https://github.com/leoimewore/Jenkins-Master-Agent/assets/95531716/26d3f284-34ee-4754-ad4e-7aeac8a68087)


9) Fill up the following
    -Description:  Connect Master to Agent
    -Number of executors: 2
    - remote directory:/home/ubuntu/jenkins   create this directory on the Jenkins agent server
    -Label: dev
    -Launch Method: Launch agents via ssh
    -Host: private IP of the Jenkins-Agent server

Create a Jenkins Credential Provider

kind: SSH Username with private key
Username: ubuntu        user of the Jenkins Agent
Private key: This is the private key of the Jenkins Master Server 
On the Mater server
cd .ssh
cat id_rsa
<img width="660" alt="Screen Shot 2023-09-19 at 9 30 07 PM" src="https://github.com/leoimewore/Jenkins-Master-Agent/assets/95531716/572146b3-5a69-461a-b812-65116b7a965b">

Host Key Verification Strategy: Non verifying Verification Strategy







