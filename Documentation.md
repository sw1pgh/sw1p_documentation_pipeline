# DOCUMENTATION for GitHub - Jenkins - Dockerhub - Kubernetes Pipeline (DevOps)
&copy; Swapnaneel Ghosh



**Step 1: "Create a GitHub Repository to upload and store the dockerfile, nodeapp code and deployment.yml file"**

>• GitHub Repository link for the entire node application file along with docker file and deploymentservice.yml : [GitHub_Link](https://github.com/shazforiot/nodeapp_test)

> **_-> Steps needed to add the files into the repository are all Git commands; they are:-_**

> 1. Create a Folder in your PC and give it a name such as 'local-repo'. This will be your local repository.
> 2. Add the files locally to your 'local-repo' folder.
> 3. Open the folder via CMD/Git Bash and initialize Git in it by the following command : 'git init'
> 4. Clone your GitHub repository into your 'local-repo' via the following command : git remote add origin "git@github.com:shazforiot/Jenkins-integration-with-kubernetes.git"
> 5. This key "git@github.com:shazforiot/Jenkins-integration-with-kubernetes.git" is the SSH key added for the repository.
> 6. Add all files by the following command : 'git add .'  
> 7. Get the status of the added files by the command : 'git status'
> 8. Commit all the added files by the command : 'git commit -m "Pipeline Files Added"'
> 9. Push all the files in the GitHub repository by the command : 'git push add origin'
> 10. Refresh your GitHub repository; your repository should be updated with all the added files.



**Step 2: "Download and Install Jenkins; Start it at local port: 8080"**
>• Jenkins Installation Video and initialization Link : [Jenkins tutorial video](https://www.youtube.com/watch?v=0RVbTDLXk50)


> **_• It is very important to manage all the plugins. If the suggested plugins fail to be added during the time of installation of jenkins then go to 'Manage Jenkins' -> 'Manage Plugins' section of the Jenkins and re-add the needed plugins._**

> • Few plugins which must be added are:-
> 1. Maven related all plugins
> 2. Docker related all plugins
> 3. Kubernetes related all plugins
> 4. PIP pipeline related all plugins


> **_• Before beginning any pipeline, it is also very important to Configure the global tools such as Maven and Docker_**

> -> To install Maven; go to 'Manage Jenkins' -> 'Global Tool Configuaration'; then Add Maven -> Install from Apache; Save and exit.
 
> -> To install Docker; go to 'Manage Jenkins' -> 'Global Tool Configuaration'; then Add Docker -> Install Automatically and select Download from Docker.com; Save and exit.

> -> While working with Kubernetes, you have to install a plugin externally. The plugin file for kubernetes can be downloaded from this link : [kubernetes-cd.hpi](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqa3FSX0VkYUxzWVRkZkFrT2ZWVERscENud2U4d3xBQ3Jtc0ttRzlCNVZZZXhOOEp0UkZfZ2MtQTFxLUdyWTZfOXdPR0wzT2FuaTFoc1QtODBLSEhHNVdKcFZXcXJWSUdsQldLdzJmM2FsVElwZmJGdzgwcTFnTFJqSnBXaGktekRNZ0dySnlIR2RKV1htelZnSERkTQ&q=https%3A%2F%2Fupdates.jenkins.io%2Fdownload%2Fplugins%2Fkubernetes-cd%2F1.0.0%2Fkubernetes-cd.hpi&v=XE_mAhxZpwU). To add this plugin go to Manage Jenkins -> Manage Plugins -> Advanced Settings -> Under Deploy Plugin browse and select the downloaded plugin from your local system and click on 'Deploy'.



**Step 3: "Go to [DockerHub](https://hub.docker.com/) and create an account to start your DockerHub journey"**  

>• You will also need to download DockerHub Windows Application to view your local images. You can download it from [DockerHub Desktop Download](https://docs.docker.com/desktop/install/windows-install/)

>• DockerHub Installation and Startup Link: [DockerHub Tutorial](https://www.youtube.com/watch?v=mAzHELZWE-Y)



**Step 4: "Kubernetes Installation and Startup"**

>• [Install kubectl on windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/). Click on Download latest release.

>• Move the downloaded file in a folder; Copy the file path and add the PATH to environment variable as well as system variables.

>**_• Run CMD on that path and apply the following commands step by step:-_**
>1. curl.exe -LO "https://dl.k8s.io/release/v1.26.0/bin/windows/amd64/kubectl.exe"
>2. curl.exe -LO "https://dl.k8s.io/v1.26.0/bin/windows/amd64/kubectl.exe.sha256"
>3. CertUtil -hashfile kubectl.exe SHA256
>4. type kubectl.exe.sha256
>5. kubectl version --client --output=yaml
>6. del kubectl.exe kubectl.exe.sha256


>• [Download Minikube on Windows](https://storage.googleapis.com/minikube/releases/latest/minikube-installer.exe)

>• Install the .exe file in your PC.

>• Copy the path where you installed the file and add the PATH to environment variable as well as system variables.

>• Run CMD on that path.

>**_• To start a minikube cluster run the following commands step by step_**
>1. minikube start --driver=docker (After this command, you can see your cluster started in Docker Desktop App in the container section)
>2. minikube kubectl 
>3. minikube status (To check the status of your cluster)
>4. Let this cmd run continuously in the background


**Step 5: "The whole Git-Jenkins-DockerHub-Kubernetes Pipeline with script"**

>• [Video tutorial](https://www.youtube.com/watch?v=XE_mAhxZpwU)

>• Follow this video step by step

>• Pipeline Script:-

>pipeline {
>
>  environment {
 >   dockerimagename = "sw1pgh/nodeapp"
  >  dockerImage = ""
  >}
>
  >agent any
>
  >stages {
>
   > stage('Checkout Source') {
    >  steps {
     >   git 'https://github.com/shazforiot/nodeapp_test.git'
     > }
    >}
>
> stage('Build image') {
     > steps{
 >       script {
  >        dockerImage = docker.build dockerimagename
  >      }
  >    }
  >  }
>
   > stage('Pushing Image') {
   >   environment {
    >           registryCredential = 'dockerhublogin'
    >       }
 >     steps{
  >      script {
 >         docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
 >           dockerImage.push("latest")
 >         }
 >       }
 >     }
 >   }
>
>    stage('Deploying App to Kubernetes') {
 >     steps {
>        script {
>          kubernetesDeploy(configs: "deploymentservice.yml", kubeconfigId: "kubernetes")
>        }
>      }
>    }
> }
>}