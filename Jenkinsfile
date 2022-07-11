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
		    
                sh " aws ecr get-login-password --region us-east-1 | sudo docker login --username AWS --password-stdin ${awsid}.dkr.ecr.us-east-1.amazonaws.com"
                sh " sudo docker build -t finalprojectecr:latest ."
                sh " sudo docker tag finalprojectecr:latest ${awsid}.dkr.ecr.us-east-1c.amazonaws.com/finalprojectecr:latest"
                sh " sudo docker push ${awsid}.dkr.ecr.us-east-1.amazonaws.com/finalprojectecr:latest"
            }
        }
     stage('Docker Run') {
     steps{
         script {
             sshagent(credentials : ['AWS']){
                sh 'ssh -o StrictHostKeyChecking=no -i sshkey.pem ubuntu@ip-10-0-2-53'

             }
                //sh 'ssh -i /login/-i sshkey.pem ubuntu@ip-10-0-2-53'
                sh 'docker run -d -p 8081:8080  node 334982178958.dkr.ecr.us-east-1c.amazonaws.com/finalprojectecr:latest'
            }
       }
    }
}
}
