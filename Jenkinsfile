pipeline {
    agent any
    
    environment {
        // Set your AWS credentials ID configured in Jenkins
        AWS_CREDENTIALS_ID = 'jenkinspipeline'
        // Set your EC2 instance details
        EC2_INSTANCE_IP = '3.26.48.122'
        // Set the Docker image name
        DOCKER_IMAGE_NAME = 'myapp'
    }
    
    stages {
        stage('Build Docker Image') {
            steps {
                // Build the Docker image using the Dockerfile
                script {
                    docker.build("${env.DOCKER_IMAGE_NAME}", "-f Dockerfile .")
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                // Push the Docker image to a Docker registry (optional)
                script {
                    docker.withRegistry('', "${env.AWS_CREDENTIALS_ID}") {
                        docker.image("${env.DOCKER_IMAGE_NAME}").push()
                    }
                }
            }
        }
        
        stage('Deploy to EC2') {
            steps {
                // Use SCP to copy the Docker image to the EC2 instance and run it
                sh "scp -o StrictHostKeyChecking=no ${env.DOCKER_IMAGE_NAME} ec2-user@${env.EC2_INSTANCE_IP}:"
                sh "ssh -o StrictHostKeyChecking=no ec2-user@${env.EC2_INSTANCE_IP} sudo docker stop \$(sudo docker ps -aq)"
                sh "ssh -o StrictHostKeyChecking=no ec2-user@${env.EC2_INSTANCE_IP} sudo docker rm \$(sudo docker ps -aq)"
                sh "ssh -o StrictHostKeyChecking=no ec2-user@${env.EC2_INSTANCE_IP} sudo docker run -d -p 80:80 ${env.DOCKER_IMAGE_NAME}"
            }
        }
    }
    
    post {
        always {
            // Clean up workspace after the pipeline completes
            cleanWs()
        }
    }
}
