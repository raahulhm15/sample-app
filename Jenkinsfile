@Library('github.com/releaseworks/jenkinslib') _

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
		// Checkout GIT SCM
                git branch: 'main',
                    url: 'https://github.com/raahulhm15/sample-app.git'         
	    }
        }
        stage('Build') {
            steps {
		// The below scripts login to the ECR Repo from Jenkins EC2
		// It then build the image from Dockerfile from "Checkout" stage.
		// After that a tag for ECR is created and then pushed to the repo.
		    
                sh " aws ecr get-login-password --region us-east-1 |  docker login --username AWS --password-stdin ${awsid}.dkr.ecr.us-east-1.amazonaws.com"
                sh " docker build -t finalprojectecr:latest ."
                sh " docker tag finalprojectecr:latest ${awsid}.dkr.ecr.us-east-1.amazonaws.com/finalprojectecr:latest"
                sh " docker push ${awsid}.dkr.ecr.us-east-1.amazonaws.com/finalprojectecr:latest"
            }
        }
     stage('Docker Run') {
     steps{
         script {
             sshagent(credentials : ['aws_ec2']){
                sh 'ssh -o StrictHostKeyChecking=no -i assignment-c7key.pem ubuntu@10.0.1.87'

             }
                //sh 'ssh -i /login/-i assignment-c7key.pem ubuntu@10.0.2.84'
                sh 'docker run -d -p 8081:8080  node 334982178958.dkr.ecr.us-east-1.amazonaws.com/upgradproject/latest'
            }
       }
    }
}
}
