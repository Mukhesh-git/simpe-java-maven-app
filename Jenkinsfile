pipeline {
  agent any
  stages {
    stage('cleaning package') {
      steps {
        sh 'mvn clean install package'
      }
    }
    stage('SonarQube analysis') {
    withSonarQubeEnv(credentialsId: '3cd28ea5286481b1a9c35a6951e7c395303cb982', installationName: 'My SonarQube Server') { // You can override the credential to be used
      sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.6.0:sonar'
    }
  }
    stage('building docker image from docker file by tagging') {
      steps {
        sh 'docker build -t phanirudra9/phani9-devops:$BUILD_NUMBER .'
      }   
    }
    stage('logging into docker hub') {
      steps {
        sh 'docker login --username="phanirudra9" --password="9eb876d4@A"'
      }   
    }
    stage('pushing docker image to the docker hub with build number') {
      steps {
        sh 'docker push phanirudra9/phani9-devops:$BUILD_NUMBER'
      }   
    }
    stage('deploying the docker image into EC2 instance and run the container') {
      steps {
        sh 'ansible-playbook deploy.yml --extra-vars="buildNumber=$BUILD_NUMBER"'
      }   
    }  
}
post {
    failure {
        mail to: 'lra9@gmail.com',
             subject: "Failed Pipeline: ${BUILD_NUMBER}",
             body: "Something is wrong with ${env.BUILD_URL}"
    }
     success {
        mail to: 'lprudra9@gmail.com',
             subject: "successful Pipeline:  ${env.BUILD_NUMBER}",
             body: "Your pipeline is success ${env.BUILD_URL}"
    }
}
}
