@Library('github.com/releaseworks/jenkinslib') _

pipeline {
    agent any
    environment {
        registry = "${awsid}.dkr.ecr.us-east-1.amazonaws.com/assignment"
    }
    
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/mbibekjana/jenkins_assignment.git']]])
            }
        }
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          def dockerHome = tool 'docker'
          env.PATH = "${dockerHome}/bin:${env.PATH}"
          dockerImage = docker.build registry
        }
      }
    }

    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
        steps{
            script {
                sh 'docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) ${awsid}.dkr.ecr.us-east-1.amazonaws.com/assignment '
                sh 'docker push ${awsid}.dkr.ecr.us-east-1.amazonaws.com/assignment'
            }
        }
    }

    stage('Docker Deploy') {
     steps{
         script {
             sshagent(credentials : ['upgrad']){
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@10.0.2.121 "docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) ${awsid}.dkr.ecr.us-east-1.amazonaws.com/assignment && docker pull ${awsid}.dkr.ecr.us-east-1.amazonaws.com/assignment:latest && (docker ps -f name=node -q | xargs --no-run-if-empty docker container stop) && (docker container ls -a -fname=node -q | xargs -r docker container rm) && docker run -d -p 8081:8081 --rm --name node ${awsid}.dkr.ecr.us-east-1.amazonaws.com/assignment"'

             }
                
                
            }
      }
    }
    }
}
