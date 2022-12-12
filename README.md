# Deployment-on-Kubernetes-Cluster-using-Jenkins-CI-CD-
Provision 3 ec2-instances set SG to allow all incoming traffic, for all 3 instances Setup, 1 for jenkins, 1 for ansible and 1 for K8s.
Login to each ec2 instance via putty and run the selected EC2 shell script files.

Install the Jenkins package on one of the servers and copy the public IP:8080 go access the web-page of jenkins, after that add the username and password and login to the dashboard.

It will then ask to install the required plugins, once done go to manage jenkins and install the SSH-Agent plugin, This is the plugin which is used to ssh to ansible by Jenkins. After successful installation, for changes to apply reboot Jenkins. 
Install ansible and docker on ansible server, and configure minikube setup on k8s server. 

Create the Dockerfile and push this on github (manually).

Create a new pipeline, add a repo name, in the configuration check the ‘Git SCM polling ’, add script as 
node {
     	stage('Git checkout') {
              	git branch: 'branch-name', url: 'url-of-repo’ }
}
#Configure webhook as:
Create a github repo and go to its settings part, create a webhook with the jenkins-url:8080/github-webhook/
For API token: Copy the API token and paste this into the GitHub webhook. secret field.Save

Make some changes in the Dockerfile and push it to the separate repo. 

#Define a new task in the configuration section: 
 Select SSH Agent, username, private key id, copy all the data inside key in the select field. Copy the script 
 In the configuration: paste the script, add the public IP 
 Add scp path  (this is the path we passing over scp(secure copy) to the ansible). 
 Save and Build the file. 
 stage(' sending Dockerfile to Ansible server over ssh'){
        sshagent(['jenkins-ansible']) {
            sh 'ssh -tt -o StrictHostKeyChecking=no user@Public-IP'
            sh 'scp -r /var/lib/jenkins/workspace/pipeline-demo/* user@Public-IP:/path/'
        }
 Login to the Dockerhub account, 
Select our Jenkins pipeline, configure and build the docker image by adding the stage block. (add chmod 777 /var/run/docker.sock)
Save. and apply and build
We will use  creditails plugin select text for password plugin, provide secret, copy the generated password , 
Save and apply 
Build: Image has been successfully pushed to docker hub
stage('Pushing Docker images to Docker-hub'){
        sshagent(['jenkins-ansible']){
            withCredentials([string(credentialsId: 'dockerhub_pass', variable: 'dockerhub_pass')]) {
                sh "ssh -tt -o StrictHostKeyChecking=no user@Public-IP 
docker login -u jayshreea0402 -p ${dockerhub-pass}"
                sh 'ssh -tt -o StrictHostKeyChecking=no user@Public-IP
docker image push jayshreea0402/$JOB_NAME:v1.$BUILD_ID' 
                sh 'ssh -tt -o StrictHostKeyChecking=no user@Public-IP
 docker image push jayshreea0402/$JOB_NAME:latest'
}
} 
 Connect to ansible server and the k8s server. Perform key-based SSH from ansible server to the k8s server. 
Add k8s server IP in the /etc/hosts file in ansible server and ping to the k8s server from ansible server to check connectivity. 
On jenkins, create a SSH with private-key copy the generated script.
stage('Copy files from ansible to k8s server’){
     sshagent(['jenkins-ansible']){
            sh 'ssh -tt -o StrictHostKeyChecking=no user@Public-IP
	 sh 'scp -r /var/lib/jenkins/workspace/pipeline-demo/ user@Public-IP:/path/'

 Add the deployment.yaml, service.yaml and ansible playbook files to jenkins pipeline repo 
	stage('K8s deployment using ansible’){
       sshagent(['jenkins-ansible']){
            sh 'ssh -tt -o StrictHostKeyChecking=no user@Public-IP
	 sh 'ssh -tt -o StrictHostKeyChecking=no user@Public-IP
ansible-playbook ansible.yaml. 
Done 

When we push the code on Git0hub an event is triggered by the git host provider. This trigger is known as WebHook. Create a github repo and go to its settings part, create a webhook with the jenkins-url:8080/github-webhook/
  
