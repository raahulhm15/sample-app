pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/syedbilalafzal/sample-app.git'            }
        }
        stage('Build') {
            steps {
                sh "sudo docker build -t sample-app:latest ."
                sh "sudo docker tag sample-app:latest 323721060456.dkr.ecr.us-east-1.amazonaws.com/sample-app:latest"
                sh "sudo docker push 323721060456.dkr.ecr.us-east-1.amazonaws.com/sample-app:latest"
            }
        }
        stage('deploy to app host') {
            steps
                {
                sh '''
                pwd
                ssh root@app -i /var/lib/jenkins/.ssh/id_rsa_jenkins -o StrictHostKeyChecking=no \
                "[[ ! $(sudo docker ps -a -f    "name=app" --format '{{.Names}}') == app ]] || sudo docker rm app --force"
               
                ssh root@app -i /var/lib/jenkins/.ssh/id_rsa_jenkins \
                "sudo docker pull 323721060456.dkr.ecr.us-east-1.amazonaws.com/sample-app:latest" 
                
                ssh root@app -i /var/lib/jenkins/.ssh/id_rsa_jenkins \
                sudo docker run --name app -d -p 8080:8081 323721060456.dkr.ecr.us-east-1.amazonaws.com/sample-app:latest
                '''
            }

        }        
    }
}
