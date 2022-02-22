pipeline {
  agent any
    tools {
      maven 'maven'
                 jdk 'jdk'
    }
    stages {      
        stage('Build maven ') {
            steps { 
                    sh 'pwd'      
                    sh 'mvn  clean install package'
            }
        }
        
        stage('Copy Artifact') {
           steps { 
                   sh 'pwd'
		   sh 'cp -r target/*.jar docker'
           }
        }
         
        stage('Build docker image') {
           steps {
               script {         
                 def customImage = docker.build('initsixcloud/petclinic', "./docker")
                 docker.withRegistry('https://acr8983.azurecr.io', 'ACR_ID') {
                 customImage.push("${env.BUILD_NUMBER}")
                 }                     
        }
        
        stage('kubernetes') {
           steps {
               script {
                 kubernetesDeploy(
                    configs: 'k8s-deployment.yaml',
                    kubeconfigId: 'AKS_ID',
                    enableConfigSubstitution: true
			)
        }
	  }
    }
}
}
}
}
