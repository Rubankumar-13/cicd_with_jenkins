pipeline {
  environment {
    registry = "rubankumarv/cicd_with_jenkins"
    registryCredential = 'DockerHub'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Compile') {
      steps {
        git 'https://github.com/Rubankumar-13/cicd_with_jenkins.git'
        script{
                def mvnHome = tool name: 'MAVEN_HOME', type: 'maven'
                bat "${mvnHome}\\bin\\mvn package"
        }
      }
    }
    stage('Building Docker Image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Push Image To Docker Hub') {
      steps{
        script {
          /* Finally, we'll push the image with two tags:
                   * First, the incremental build number from Jenkins
                   * Second, the 'latest' tag.
                   * Pushing multiple tags is cheap, as all the layers are reused. */
          docker.withRegistry('https://registry.hub.docker.com', 'DockerHub') {
              dockerImage.push("${env.BUILD_NUMBER}")
              dockerImage.push("latest")
          }
        }
      }
    }
    stage('Deploy to Kubernetes'){
        steps{
            sh 'kubectl apply -f deployment.yml'
       }
    }
  }
}
