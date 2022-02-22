pipeline {
    agent any
        environment {
        //once you sign up for Docker hub, use that user_id here
        registry = "acr8983.azurecr.io/mypython-app-may20"
        //- update your credentials ID after creating credentials for connecting to Docker Hub
        registryCredential = 'ACR_ID'
        dockerImage = ''
    }
    stages {

        stage ('checkout') {
            steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url:                        'https://github.com/ajinkya9011/Test.git']]])
            }
        }
       
        stage ('Build docker image') {
            steps {
                script {
                dockerImage = docker.build registry + ":$BUILD_NUMBER"
                //dockerImage = docker.build registry + ":$BUILD_NUMBER"

                }
            }
        }
       
         // Uploading Docker images into Docker Hub
    stage('Upload Image') {
     steps{   
         script {
            docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
            }
        }
      }
    }

    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
   
    stage ('K8S Deploy') {
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
