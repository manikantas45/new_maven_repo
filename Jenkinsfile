pipeline {
  environment {
    dockerimagename = "coolguy258/my-private-repo"
    registry = "https://hub.docker.com/"
    registryCredential = 'docker-credentials'
    dockerImage = ""	  
	  
    }
	
  agent any 
  stages {
    stage('Cloning Git') {
      steps {
        checkout scm
      }
	}
	
	stage ('Build Package') {

            steps {
               sh 'mvn clean package'
            }
        }
        stage ('sonarqube analaysis') {
            steps {
                 mvn clean verify sonar:sonar \
                 -Dsonar.projectKey=test \
                 -Dsonar.host.url=http://54.193.81.147:9000 \
                 -Dsonar.login=sqp_5b8cef77505d94902b4255bda3da98f096621ae8
            }
        }
    stage('Building image') {
      steps{
        script {
         
         dockerImage = docker.build dockerimagename
        }
      }
    }
    stage('Pushing Image') {
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("${env.BUILD_NUMBER}")
            dockerImage.push("latest")
          }
        }
      }
    }
	 stage('Deploying App to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "k8s-deployment-service.yaml", kubeconfigId: "kubernetes-deployment")
        }
      }
    }
	
  }
} 


