node {
	
    def application = "devopsexample"
    
    
    def dockerhubaccountid = "websh001"
	
    // reference to maven
    // ** NOTE: This 'maven-3.5.2' Maven tool must be configured in the Jenkins Global Configuration.   
    def mvnHome = tool 'maven-3.5.2'

    // holds reference to docker image
    def dockerImage
 
    def dockerImageTag = "${dockerhubaccountid}/${application}:${env.BUILD_NUMBER}"
    
    stage('Clone Repo') { 
      // Get some code from a GitHub repository
      git url:'https://github.com/websterhanie/DevOps-Cert-Training.git',branch:'master' 
      // Get the Maven tool.
      // ** NOTE: This 'maven-3.5.2' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'maven-3.5.2'
    }    
  
    stage('Build Project') {
      // build project via maven
      sh "'${mvnHome}/bin/mvn' clean install"
    }
		
    stage('Build Docker Image with new code') {
      // build docker image
      dockerImage = docker.build("${dockerhubaccountid}/${application}:${env.BUILD_NUMBER}")
    }
	//push image to remote repository , in your jenkins you have to create the global credentials similar to the 'dockerHub' (credential ID)
    stage('Push Image to Remote Repo'){
	 echo "Docker Image Tag Name ---> ${dockerImageTag}"
	     docker.withRegistry('', 'dockerHub') {
             dockerImage.push("${env.BUILD_NUMBER}")
             dockerImage.push("latest")
            }
	}
   
   stage('Remove old running container'){
	   //remove the container which is already running, when running 1st time named container will not be available so we are usign 'True'
	   //added -a option to remove stopped container also
	  sh "docker rm -f \$(docker ps -a -f name=devopsexample -q) || true"   
	       
    }
	
    stage('Deploy Docker New Image'){
	        
	    //start container with the remote image
	  sh "docker run --name devopsexample -d -p 2222:2222 ${dockerhubaccountid}/${application}:${env.BUILD_NUMBER}"  
	  
    }
	
    stage('Remove old images') {
		// remove docker old images
		sh("docker rmi ${dockerhubaccountid}/${application}:latest -f")
   }
}
