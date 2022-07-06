pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
		// Checkout GIT SCM
                git branch: 'main',
                    url: 'https://github.com/syedbilalafzal/sample-app.git'            }
        }
        stage('Build') {
            steps {
		// The below scripts login to the ECR Repo from Jenkins EC2
		// It then build the image from Dockerfile from "Checkout" stage.
		// After that a tag for ECR is created and then pushed to the repo.
		    
                sh " aws ecr get-login-password --region us-east-1 |  docker login --username AWS --password-stdin ${awsid}.dkr.ecr.us-east-1.amazonaws.com"
                sh " docker build -t sample-app:latest ."
                sh " docker tag sample-app:latest ${awsid}.dkr.ecr.us-east-1.amazonaws.com/sample-app:latest"
                sh " docker push ${awsid}.dkr.ecr.us-east-1.amazonaws.com/sample-app:latest"
            }
        }
        stage('deploy to app host') {
            steps
                {
	        // This section Logins to the ECR repository from APP Instance. It check if container is running then closes and removes it to spin up a new one.
		// The below script spins up the container if it is not running
			
                sh '''
                pwd
		ssh -o StrictHostKeyChecking=no -i assignment-c7key.pem ubuntu@10.0.2.84 \
		" aws ecr get-login-password --region us-east-1 |  docker login --username AWS --password-stdin ${awsid}.dkr.ecr.us-east-1.amazonaws.com"
                
		ssh -o StrictHostKeyChecking=no -i assignment-c7key.pem ubuntu@10.0.2.84 \
                " docker rm app --force 2> /dev/null"
               
                ssh -o StrictHostKeyChecking=no -i assignment-c7key.pem ubuntu@10.0.2.84 \
                " docker pull ${awsid}.dkr.ecr.us-east-1.amazonaws.com/sample-app:latest" 
                
                ssh -o StrictHostKeyChecking=no -i assignment-c7key.pem ubuntu@10.0.2.84 \
                " docker run --name app -d -p 8080:8081 ${awsid}.dkr.ecr.us-east-1.amazonaws.com/sample-app:latest"
                '''
            }

        }        
    }
}
